在上一节中，当点击All, Active, Completed的时候，把值赋值给了一个路由变量filter。然后，在渲染VisibleTodoList组件的时候，通过其filter属性接受来自params.filter的属性值。

	<VisibleTodoList filter={params.filter || 'all'} />

VisibleTodoList看作是一个容器组件，更明确地说，是一个connected component。那么，**能否把params.filter注入到connected component中去呢**？

<br>

react-router为我们准备了withRouter方法。

<br>

> src/components/VisibleTodoList.js

<br>

	import { withRouter } from 'react-router';
	
	...
	
	const mapStateToProps = (state, { params }) => ({
	  todos: getVisibleTodos(state.todos, params.filter || 'all'),
	});
	
	const VisibleTodoList = withRouter(connect(
	  mapStateToProps,
	  mapDispatchToProps
	)(TodoList));
	
	...
以上，通过把withRouter包裹在connect方法之外后，就可以在connected component中通过prams.filter获取到路由变量filter。

<br>

完整如下：

	import { connect } from 'react-redux';
	import { withRouter } from 'react-router';
	import { toggleTodo } from '../actions';
	import TodoList from './TodoList';
	
	const getVisibleTodos = (todos, filter) => {
	  switch (filter) {
	    case 'all':
	      return todos;
	    case 'completed':
	      return todos.filter(t => t.completed);
	    case 'active':
	      return todos.filter(t => !t.completed);
	    default:
	      throw new Error(`Unknown filter: ${filter}.`);
	  }
	};
	
	const mapStateToProps = (state, { params }) => ({
	  todos: getVisibleTodos(state.todos, params.filter || 'all'),
	});
	
	const mapDispatchToProps = (dispatch) => ({
	  onTodoClick(id) {
	    dispatch(toggleTodo(id));
	  },
	});
	
	const VisibleTodoList = withRouter(connect(
	  mapStateToProps,
	  mapDispatchToProps
	)(TodoList));
	
	export default VisibleTodoList;

<br>

> src/components/App.js

<br>

	import React, { PropTypes } from 'react';
	import Footer from './Footer';
	import AddTodo from './AddTodo';
	import VisibleTodoList from './VisibleTodoList';
	
	const App = () => (
	  <div>
	    <AddTodo />
	    <VisibleTodoList />
	    <Footer />
	  </div>
	);
	
	App.propTypes = {
	  params: PropTypes.shape({
	    filter: PropTypes.string,
	  }),
	};
	
	export default App;

<br>

> localhost:3000

<br>
此时会报错：prams.filter...

<br>
这是因为react-router的版本问题。

<br>

> package.json

<br>
"react-router": "^3.0.0-alpha.1",
<br>

> npm install

<br>
> localhost:3000

<br>





