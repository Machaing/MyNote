## 一、依赖安装

#### 1. React官方脚手架安装

#### 2. 脚手架下添加依赖

(1) yarn add redux

(2) yarn add redux-thunk

(3) yarn add react-redux



## 二、redux-DevTools

#### 1. 下载  

谷歌商店Redux-DevTools 插件

#### 2. 官网

https://github.com/zalmoxisus/redux-devtools-extension

#### 3. 配置

```javascript
 const store = createStore(
   reducer, /* preloadedState, */
+  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
 );
```



## 三、单文件下使用redux

#### 1. 基本依赖

```javascript
import {createStore, combinReducers, compose, applymiddleware} from 'redux'
import thunk from 'redux-thunk'
```

* 当有多个自定义Reducer时,必须引入 **combinReducers** 合并多个Reducer
* 若有异步修改 Store 数据必须引入**compose**、**applymiddleware**、**thunk**



#### 2. 基本代码

##### (1) reducer定义

```javascript
const counterReducer = function(state = { conunt:1}, action) {
  switch(action.type) {
    case 'COUNT_ADD': 
      return {
        ...state, count: state.count + 1
      }
    case 'COUNT_REDUCE': 
      return {
        ...state, count: state.count - 1
      } 
    default:     // 一般默认处理为返回值，且建议设置state的默认值
      return state
  }
}
// reducer2 
const postReducer = function(state = [{ title:'你好'}], action) {
   switch(action.type) {
    case 'LOAD_POSTS
      return {
        ...state, list:action.payload
      }
    default:    
      return state
  }
}
// 根 reducer 
const rootReducer = combinReducers({
  counter: counterReducer,
  post: postReducer
})
```

##### (2) store定义

```javascript
const store = createStore( // createStore是从redux库import进来
  rootReducer,
  compose {
  	applymiddleware (...[thunk]), //中间件数组
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
  },
) 
```

##### (3) 派发

```javascript
// 同步派发:
store.dispatch({
    type:'COUNT_ADD',  
    payload:{},
})

// 异步派发:
store.dispatch(async function(dispatch){
  const res = await getPostRequest() //getPostRequest 为返回promise的API调用封装
  // console.log(res.data)
  dispatch{(
  	type:'LOAD_POST'
    payload: res.data
  )}
}) 
```



## 四、多文件拆分redux模块

#### 1. 目录结构



#### 2. reducers文件夹

##### (1) counter-reducer.js

```javascript
const  = function(state = { conunt:1}, action) {
  switch(action.type) {
    case 'COUNT_ADD': 
      return {
        ...state, count: state.count + 1
      }
    case 'COUNT_REDUCE': 
      return {
        ...state, count: state.count - 1
      } 
    default:     // 一般默认处理为返回值，且建议设置state的默认值
      return state
  }
}

export default counterReducer
```

##### (2) post-reducer.js

```javascript
const counterReducer = function(state = { conunt:1}, action) {
  switch(action.type) {
    case 'COUNT_ADD': 
      return {
        ...state, count: state.count + 1
      }
    case 'COUNT_REDUCE': 
      return {
        ...state, count: state.count - 1
      } 
    default:     // 一般默认处理为返回值，且建议设置state的默认值
      return state
  }
}

export default postReducer
```

##### (3) index.js

```javascript
import {combinReducers} from 'redux'
import counterReducer from './counter-reducer'
import postReducer from './post-reducer'

// 根 reducer 
const rootReducer = combinReducers({
  counter: counterReducer,
  post: postReducer
})

export default rootReducer
```



#### 3. action文件夹

##### (1) counter-action.js

```javascript
export const countAddAction = {
  type: 'COUNT_ADD'
}

export const countReduceAction = {
  type: 'COUNT_REDUCE'
}
```

##### (2) post-action.js

```javascript
import getPost from './xxxxx'

export const loadPostsAction = async (dispatch) =>{
  const res = await getPost()
   dispatch{(
  	type:'LOAD_POST'
    payload: res.data
  )}
}
```



#### 4. /store.js

```javascript
import {createStore, compose, applymiddleware} from 'redux'
import thunk from 'redux-thunk';
import rootReducer from './redux/reducers'

const store = createStore( // createStore是从redux库import进来
  rootReducer,
  compose (
    applymiddleware (...[thunk]), //中间件数组
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
  )
) 

export default store
```

#### 5. index.js or main.js

```react
...
import {Provider} from 'react-redux'
....

ReactDom.render (
  <Provider store={store}>
    <App />
  </Provider>,document.getElementById('root'));
)

```

使用provider后 各个组件的 **props** 就有了 **store 的** **dispatch** 方法，不再需要引入 **store** 方法，可以在组件文件中 console.log(props) 自行对比。 //may be wrong

#### 6. xxx.jsx 组件

```javascript
import { connect } from 'react-redux'
import { loadPostsAction } from './actions/postAction'

componentDidMount(){
  this.props.dispatch(loadPostsAction)
}


const mapStateToProps = (dispatch, ownProps) => {
  return {
    post: state.post
  }
  state, ownProps
}
//通过connect 连接组件和 redux 数据,传递 state 数据和 dispatch 方法
export defalut connect(mapStateToProps){PostList} 
```





## UnkonwCommand

gc  -m ‘split’ codes to differt dirs

gc  -m ‘add react-redux to app’