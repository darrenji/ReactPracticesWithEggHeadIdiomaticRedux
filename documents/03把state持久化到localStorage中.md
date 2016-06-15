**state如何持久化到localStorage中呢？**

<br>

首先，应该有一个与localStorage的写入读出过程。

<br>

> src/localStorage.js

<br>

	export const loadState = () => {
	    try{
	        const serializedState = localStorage.getItem('state');
	        if(serializedState === null){
	            return undefined;
	        }
	        return JSON.parse(serializedState);
	    } catch(err){
	        return undefined;
	    }
	};
	
	export const saveState = (state) => {
	    try{
	        const serializedState = JSON.stringify(state);
	        localStorage.setItem('state', serializedState);
	    } catch(error){
	        
	    }
	};

<br>

接着，在createStore实例化的时候把从localStorage读取出来的数据作为实参传递过去。并且，当reducer每次调用dispatch的时候把当前状态保存到localStorage中去。而做到这样，就需要调用createStore的subscribe方法先注册把状态保存到localStorage的这个事件。

<br>

> src/index.js

<br>

	import 'babel-polyfill';
	import React from 'react';
	import { render } from 'react-dom';
	import { Provider } from 'react-redux';
	import { createStore } from 'redux';
	import todoApp from './reducers';
	import App from './components/App';
	import { loadState, saveState } from './localStorage';
	
	const persistedState = loadState();
	
	const store = createStore(
	    todoApp,
	    persistedState
	);
	
	store.subscribe(() => {
	    saveState(store.getState());
	});
	
	render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  document.getElementById('root')
	);

<br>

> localhost:3000

<br>

发现：不仅把数据的状态缓存了下来，而且还把过滤条件的状态保存下来。

<br>

**显然，我们不想把过滤状态缓存下来。**

<br>

> src/index.js

<br>

	store.subscribe(() => {
	    saveState({
	        todos: store.getState().todos
	    });
	});

<br>

> localhost:3000

<br>

但是，此时再刷新页面，再添加todo项，就会报错：warning.js:44 Warning: flattenChildren(...): Encountered two children with the same key, `0`. Child keys must be unique; when two children share a key, only the first child will be used.

<br>

因为，在Todo组件中key属性值依赖于新添加todo项的id:

	<Todo
	key={todo.id}
	{...todo}
	onClick={() => onTodoClick(todo.id)}
	/>

<br>

添加todo项这个动作时在src/action/index.js中定义的：

	let nextTodoId = 0;
	export const addTodo = (text) => ({
	  type: 'ADD_TODO',
	  id: (nextTodoId++).toString(),
	  text,
	});
以上，也就是说，每次刷新页面，这里的nextTodoId都是从0开始，而这已经有了。

<br>

**如何解决这个冲突呢？**

<br>

> npm install node-uuid --save

<br>

> src/actions/index.js

<br>

	import { v4 } from 'node-uuid';
	
	
	export const addTodo = (text) => ({
	  type: 'ADD_TODO',
	  id: v4(),
	  text,
	});
	
	export const setVisibilityFilter = (filter) => ({
	  type: 'SET_VISIBILITY_FILTER',
	  filter,
	});
	
	export const toggleTodo = (id) => ({
	  type: 'TOGGLE_TODO',
	  id,
	});

<br>

> localhost:3000

<br>

冲突解决。

<br>

最后一个问题：在src/index.js中，保存state的状态可能过于频繁。当前是这样的：

	store.subscribe(() => {
	    saveState({
	        todos: store.getState().todos
	    });
	});

我们希望：最快1秒钟存储一次状态到localStorage中去。

<br>

> src/index.js

<br>
lodash提供了解决方案：

	import 'babel-polyfill';
	import React from 'react';
	import { render } from 'react-dom';
	import { Provider } from 'react-redux';
	import { createStore } from 'redux';
	import todoApp from './reducers';
	import App from './components/App';
	import { loadState, saveState } from './localStorage';
	import throttle from 'lodash/throttle';
	
	const persistedState = loadState();
	
	const store = createStore(
	    todoApp,
	    persistedState
	);
	
	store.subscribe(throttle(() => {
	    saveState({
	        todos: store.getState().todos
	    });
	}, 1000);
	
	render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  document.getElementById('root')
	);

<br>





