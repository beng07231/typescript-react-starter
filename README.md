## WLM-TypeScript-React-Starter

`WLM-TypeScript-React-Starter` 是一个 TypeScript Starter 项目，集成了 [ React + React-Router + Redux + Redux-Thunk ]，旨在为 Web 应用程序开发者提供 “开箱即用” 的 TypeScript 工程，开发者只需下载此项目，根据范例即可编写复杂大型的 React 应用。 

## Install

```bash
$ git clone https://github.com/welearnmore/WLM-TypeScript-React-Starter.git
$ cd WLM-TypeScript-React-Starter
$ yarn
$ npm start
```

直接使用浏览器访问本地文件 `index.html`，即可。（更多命令可查看 package.json 的 scripts 字段）

## Version

编译环境：

- node.js > 8.0
- typescript > 3
- git
- yarn

React 系列：

```bash
"prop-types": "^15.6.2",
"react": "^16.4.2",
"react-dom": "^16.4.2",
"react-redux": "^5.0.7",
"react-router-dom": "^4.3.1",
"redux": "^4.0.0",
"redux-thunk": "^2.3.0"
```

## 目录结构

顶级目录结构如下：

- src 项目源文件
- typings 自定义声明
- dist 编译后的目录

`src` 目录结构如下：

- components 不同页面之间复用的组件（自行创建）
- pages 页面级别的组件
  - [MainPage] 自定义的目录
    - flow redux 相关
    - components 此页面可复用的组件（自行创建）
    - index.tsx 页面入口
    - style.less 样式入口
- store redux store 配置
- index.tsx 入口

## 使用

在 `pages` 中创建一个目录，假设名为 `MainPage` （这是开发者可自己约定的命名），然后创建 `index.tsx`，`style.less`文件和 `flow` 目录（创建 `actions.ts`，`constants.ts`，`mainPageReducers.ts`），【如果你不使用redux，完全可以不创建 `flow` 目录】。

编写入口 `index.tsx`：

```javascript
import * as React from 'react';
import { connect } from 'react-redux';
import { Dispatch } from 'redux';
import { RouteComponentProps } from 'react-router-dom';
import * as actions from './flow/actions';
import { ImainPageStoreState } from './flow/mainPageReducers';
import { IStoreState } from '../../store/configureStore';
import './style.less';

interface ImainPageActionsProps {
  dataSync: () => void;
  dataAsync: (parameter: string) => (dispatch: Dispatch) => void;
}

interface ImainPageState {
  name: string;
}

interface ImainPageProps extends RouteComponentProps<any>, ImainPageActionsProps {
  mainPage: ImainPageStoreState;
}


class MainComponent extends React.Component<ImainPageProps, ImainPageState> {
  constructor(props: ImainPageProps){
    super(props);
    this.state = {
      name: ''
    };
  }

  actionDataSync = () => {
    this.props.dataSync();
  }

  actionDataAsync = () => {
    this.props.dataAsync('icepy');
  }

  setName = () => {
    this.setState({
      name: 'icepy'
    });
  }

  logReactRouterObj = () => {
    console.log(this.props.history);
  }

  render() {
    const { mainPage } = this.props;
    const { syncId, asyncId } = mainPage;
    const { name } = this.state;
    return (
      <div className="container">
        <div className="buttons">
          <button onClick={ this.actionDataSync }> dataSync action </button>
          <button onClick={ this.actionDataAsync }> dataAsync action </button>
          <button onClick={ this.setName }> setState name </button>
          <button onClick={ this.logReactRouterObj }> react-router object </button>
        </div>
        <div className="contents">
          <p>
            syncId: { syncId }
          </p>
          <p>
            asyncId: { asyncId }
          </p>
          <p>
            setState name: { name }
          </p>
          <p>
            react-router object: open Chrome Dev Tool console.log;
          </p>
        </div>
      </div>
    );
  }
}


const mapStateToProps = (state: IStoreState ) => {
  const mainPage: ImainPageStoreState = state.mainPage;
  return {
    mainPage,
  }
}

export const MainPage = connect(mapStateToProps, actions)(MainComponent)
```

编写 `reducers`：

```javascript
import * as CONST from './constants';

export interface ImainPageAction {
  type: string;
  payload: any;
}

export interface ImainPageStoreState {
  syncId: string;
  asyncId: string;
}

const initState: ImainPageStoreState = {
  syncId: '默认值',
  asyncId: '默认值'
};

export function mainPageReducers(state=initState, action: ImainPageAction): ImainPageStoreState{
  const { type, payload } = action;
  switch(type) {
    case CONST.SYNC_DATA:
      return { ...state, syncId: payload.data };
    case CONST.ASYNC_DATA:
      return { ...state, asyncId: payload.data };
    default:
      return { ...state };
  }
}
```

在 `store` 中引入 reducers：

```javascript
import { createStore, applyMiddleware, combineReducers, compose } from 'redux';
import thunk from 'redux-thunk';
import { ImainPageStoreState, mainPageReducers } from '@/pages/MainPage/flow/mainPageReducers';

/* eslint-disable no-underscore-dangle, no-undef */
export interface IStoreState {
  mainPage: ImainPageStoreState;
}

const composeEnhancers = (<any> window) && (<any> window).REDUX_DEVTOOLS_EXTENSION_COMPOSE || compose;
const reducer = combineReducers({
  mainPage: mainPageReducers,
});

export const configureStore = () => createStore(
  reducer,
  composeEnhancers(applyMiddleware(thunk)),
);
```

## LICENSE

GNU LESSER GENERAL PUBLIC LICENSE Version 3, 29 June 2007