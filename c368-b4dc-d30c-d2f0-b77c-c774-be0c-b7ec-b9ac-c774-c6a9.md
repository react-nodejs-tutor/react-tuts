react virtualized를 사용하면, 사용자에게 보여주는 부분만 렌더해주고 나머지 데이터들은 동적으로 렌더해서 성능을 엄청나게 최적화 해줍니다.

dummy 데이터 5천개가 있든 1만개가 있든 상관이 없습니다. 사용자에게 보여지는 부분만 렌더해주기 때문이지요.

[https://github.com/bvaughn/react-virtualized/blob/master/docs/List.md\#rowrenderer](https://github.com/bvaughn/react-virtualized/blob/master/docs/List.md#rowrenderer)

라이브러리는 해당 라이브러리의 사용법을 배우기보다는, 문서를 읽고 적용할 줄 아는 것이 중요합니다.

react-virtualized의 사용법 자체를 공부하시기 보다는, 수업시간에 쓴 코드들이 문서에 어떻게 나와있는지 공부해보시는 것을 추천드려요!

react-virtualized를 체험해보고 싶으시다면 기존 코드를 아래 코드로 교체해주세요.

물론 yarn add react-virtualized를 통해 해당 라이브러리를 먼저 설치해야합니다.

```js
// src/components/TodoList.js

import React, { Component } from 'react';
import './TodoList.css';
import TodoItem from './TodoItem';
import { List } from 'react-virtualized';

class TodoList extends Component {
    render() {
        const { todos, onToggle, onRemove } = this.props;

        const rowRenderer = ({ key, style, index }) => {
            const todo = todos[index];
            return (
                <TodoItem
                    key={key}
                    style={style}
                    todo={todo}
                    onToggle={onToggle}
                    onRemove={onRemove}
                />
            );
        };

        return (
            <div className="TodoList">
                <List
                    rowRenderer={rowRenderer}
                    width={600}
                    height={364}
                    rowCount={todos.length}
                    rowHeight={62}
                />
            </div>
        );
    }
}

export default TodoList;
```

위 코드에서 핵심은 rowRenderer 함수와 List 컴포넌트입니다. 

List 컴포넌트에는 각 row의 높이, 그리고 리스트 전체의 크기를 사전에 정해주고 어떤 컴포넌트를 어떻게 렌더링할지에 대한 함수인 rowRenderer함수를 전달해줍니다.

그리고 아래와 같이 TodoItem.js에서는 기존 jsx를 div로 한번 감싸주고 style을 props로 넣어주세요. style이 갑자기 어디서 나온 것일까요? 그건 List컴포넌트가 rowRenderer함수에게 준 props입니다. 라이브러리는 추상화 되어있기 때문에 다른 프로그래머들이 만들어 놓은대로 우리는 가져다 쓰면 되는것이지 그게 어떻게 만들어져있는지에 대해서는 궁금해할 필요가 없어요. 그래도 만약 궁금하다면 직접 react-virtualized 라이브러리를 열어보면 됩니다. 실제로 열어보면 List가 rowRenderer함수에게 props로 style을 넣어준 것을 확인하실 수 있습니다.

```js
// src/components/TodoItem.js

import React, { Component } from 'react';
import './TodoItem.css';

class TodoItem extends Component {
    shouldComponentUpdate(nextProps, nextState) {
        return this.props.todo !== nextProps.todo;
    }

    render() {
        const { style, todo, onToggle, onRemove } = this.props;
        return (
            <div style={style}>
                <div
                    className={`TodoItem ${todo.checked && 'active'}`}
                    onClick={() => onToggle(todo.id)}
                >
                    <div className="check">&#10004;</div>
                    <div
                        className="remove"
                        onClick={e => {
                            e.stopPropagation();
                            onRemove(todo.id);
                        }}
                    >
                        [지우기]
                    </div>
                    <div className="text">{todo.text}</div>
                </div>
            </div>
        );
    }
}

export default TodoItem;
```

이렇듯 적재적소 어떤 써드파티 라이브러리를 가져다 쓰느냐가 어플리케이션의 퍼포먼스에 큰 영향을 미칠 수 있습니다.

performance탭을 열어서 확인해보세요. 훨씬 속도가 빨라진 것\(약 30ms\)을 확인하실 수 있을거에요.

