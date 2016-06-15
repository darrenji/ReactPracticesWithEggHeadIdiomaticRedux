> src/action/index.js

<br>

这里定义了一些action,原先使用lambda表达式是这样的：

	let nextTodoId = 0;
	export const addTodo = (text) => {
		return {
			type: 'ADD_TODO',
			id: (nextTodoId++).toString(),
			text
		};
	};

	export const setVisibilityFilter = (filter) => {
		return {
			type: 'SET_VISIBILITY_FILTER',
			filter
		};
	};

	...

<br>

现在，使用函数也可以这么写：

	export function addTodo(text){
		return {};
	}

<br>

如果我们还是用lambda表达式，还可以更简洁：

	let nextTodoId = 0;
	export const addTodo = (text) => ({
	  type: 'ADD_TODO',
	  id: (nextTodoId++).toString(),
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

也就是：可以省略掉return关键词，并把{}语句块包裹在()之内。在{}之内的是语句块，在()之内的是表达式。

<br>

> src/components/FilterLink.js

<br>

原来这么写：

	const mapStateToProps = (state, ownProps) => {
		return {
			active: ownProps.filter === state.visibilityFilter
		};
	};

<br>
改成这样：

	const mapStateToProps = (state, ownProps) => ({
		active: ownProps.filter === state.visibilityFilter
	});

<br>

原来这么写：

	const mapDispatchToProps = (dispatch, ownProps) => {
		return {
			onClick: () => {
				dispatch(setVisibilityFilter(ownProps.filter));
			}
		};
	};

<br>
改成这样：

	const mapDispatchToProps = (dispatch, ownProps) => ({
		onClick() {
			dispatch(setVisibilityFilter(ownProps.filter));
		}
	});
以上的onClick的写法也改变了。

<br>






