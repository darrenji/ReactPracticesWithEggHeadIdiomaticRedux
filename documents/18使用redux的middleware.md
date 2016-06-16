redux提供了一个applyMiddleware的对象，可以实现middleware的chain.

<br>

> npm install redux-promise --save

<br>

> npm install redux-logger --save

<br>

> src/configueStore.js

<br>

	import { createStore, applyMiddleware } from 'redux';
	import promise from 'redux-promise';
	import createLogger from 'redux-logger';
	import todoApp from './reducers';
	
	const configureStore = () => {
	  const middlewares = [promise];
	  if (process.env.NODE_ENV !== 'production') {
	    middlewares.push(createLogger());
	  }
	
	  return createStore(
	    todoApp,
	    applyMiddleware(...middlewares)
	  );
	};
	
	export default configureStore;

<br>

