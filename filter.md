지난시간에 이어서 삭제작업을 진행했습니다.

배열에 추가를 할 때는 배열내장함수에서 push가 아닌 concat을 사용했는데, 이는 불변성을 지키기 위함이라고 말씀드렸죠?

삭제 작업도 마찬가지입니다. 이 때는 filter를 사용하면 됩니다.

만약 filter와 같은 불변성을 지켜주는 함수를 쓰지않으면 아래와 같이 삭제작업을 해야합니다.

```js
import React, { Component } from 'react';

class App extends Component {
  state = {
    username: '',
    job: '',
    list: []
  };

  (...중간생략)

  handleRemove = id => {
    const {list} = this.state;
    const index = list.findIndex(item => item.id === id);
    const newList = list.slice();
    newList.splice(index, 1);
    this.setState({
      list: newList
    })
  };

  render() {
    const { username, job, list } = this.state;
    return (
      <div>
        (... 중간생략)
        <ul>
          {list.map(item => (
            <li key={item.id}>
              {item.username}({item.job})
              <button onClick={() => this.handleRemove(item.id)}>
                [삭제]
              </button>
            </li>
          ))}
        </ul>
      </div>
    );
  }
}

export default App;
```

slice와 splice함수 모두 배열에서 특정원소를 원하는 개수만큼 잘라내는 함수인데,

slice는 불변성을 지켜주는 반면 splice는 그러지 못합니다.

slice로 아무것도 자르지않으면 그냥 복사가되고, 복사된 배열에서 지우고싶은 인덱스의 원소를 제거한뒤 새로운 state로 값을 집어넣습니다.

하지만 filter를 사용하면 이러한 작업들을 하지 않아도됩니다.

```js
handleRemove = id => {
    const {list} = this.state;
    this.setState({
        list: list.filter(item => item.id !== id)
    })
};
```

filter도 map함수와 마찬가지로 콜백함수를 인자로 받는데요, 모든 원소를 한번씩 돌리면서 콜백함수의 몸체부분에서 true를 반환하는 원소들만 걸러줍니다.

handleRemove의 경우에는 인자로 받은 id를 가지고있는 원소를 제외한 모든 원소들이 걸러지겠지요?

```js
<button onClick={() => this.handleRemove(item.id)}>
    [삭제]
</button>
```

이번엔 삭제 버튼 부분을 볼까요?

여기를 보면 this.handleRemove\(item.id\)가 아닌 \(\) =&gt; this.handleRemove\(item.id\)로 표현했습니다.

jsx에서는 이벤트핸들러를 등록할때 함수이름만 넣고 호출하지 않는다고 했죠?

카운터 예제에서 onClick={this.handleIncrease} 이렇게 했지 onClick={this.handleIncrease\(\)}이렇게 하지 않았습니다.

그 이유는 무엇이었나요?

this.handleIncrease\(\)이렇게 호출해버리면 컴포넌트가 렌더됨과 동시에 해당 이벤트핸들러가 호출되고 바로 state로 관리하는 number값이 0에서 1로 증가하기 때문에 즉시 해당 컴포넌트가 리렌더되고, 계속해서 그 작업이 반복되기 때문에 무한루프에 빠진다고 했습니다.

handleRemove의 경우도 마찬가지 입니다.

this.handleRemove에 item.id라는 인자를 넣긴 넣어서 전달해주어야 하는데 this.handleRemove\(item.id\)이렇게 해버리면 즉시호출 되겠죠?

따라서 this.handleRemove\(item.id\)를 한번 더 감싸주는 것입니다.

\(\) =&gt; this.handleRemove\(item.id\) 이렇게요.

그러면 버튼이 클릭되면 \(\(\) =&gt; this.handleRemove\(item.id\)\)\(\)가 되면서 this.handleRemove함수에 id가 잘 담겨서 호출됩니다.

