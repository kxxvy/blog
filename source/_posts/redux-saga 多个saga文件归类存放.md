---
title: redux-saga 多个saga文件归类存放
date: 2021-11-18
tags: [redux-saga]
---

类似于 reducer，我们可以用 combineReducers 多个模块的 reducers,

```js
/**reducers文件**/
import { combineReducers } from "redux";
import homeReducer from "./home";
import loginReducer from "./login";

const rootReducer = combineReducers({
  homeReducer,
  homeReducer,
});

export default rootReducer;
```

同样的，redux-saga 依然可以分模块存放于单一的入口文件中。

```js
/**sagas文件**/
// saga模块化引入
import { fork, all } from 'redux-saga/effects'

// 异步逻辑
import { homeSagas } from './signin'
import { loginSagas } from './signout'

// 单一进入点，一次启动所有Saga
export default function* rootSaga() {
  yield all([fork(homeSagas), fork(loginSagas)])
}

export default rootReducer


```

最后，定义 store.js 文件

```js
/**store文件**/
import { createStore, applyMiddleware, compose } from "redux";
import createSagaMiddleware from "redux-saga";
import reducer from "./reducers";
import sagas from "./sagas";
const sagaMiddleware = createSagaMiddleware();
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
  ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({})
  : compose;
const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware));
const store = createStore(reducer, enhancer);

sagaMiddleware.run(sagas);

export default store;
```

至此，我们就可以分模块归置 sagas，这样有助于后期的维护，而不至于所有的 sagas 都全部归置在一起。
