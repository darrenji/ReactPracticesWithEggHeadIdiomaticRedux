本篇设置redux的初始状态。

<br>

> src/index.js

<br>

我们看到createStore实例是这样创建的：

	const store = createStore(todoApp);
createStore接受的实参是reducer。

<br>

我们试着把createStore打印出来：

	import 'babel-polyfill';
	import React from 'react';
	import { render } from 'react-dom';
	import { Provider } from 'react-redux';
	import { createStore } from 'redux';
	import todoApp from './reducers';
	import App from './components/App';
	
	const store = createStore(todoApp);
	console.log(store.getState());
	
	render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  document.getElementById('root')
	);

<br>

> npm install

<br>

> npm start

<br>

> localhost:3000

<br>

	todos:Array[0]
	visibilityFilter:"SHOW_ALL"

<br>

以上，visibilityFilter之所以有初始状态，是因为我们在reducer中定义了。来到src/reducers/visibiltyFilter.js

	const visibilityFilter = (state = 'SHOW_ALL', action) => {
	  switch (action.type) {
	    case 'SET_VISIBILITY_FILTER':
	      return action.filter;
	    default:
	      return state;
	  }
	};
	
	export default visibilityFilter;

<br>

**是否存在其它的方式为todo设置初始状态呢？**

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
	
	const persistedState = {
	    todos: [{
	        id: '0',
	        text:'Welcome back',
	        completed: false
	    }]
	};
	
	const store = createStore(
	    todoApp,
	    persistedState
	);
	console.log(store.getState());
	
	render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  document.getElementById('root')
	);

createStore提供了另外一个方法，可以设置redux的初始状态。

<br>

