在上一节中，createStore的dispatch原本接受action的promise，而我们重新定义了dispatch方法之后，dispatch还可以接受action返回的非promise结果了。

<br>

dispatch的行为可以被不断地包裹，功能不断地被重新定义。这些后加上的叫做middleware。src/configureStore.js可以重构。

> src/configureStore.js

<br>

	import { createStore } from 'redux';
	import todoApp from './reducers';
	
	const logger = (store) => (next) => {
	  /* eslint-disable no-console */
	  if (!console.group) {
	    return next;
	  }
	
	  return (action) => {
	    console.group(action.type);
	    console.log('%c prev state', 'color: gray', store.getState());
	    console.log('%c action', 'color: blue', action);
	    const returnValue = next(action);
	    console.log('%c next state', 'color: green', store.getState());
	    console.groupEnd(action.type);
	    return returnValue;
	  };
	  /* eslint-enable no-console */
	};
	
	const promise = (store) => (next) => (action) => { // eslint-disable-line no-unused-vars
	  if (typeof action.then === 'function') {
	    return action.then(next);
	  }
	  return next(action);
	};
	
	const wrapDispatchWithMiddlewares = (store, middlewares) =>
	  middlewares.slice().reverse().forEach(middleware => {
	    store.dispatch = middleware(store)(store.dispatch); // eslint-disable-line no-param-reassign
	  });
	
	const configureStore = () => {
	  const store = createStore(todoApp);
	  const middlewares = [promise];
	
	  if (process.env.NODE_ENV !== 'production') {
	    middlewares.push(logger);
	  }
	
	  wrapDispatchWithMiddlewares(store, middlewares);
	
	  return store;
	};
	
	export default configureStore;