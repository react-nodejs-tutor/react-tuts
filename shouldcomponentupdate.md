리액트에서는 부모컴포넌트가 리렌더링되면 자식컴포넌트도 리렌더링 된다고 말씀드렸죠?

만약 자식컴포넌트의 내용은 바뀐 것이 전혀 없는데 부모가 리렌더링 되었다는 이유만으로 자식컴포넌트도 리렌더링 된다면 정말 비효율적일 것입니다.

현재 우리가 작업중인 컴폰넌트 구조는 부모가 todolist를 관리하고 자식들이 그 내용을 보여주고 있습니다.

따라서 todolist 배열의 여러 요소들중 한 요소의 checked만 바뀌었으면 그 요소만 리렌더링 해주면 됩니다.

바로 이런 경우에 shouldComponentUpdate를 통해 최적화해 줄 수 있습니다.

```js
// src/components/TodoItem.js

import React, { Component } from 'react';
import './TodoItem.css';

class TodoItem extends Component {
    shouldComponentUpdate(nextProps, nextState) {
    if (this.props.todo !== nextProps.todo) {
        return true;
    } else {
        return false;
    }
    }

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

성능을 보다 정확하게 해보고 싶으시면 개발자도구를 열어 performance탭에서 record기능을 통해 비교해보세요.

App컴포넌트에서 더미 데이터를 여러개 만들고 테스트해보면 shouldComponentUpdate가 주는 성능성 이점을 보다 명확하게 이해하실 수 있어요.

```js
// src/App.js

import React, { Component } from 'react';
import './App.css';

import CreateForm from './components/CreateForm';
import TodoList from './components/TodoList';

const bulkTodos = (() => {
    const todos = [];

    for (let i = 0; i < 5000; i++) {
        todos.push({
            text: `TodoItem ${i}`,
            checked: false,
            id: i
        });
    }

    return todos;
})();

class App extends Component {
    id = 5000;

    state = {
        todos: bulkTodos
    };

    handleInsert = text => {
        const { todos } = this.state;

        this.setState({
            todos: todos.concat({
                text,
                id: this.id++,
                checked: false
            })
        });
    };

    handleToggle = id => {
        const { todos } = this.state;

        this.setState({
            todos: todos.map(todo => {
                if (todo.id === id) {
                    return { ...todo, checked: !todo.checked };
                } else {
                    return todo;
                }
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



