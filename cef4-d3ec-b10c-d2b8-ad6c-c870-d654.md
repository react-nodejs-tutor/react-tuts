**컴포넌트 구조화**

```js
// 컴포넌트 구조화 실습 skeleton

git clone https://github.com/react-nodejs-tutor/component-structurized-layout.git

yarn

yarn start
```

지금까지 배운 jsx문법과 state를 활용해서 정말 간단한 todolist를 만들어 보았어요.

하지만 단일 App컴포넌트에 모든 로직을 몰아서 작성했다는 것이 아쉽습니다.

수업 첫 시간에 컴포넌트는 '재사용 가능한' UI라고 말씀드렸죠?

따라서 기능별로 컴포넌트를 분리해서 적재적소에 사용할줄 알아야 합니다.

이번에는 다음과 같이 컴포넌트를 분리해 볼게요.

![](/assets/csa.png)

폼을 입력하게 해주는 CreateForm컴포넌트, 입력한 항목들을 나열해주는 TodoList, 그리고 개별 항목의 내용이 담긴 TodoItem가 있습니다.

단일 App컴포넌트에 몰아서 작성할 때는 App컴포넌트 자기자신의 mutation을 관리하기 위한 state만 사용했다면,

이제는 컴포넌트들끼리 소통을 해야하기 때문에 props를 사용해야 하고, 어떤 props가 어디서 어디로 내려가야 하는지에 고민해야 합니다.

input state는 당연히 createForm에서 관리하면 됩니다.

그러면 TodoList는 내용들을 보여주는 컴포넌트이므로 todos라는 빈 배열 state는 TodoList에 있어야 할까요?

그렇지 않습니다. 이는 컴포넌트 간의 관계를 따져봐야 해요.

CreateForm컴포넌트는 \(영향을 주기만 하지\) 다른 컴포넌트에게서 영향을 받지 않습니다.

따라서 input이라는 state는 CreateForm컴포넌트에 있으면 됩니다.

하지만 todos배열은 CreateForm 컴포넌트로부터 입력된 내용을 보여주는 것이지요? 즉 영향을 받습니다.

따라서 CreateForm으로부터 입력받은 내용을 파라미터로 전달받아서 todos를 별도의 state로 관리해줘야 합니다.

그렇다면 바로 TodoList에서 CreateForm에서 전달해준 input을 받으면 되는가하면 또 그건 아닙니다.

App.js\(최상단 부모컴포넌트\)를 제외한 다른 컴포넌트들끼리 props를 교환하면 나중에 컴포넌트 갯수가 많아졌을때 구조가 복잡해져요.

따라서 부모를 거쳐서 내려받도록 합니다. \(물론 나중에 리덕스를 배우면 이러한 수고로움도 덜 수 있습니다\)

물론 구현방식에는 개발자마다 의견이 다를 수 있습니다.

\(저는 제 임의대로 개발하는 것이 아닌, 최대한 공식문서 또는 페이스북 팀 또는 리액트 컨퍼런스에서 제안해 주는 방식을 따릅니다\)

```jsx
// 컴포넌트 구조화 실습 최종본

git clone https://github.com/react-nodejs-tutor/component-structurized.git

yarn

yarn start
```

각 이벤트 핸들러 구현은 첫 시간에 구현한 내용과 별 다를 것이 없기 때문에,

각 이벤트 핸들러의 내용이 잘 이해가 되시지 않는다면 1주차 강의자료를 참고해주세요.

> _**이번 컴포넌트 구조화에서 새롭게 추가된 내용이라고하면 handleToggle 부분 \(수업시간에는 handleCheck에 해당합니다\)인데요, **_
>
> _**여기서는 ...이라는 스프레드 연산자가 사용이 되었습니다.**_
>
> _**리액트에서는 불변성 관리를 해주어야 하기때문에 객체 그자체를 변경하면 안되고 반드시 복사를 해줘야한다고 말씀드렸죠?**_
>
> _**객체 불변성 관리의 경우에는 스프레드 연산자를 사용하면 편합니다.**_

스프레드 연산자 사용법은 다음과 같습니다.

```js
const data = [
  {
    id: 0,
    value: true
  },
  {
    id: 1,
    value: false
  }
];

const nextData = data.map(
  o => (o.id === 1)
    ? { ...o, value: !o.value }
    : o
);

// 이렇게 하면 기존 데이터를 수정하지 않으면서 새 값을 지니고있는 새 데이터를 만들 수 있습니다.
// data와 nextData는 서로 다른 주소값을 들고있어요. handleToggle도 스프레드 연산자를 이용하면 쉽게 구현할 수 있습니다.
```

첫 시간에 말씀드렸듯이, 리액트 자체는 쉽습니다. 구조화하고 어떻게 활용하는 것이 어려워요.

_**중요\) 복습을 하실 때는, "왜 컴포넌트를 이런식으로 구조화 했는가"를 고민해보세요.**_

_**어떤 컴포넌트에서 어떤 내용을 state로 관리하고, 어떤 내용을 props로 내려주는지, 그 관계에 주목을 하면서 복습하시기를 추천드립니다.**_

```js
// src/App.js

import React, { Component } from 'react';
import './App.css';

import CreateForm from './components/CreateForm';
import TodoList from './components/TodoList';

class App extends Component {
    id = 1;

    state = {
        todos: []
    };

    handleInsert = text => {
        const { todos } = this.state;
        this.setState({
            todos: todos.concat({
                id: this.id++,
                text,
                checked: false
            })
        });
    };


// 스프레드 연산자 사용
// 우리가 넣어준 id파라미터를 가지고있는 todoitem의 checked값만 반전시켜줍니다.
    handleToggle = id => {
        const { todos } = this.state;
        this.setState({
            todos: todos.map(todo => {
                if (todo.id === id) {
                    return {
                        ...todo,
                        checked: !todo.checked
                    };
                }
                return todo;
            })
        });
    };

    handleRemove = id => {
        const { todos } = this.state;
        this.setState({
            todos: todos.filter(todo => todo.id !== id)
        });
    };

    render() {
        return (
            <div className="App">
                <h3>TODO LIST</h3>
                <CreateForm onInsert={this.handleInsert} />
                <TodoList
                    todos={this.state.todos}
                    onToggle={this.handleToggle}
                    onRemove={this.handleRemove}
                />
            </div>
        );
    }
}

export default App;
```

```js
// src/components/CreateForm.js

import React, { Component } from 'react';
import './CreateForm.css';

class CreateForm extends Component {
    state = {
        input: ''
    };

    handleChange = e => {
        const { value } = e.target;

        this.setState({
            input: value
        });
    };

    handleSubmit = e => {
        e.preventDefault();
        this.props.onInsert(this.state.input);
        this.setState({
            input: ''
        });
    };

    render() {
        const { input } = this.state;
        return (
            <div className="CreateForm">
                <form className="form_container" onSubmit={this.handleSubmit}>
                    <input
                        value={input}
                        placeholder="something to do?"
                        onChange={this.handleChange}
                    />
                    <button type="submit">추가</button>
                </form>
            </div>
        );
    }
}

export default CreateForm;
```

```js
// src/components/TodoList.js

import React, { Component } from 'react';
import './TodoList.css';
import TodoItem from './TodoItem';

class TodoList extends Component {
    render() {
        const { todos, onToggle, onRemove } = this.props;

        return (
            <div className="TodoList">
                {todos.map(todo => {
                    return (
                        <TodoItem
                            todo={todo}
                            onToggle={onToggle}
                            onRemove={onRemove}
                        />
                    );
                })}
            </div>
        );
    }
}

export default TodoList;
```

```js
// src/components/TodoItem.js

import React, { Component } from 'react';
import './TodoItem.css';

class TodoItem extends Component {

    // 아직 shouldComponentUpdate를 안함

    render() {
        const { todo, onToggle, onRemove } = this.props;
        return (
            <div
                className={`TodoItem ${todo.checked && 'active'}`}
                onClick={() => onToggle(todo.id)}
            >
                <div className="check">&#10004;</div>
                <div
                    className="remove"
                    onClick={e => {
                        e.stopPropagation();
                        // e.stopPropagation()을 해주지않으면 onRemove대신 onCheck가 실행됩니다.
                        onRemove(todo.id);
                    }}
                >
                    [지우기]
                </div>
                <div className="text">{todo.text}</div>
            </div>
        );
    }
}

export default TodoItem;
```



