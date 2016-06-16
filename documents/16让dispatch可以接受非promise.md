> src/components/VisibleTodoList.js

<br>

    fetchData(){
        const { filter, receiveTodos } = this.props;
        fetchTodos(filter).then(todos => 
            receiveTodos(filter, todos)                                
        );
    }
获取promis,然后采用receiveTodos方法，是否可以合并成一步呢？
<br>

即写成这样：

	fetchData() {
		const { filter, fetchTodos } = this.props;
		fetchTodos(filter);
	}

这里的fetchTodos方法可以写到action所在的那个文件中去的，即src/actions/index.js中。

<br>

> src/actions/index.js

<br>

	import { v4 } from 'node-uuid';
	import * as api from '../api';
	
	const receiveTodos = (filter, response) => ({
	    type: 'RECEIVE_TODOS',
	    filter,
	    response,
	});
	
	export const fetchTodos = (filter) => 
	    api.fetchTodos(filter).then(response => 
	        receiveTodos(filter, response)
	    );
	
	
	export const addTodo = (text) => ({
	  type: 'ADD_TODO',
	  id: v4(),
	  text,
	});
	
	
	
	export const toggleTodo = (id) => ({
	  type: 'TOGGLE_TODO',
	  id,
	});

这里的fetchTodos这个action和以其它action不一样，其它的action通常返回的是promise，而这里返回的是实实在在的结果。换句话说，createStore的dispatch方法，默认dispatch的action返回的结果是promise,而这里返回的是实实在在的结果。所以，有必要对createStore的dispatch方法重新定义一下。

<br>

> src/configureStore.js

<br>


	import { createStore } from 'redux';
	import todoApp from './reducers';
	
	
	const addLogginToDispatch = (store) => {
	    const rawDispatch = store.dispatch;
	    
	    if(!console.group){
	        return rawDispatch;
	    }
	    
	    return (action) => {
	        console.group(action.type);
	        console.log('%c prev state', 'color:gray', store.getState());
	        console.log('%c action', 'color: blue',  action);
	        const returnValue = rawDispatch(action);
	        console.log('%c next state', 'color: green',store.getState());
	        console.groupEnd(action.type);
	        return returnValue;
	    };
	};
	
	
	const addPromiseSupportToDispatch = (store) => {
	    const rawDispatch = store.dispatch;
	    
	    return (action) => {
	        if(typeof action.then === 'function'){
	            return action.then(rawDispatch);
	        }
	        return rawDispatch(action);
	    };
	};
	
	
	const configureStore = () => {
	
	  const store = createStore(todoApp);
	
	  if(process.env.NODE_ENV !== 'production'){
	      store.dispatch = addLogginToDispatch(store);
	  }
	  
	  store.dispatch = addPromiseSupportToDispatch(store);
	
	  return store;
	};
	
	export default configureStore;

<br>

> src/components/VisibleTodoList.js

<br>

	import React, { Component, PropTypes } from 'react';
	import { connect } from 'react-redux';
	import { withRouter } from 'react-router';
	import * as actions from '../actions';
	import { getVisibleTodos } from '../reducers';
	import TodoList from './TodoList';
	
	class VisibleTodoList extends Component {
	  componentDidMount() {
	    this.fetchData();
	  }
	
	  componentDidUpdate(prevProps) {
	    if (this.props.filter !== prevProps.filter) {
	      this.fetchData();
	    }
	  }
	
	  fetchData() {
	    const { filter, fetchTodos } = this.props;
	    fetchTodos(filter);
	  }
	
	  render() {
	    const { toggleTodo, ...rest } = this.props;
	    return (
	      <TodoList
	        {...rest}
	        onTodoClick={toggleTodo}
	      />
	    );
	  }
	}
	
	VisibleTodoList.propTypes = {
	  filter: PropTypes.oneOf(['all', 'active', 'completed']).isRequired,
	  fetchTodos: PropTypes.func.isRequired,
	  toggleTodo: PropTypes.func.isRequired,
	};
	
	const mapStateToProps = (state, { params }) => {
	  const filter = params.filter || 'all';
	  return {
	    todos: getVisibleTodos(state, filter),
	    filter,
	  };
	};
	
	VisibleTodoList = withRouter(connect(
	  mapStateToProps,
	  actions
	)(VisibleTodoList));
	
	export default VisibleTodoList;

<br>









