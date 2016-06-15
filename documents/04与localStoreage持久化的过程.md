本篇，就把与localStoreage持久化的过程重构一下。

<br>


现在，在src/index.js中有这样一段代码：

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

- 从localStorage中读取数据
- 实例化createStore
- 保存数据到localStorage

<br>

是否可以替换成？

	const store = configureStore();

也就是，把所有的配置写到一个地方去。

<br>

> 在src目录下创建configureStore.js文件

<br>

	import { createStore } from 'redux';
	import throttle from 'lodash.throttle';
	import todoApp from './reducers';
	import { loadState, saveState } from './localStorage';
	
	const configureStore = () => {
	  const persistedState = loadState();
	  const store = createStore(todoApp, persistedState);
	
	  store.subscribe(throttle(() => {
	    saveState({
	      todos: store.getState().todos,
	    });
	  }, 1000));
	
	  return store;
	};
	
	export default configureStore;

<br>

> src/index.js

<br>

	import 'babel-polyfill';
	import React from 'react';
	import { render } from 'react-dom';
	import { Provider } from 'react-redux';
	import App from './components/App';
	
	import configureStore from './configureStore';
	
	const store = configureStore();
	
	
	render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  document.getElementById('root')
	);


<br>
