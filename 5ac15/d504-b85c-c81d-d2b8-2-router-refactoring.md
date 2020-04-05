프로젝트 2에서는 사용자가 웹페이지에 처음 들어가면 componentDidMount에서 풋볼 api를 호출해서 데이터를 보여줬습니다.

그리고 MatchFinder에서 달력에서 새로운 날짜를 선택하거나 헤더메뉴에서 새로운 리그를 선택하면,

그에 맞게 변화된 값을 전부 state로 관리해서 componentDidUpdate를 통해 새로운 api를 호출했습니다.

메뉴같은 간단한 것들은 이렇게 전부 state로 관리해도 상관은 없지만,  어플리케이션의 특성에 따라 얼마든지 다르게 구현할 수 있습니다.

프로젝트2에서 한 것처럼 모든 것을 state로 관리하면 브라우저 히스토리를 사용하지 못합니다.

브라우저 히스토리에 담기지 않으면 사용자가 직접 뒤로가기 앞으로가기를 할 수 없습니다.

반면에 리액트 라우터를 이용하면 히스토리를 사용할 수 있는데요, 이를 이용해서 프로젝트를 조금 바꿔보겠습니다.

> 코드에는 정답이 없습니다.
>
> 어떤걸 사용했는지 참고만 해보시고 더 좋은 방법으로 직접 구현해보세요.
>
> 더 좋은 공부 방법은 구글링해서 계속해서 더 나은 방법을 찾아서 만들어 보는 것입니다.
>
> 그리고 코드가 완성되면, 저나 조교님에게 보내주세요.

리팩토링을 하기 위한 몇가지 가이드를 제시해 드리겠습니다.

**우선 리액트 라우터를 적용해야하므로 리액트라우터를 설치해주고 브라우저 라우터를 불러와주세요.**

App컴포넌트에서 바로 브라우저 라우터를 사용하셔도되고, 루트라는 파일을 따로 만들어서 App을 렌더해주어도 무방합니다.

예를들면 다음과 같이 할 수 있습니다.

```js
// src/Root.js

import React, { Component } from 'react';
import App from './components/App';
import { BrowserRouter, Route } from 'react-router-dom';

class Root extends Component {
  render() {
    return (
      <div>
        <BrowserRouter>
          <Route path="/" component={App} />
        </BrowserRouter>
      </div>
    );
  }
}

export default Root;
```

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import * as serviceWorker from './serviceWorker';
import './styles/main.scss';
import Root from './Root';

ReactDOM.render(<Root />, document.getElementById('root'));
serviceWorker.unregister();
```

**다음으로, 기존에 App컴포넌트에서 관리했던 상태는 모두 날려주세요. 더이상 state를 가지고 있을 필요가 없겠죠?**

**api를 아래와 같이 관리할 것이기 때문에 state없이도 날짜와 리그에 대한 정보를 받아올 수 있기 때문입니다.**

**파라미터는 match, 쿼리는 location을 사용해서 말이죠**

> // api example
>
> localhost:3000/match/premier&from=2019-12-02&to=2020-02-24
>
> localhost:3000/match/laliga&from=2020-01-20&to=2020-02-15
>
> 더 직관적인 api가 있다면 새롭게 만들어보세요!

라우터를 사용하게되면 api가 바뀔때마다 새로운 페이지를 보여주는셈이니 페이지를 하나 만들어주겠습니다

```js
import React, { Component } from 'react';
import qs from 'qs';

import MatchTemplate from '../components/MatchTemplate';
import MatchFinder from '../components/MatchFinder';
import Match from '../components/Match';

class MatchPage extends Component {
  render() {
    const { match, location } = this.props;

    const { leagueName } = match.params;
    const range = qs.parse(location.search.substr(1));

    return (
      <MatchTemplate header={<MatchFinder />}>
        <Match range={range} leagueName={leagueName} />
      </MatchTemplate>
    );
  }
}

export default MatchPage;
```

이렇게 하면 **페이지에서는 match와 location을 사용해서 날짜와 리그종류를 받아올 수 있겠죠?**

다음은 App을 볼까요?

```js
// src/components/App.js

import React, { Component } from 'react';
import MatchPage from '../pages/MatchPage';
import { Switch, Route, Redirect } from 'react-router-dom';

class App extends Component {
  render() {
    return (
      <div>
        <Switch>
          <Route exact path="/">
            <Redirect to="/match/premier?from=2020-02-01&to=2020-03-01" />
          </Route>
          <Route path="/match/:leagueName" component={MatchPage} />
          <Route render={() => <div>wrong url</div>} />
        </Switch>
      </div>
    );
  }
}

export default App;
```

여기서는 Redirect컴포넌트를 사용했습니다.

**Redirect컴포넌트를 Route컴포넌트의 children으로 넣어주면 해당 주소로 들어갔을때 리디렉션을 해줍니다.**

이 경우에는 default value인 프리미어리그를 보여주게 했습니다.

조금 조잡하다구요? 아예 다른 컴포넌트로 빼주셔도 됩니다.

```js
<Route exact path="/" component={Redirection} />
```

이렇게 Redirection컴포넌트를 따로 만들어도 됩니다.

```js
// src/common/Redirection.js

import React, { Component } import React, { Component } from 'react';
import { withRouter, Redirect } from 'react-router-dom';

class Redirection extends Component {
  render() {
    const { pathname } = this.props.location;

    return (
      <div>
        {pathname === '/' && (
          <Redirect to="/match/premier?from=2020-02-01&to=2020-03-01" />
        )}
      </div>
    );
  }
}

export default withRouter(Redirection);
```

**여기서는 withRouter라는걸 사용했는데**, withRouter를 사용하면 Route컴포넌트를 통해 render된 컴포넌트가 아니더라도 match, location, history등의 props들을 사용할 수 있습니다.

리덕스를 다룰때 배운 connect처럼 일종의 HOC이므로 Redirection컴포넌트를 감싸기만 하면 해당 기능들이 부여됩니다.

이렇게 관리하는 것도 싫으면 state로 빼서 관리해도 됩니다.

재사용성에 초점을 맞춰서 본인이 관리하고 싶은대로 관리하면 됩니다.

이제 MatchList에서는 바뀐 url을 받아와서 그대로 쿼리만 날려주면 되겠죠?

```js
// LeagueList.js

...

export const leagueTypes = [
  {
    league: 'premier',
    league_name: '프리미어 리그',
    league_id: 148
  },
  {
    league: 'laliga',
    league_name: '라리가',
    league_id: 468
  },
  {
    league: 'serie',
    league_name: '세리에 A',
    league_id: 262
  },
  {
    league: 'bundes',
    league_name: '분데스리가',
    league_id: 196
  },
  {
    league: 'legue',
    league_name: '리그 앙',
    league_id: 176
  },
  {
    league: 'champs',
    league_name: '챔피언스리그',
    league_id: 149
  }
];

...
```

```js
// src/utils/leaugeIdMapper.js

import { leagueTypes } from '../components/MatchFinder/League/LeagueList';

export default function leagueIdMapper(leagueName) {
  const leagueMaps = [...leagueTypes];
  const { league_id } = leagueMaps.find(l => l.league === leagueName);
  return league_id;
}
```

이런식으로 유틸함수를 만들어주면 다른 컴포넌트에서 url에 맞는 정보를 빼올 수도 있습니다.

```js
// src/components/Match/MatchList.js

...

    const { from, to } = this.props.range;
    const { leagueName } = this.props;
    const leagueId = leagueIdMapper(leagueName);

    const query = `&from=${from}&to=${to}&league_id=${leagueId}`;

...
```

**마지막으로 달력 컴포넌트에서는 history객체를 사용했는데요, history.push를 사용하면 다른 url로 이동할 수 있습니다.**

\(history를 사용하기 위해서는 Calendar컴포넌트 역시 withRouter로 감싸주어야 합니다\)

```js
// src/components/MatchFinder/Calendar/Calendar.js


...

  handleSubmit = e => {
    e.preventDefault();

    const { date } = this.state;
    const from = dateFormatter(date[0]);
    const to = dateFormatter(date[1]);
    const { history, match } = this.props;

    history.push(`${match.url}?from=${from}&to=${to}`);
  };

...
```

history와 redirection의 차이점은 리디렉션 같은경우는 히스토리 브라우저 스택에 쌓이지 않습니다.

굳이 되돌아가기를 해서 이전 페이지로 돌아갈 필요가 없는 경우에는 리디렉션을 사용하면 됩니다.

직접 리팩토링을 해보고 새로운 프로젝트를 만들어보면 리액트 라우터라는 라이브러리 하나에도 다양한 많은 기능들을 새로 공부할 수 있습니다.

그런 의미에서 과제를 하나 더 내드릴게요.

```js
// src/components/App.js

...

class App extends Component {
  render() {
    return (
      <div>
        <Switch>
          <Route exact path="/" component={Redirection}/>
          <Route path="/match/:leagueName" component={MatchPage} />
          <Route render={() => <div>wrong url</div>} />
        </Switch>
      </div>
    );
  }
}

export default App;
```

위 라우팅 방식에서

```js
<Route path="/match/:leagueName" component={MatchPage} />
```

이 부분이 영 마음에 들지 않습니다. 서브라우트로 구현해보는건 어떨까요? 마지막으로 전체적인 프로젝트 구조를 조금 더 이쁘게 다듬어보세요.

