---
title: 從琅琊榜學 Redux
tags: React
category: Frontend
excerpt: 從琅琊榜學 Redux
created: 2019-03-20
image: ./images/marco-marques-dJ_Zl5LpPto-unsplash.jpg
image_caption: Photo by Marco Marques on Unsplash
author: author1
---

### 前言
這篇文章希望從另外一個角度來看關於 Redux 的機制與運用，在網路上各式的教學文章中值得先推薦的自然是官方教學
如果英文不是很好的可以參考繁體中文版。這是小弟認為要學習 Redux 必讀的文件。另外還有A cartoon intro to redux也是值得一讀的好文可以加深記憶。

接著就讓我們開始吧，當然最後如果有任何錯誤，也歡迎您的指教。

### 一個中國的古代智慧
先別管 Flux, Redux,React 等術語了，在我們深入任何程式碼與實作之前讓我們先來聊一下關於中國的古裝宮廷劇的橋段。雖然連續劇上演的可能並不是古代真實的狀況，但就讓我們假設這樣的過程是對的。就讓我們從當紅電視劇<琅琊榜>來聊起，相信你我都知道這樣的流程。當梁皇大大決定了要加封蕭景琰從郡王變成親王，這個時候皇上要做的就是下旨，下旨之後接著就需要一份實體的聖旨或稱詔書來完成整個流程。這份聖旨當然不是任何人都能撰寫的，它通常需要由內閣按照一定格式完成。聖旨完成之後，接著就需要太監們去負責傳旨這個流程，太監總管自然是可以差遣底下的太監去
正式的宣旨，在這過程中我們合理的推測太監總管是會通知像是禮部這類單位去準備親王穿的新衣服等等。好了，我們已經講完了關於單一資料流與 Redux 了！？

### 角色與其職
上面的沒頭沒腦地說明讓你開始想關掉這篇文章了嗎？讓我們先用較為通俗的比喻來對應這個流程，雖然這的確不是很精確。但我相信在學習過程中多少能有點幫助。
開始介紹各個角色與其對應之前先讓我們來看看 Redux 的世界裡有哪些主要角色？

* state

* action

* action creator

* reducer

* store

### state 單一事件的狀態
首先介紹的就是 `state` ，少了這個 `state` 那麼是無法交織出精彩的宮廷劇。這個 state表示的是一件事情的狀態，用上面的例子來說就是蕭景琰是不是親王。

### action 聖旨
在古代朝廷裡舉凡重要大事例如：加薪升職或者出兵打仗基本上都是需要聖旨，這個聖旨也就只是一份記錄下來，具備特定格式的文件。
而在 Redux 中剛好有這麼一個角色，它就是 `action` 。基本上他就是一個單純的物件，記錄著要做什麼事，就像聖旨一般。

如下就是一個 `action` 在程式中的樣子
```
var action = {
  type: '加封親王',
  name: '蕭景琰' // 取自<琅琊榜>
}
```
### action creator 內閣
這個角色在劇中通常我們會忽略，因為他的任務好無聊啊！就只是把要執行的事情格式化成特定的文件。
講白一點，梁帝只說了一句話：加封蕭景琰為親王。自然就要有人來幫他寫完這份聖旨。

下面即 `action creator` 在程式中的範例：本質是一個 `function` 回傳 `object` ，這個 `object` 即 action
```
function actionCreator(data) {
  return {
    type: '加封親王',
    name: '蕭景琰' // 取自<琅琊榜>
  };
}
```
`store` 梁帝身旁的太監總管 - 高湛
簡單來說，梁帝得知任何情報消息都是靠他，甚至要發聖旨也要靠他。在一般情況下太監總管幾乎等於皇上的狀態管理中心，而且就只有一個，管理一個國家和管理程式的狀態一樣複雜，如果到處都可以發動命令修改狀態那總會有狀態因為時間差不一致的狀況。

而要在程式中建立一個 `store` 我們需要先從 `redux` 匯入 `createStore` 接著如下建立
```
var store = createStore(reducer)
```
reducer 實際宣旨的太監 - 負責實際的狀態變更
當太監總管把任務交代下來，底下的太監就要去執行任務，reducer 才是真正變更了狀態的角色。

雖然在劇中不是挺重要的角色，但在 `Redux` 所有的狀態都是由它進行異動的，要牢記的是 `reducer` 本質上是一個 `function`，必須要回傳 `state` 物件，最重要的是它永遠是傳入當前的 `state` 和 `action` 接著處理完後回傳另一個完整的 `state` ， 表示式為 `(state, action) => newState`。

流程
在開演之前有些幕後工作要完成，首先是太監總管(store)要知道怎麼使喚部下(reducer)。
```
store = createStore(reducer)
```
`createStore()` 等於是前置作業，如此一來太監總管就知道該怎麼分派任務。當皇上下旨後，我們會需要內閣(`action creator`)的協助，得到一份聖旨(`action`)，接著太監總管(`store`)就分派(`dispatch`)聖旨(`action`)給底下的太監們(`reducers`)去執行。
```
store.dispatch(action)
```
如果皇上想要知道事情的狀態那就得問太監總管
```
store.getState()
```
另外就是當聖旨宣布後，太監總管應該也要順便通知禮部等單位進行後續的準備
```
store.subscribe(listener)
```
這個 `listener` 就是當聖旨頒佈後後續要做的事，很合理的該單位總是也該知道事情進行到哪了，此時這個 `function` 便可以透過 `getState()` 來取得消息，不過要注意的是;這個太監總管不會先行通知，只會在發佈了聖旨後才會通知各部(`listener`)。

看到這邊我們才驚覺原來在古代的中國早就知道單一資料流的好處 XD。

而所謂的 `Redux` 概略的比喻就是各司其職的這些角色搭配運作流程的規範。最後讓我們先看一下概略示意虛擬碼：
```
// 注意: Javascript 的變數與 function name 並不支援中文
// action type
const 加封七珠親王 = '加封七珠親王';

// action creator
function 內閣撰寫聖旨加封 (人名, 官位) {
  // 傳回一個 action
  return {
    type: 加封七珠親王,
    name: 人名,
    level: 官位
  }
}

// reducer
function 負責的太監甲 (state, action) {
  switch (action.type) {
    case: 加封七珠親王:
      return {
        ...state,
        {
          ...state[action.name],
          level: action.level
        }
      };
    default:
      return state;
  }
}

// store
var 太監總管 = createStore(太監們)

// action
var 聖旨 = 內閣撰寫聖旨加封('蕭景琰', '親王')

太監總管.dispatch(聖旨)
```
總體來說這邊只希望你記住三件事：

太監總管只有一個; 整個應用程式的 `state`，被儲存在一個樹狀物件放在唯一的 `store` 裡面。

要執行任何重大事件都需要聖旨; 改變 `state` 的唯一的方式是發出一個 `action`，也就是一個描述發生什麼事的物件。

實際去宣旨的是太監(`reducer`); `state` 的轉變必須只能在 `reducer` 撰寫，這永遠是一個 `(state, action) => newState` 的 `function`。

### 實作 Redux
上面段落扯了這麼多，是希望用一個通俗的說明，讓您先大略明白整個流程，有個概念，接著我們就要以實作流程來說明。我相信目前網路上的教學不論詳細程度還是文章的質量都很難比官方教學來得好，但就是因為該文件太詳細了，有些緣由對於初學者來說不太好掌握，所以這個部分我希望能夠快速帶你走過一遍個人的實作流程，讓你能夠掌握每一個角色，有了概念後建議您搭配官方教學來深入學習。

從規劃 `state` 物件開始
為了讓事情單純一點，我們將使用 Boilerplate 以避開一些繁瑣的設定，讓你能夠先專注在 redux 上。這個樣板包含了下面這些工具與函式庫的相關設定：
```
webpack
webpack-dev-server
babel
hot-reload
react
```
建立專案指令如下：
```
git clone https://github.com/gaearon/react-hot-boilerplate.git demo // demo 可換成你喜歡的專案名稱
cd demo
npm install
npm install redux -S
# npm start 便可以開啟網站
```
現在，假設我們只有一個陣列 todos 要維護，這個 state 物件 結構如下
```
{
  todos: []
}
```
注意：這個 `state` 物件就是我們整個應用程式唯一的狀態管理中心，我們一般無法直接操作它，一切都是透過 `store` 管理，`reducer` 改變。

### 列舉出需要實作的行為 - action type
我們只會實作一個行為 `ADD_TODO` 用來解釋每個角色之間的使用與關係，接著用你喜歡的編輯器開啟 demo 目錄之後，我們先建立 `src/actions.js`，我們使用 `string` 來替這些行為命名，這就是 `action type`。
把 `action type` 宣告為常數並匯出不是必須的，你的確可以單純用字串就好，不過為了減少錯誤與重構的好處我們還是使用了常數的方式。
```
/* file: src/actions.js */

// action type
export const ADD_TODO = 'ADD_TODO'

/**
 * action creator
 * 有一個 action type 就需要對應一個 action creator 用來格式化需要的資料
 * 本質是一個 function 回傳一個 object 裡面包含著要執行的行為和需要的資料 
 */
export function addTodo() {
  return {
    type: ADD_TODO
  }
}
```
### 透過 root reducer 定義資料結構
在此我們不深入探討原理，只需要知道使用 `combineReducers()` 傳入的物件參數等於我們最後會得到的 `state` 物件。

建立 `src/reducers.js` 並輸入程式碼如下：
```
/* file: src/reducers.js */

import {combineReducers} from 'redux'
import {ADD_TODO} from './actions'

/**
 * root reducer 就是決定 state 屬性的地方
 */
const rootReducer = combineReducers({
  // 這邊等於 state 規劃的 property
  todos
})

export default rootReducer
```
透過 `reducer` 實作變更 `state` 的行為
用一個舊有的經驗來比喻，`combineReducers` 的參數部分所定義的物件，就像是我們在規劃資料庫時定義資料表`(Table)`一般。
至於內部欄位還得靠真正被傳進去的那些 `reducer` 。所以實際的欄位操作得在 `reducer` 定義。
```
/* file: src/reducers.js */

import {combineReducers} from 'redux'
import {ADD_TODO} from './actions'

/**
 * root reducer 就是決定 state 屬性的地方
 */
const rootReducer = combineReducers({
  // 這邊等於 state 規劃的 property
  todos
})

/**
 * reducer: 依據 rootReducer 的屬性實作 reducer 這個 function
 * 注意: action 除了 type 外，還要包含修改 state 所需要的資料
 * 這個資料可能是索引或者要加入的資料
 * 
 */
function todos (state = [], action) {
  switch (action.type) {
    case ADD_TODO:
      return [
        ...state,
        action.todo
      ]
    // 處理不符合 action.type 的部分，一定要回傳 state
    default:
      return state 
  }
}

export default rootReducer
```
記住! `reducer` 是一個純函式遵循著 `(state, action) => newState` 的結構規範，且不得在內部產生任何 side effect。
簡單說 `reduce` 這個函式的工作就是把 `state` 和 `action` 當作參數傳入，回傳另外一個變更後的 state，這個 state 因為不能有 side effect 所以回傳的是另外一個全新的物件。

再次強調 `(state, action) => newState` 所以就算你傳入的 `action` 跟該 `reducer` 無關，你也要再把 state 傳回去。記住上面的表示式，非常重要。

`action` 只是一個聖旨記錄了要執行的事情，而 `reducer` 才是實際處理 `state` 的地方。

根據 `reducer` 所需，收斂整理出需要的資料，完善 `action creator` 和 `action`
這一步會隨個人習慣有所差異，有人能夠在一開始就把 `action` 需要的資料都定義好，但對於反應較慢的小弟我來說
反過來從 `reducer` 推回去比較直覺一點。

重點是 `action` 除了 `action.type` 即我們的任務之外還要包含處理這個任務所需要的資料。而剛剛的 reducer 我們先假定了有 action.todo，所以讓我們回到 src/actions.js 來完善他們
```
/* file: src/actions.js */

// Action Type
export const ADD_TODO = 'ADD_TODO';

// Action Creator
export function addTodo(todo) {
  return {
    type: ADD_TODO,
    todo
  }
}
```
### store 的建立與實際測試
到了這一步，我們可以來釐清這些角色之間的關係與該如何使用，開啟 src/index.js ，刪去預設產生的程式碼，改寫如下
```
/* file: src/index.js */

// 匯入 action creator: addTodo
import {addTodo} from './actions'

import { createStore } from 'redux'

import rootReducer from './reducers'

var store = createStore(rootReducer);

var unsubscribe = store.subscribe(() => {
  console.log(store.getState())
});

console.log('Initial:', store.getState());

store.dispatch(addTodo('Hello, Redux'));
```

相信到這邊你已經明白 `redux` 的流程是怎麼回事了。

### 先設計一個單純只負責呈現的 component
這部分我們要開始參透 redux 如何和 react 搭配使用，我假設你已經很了解 react ，因為這篇文章訴求是說明概念，因此就不在檔案架構上做太多分類切割。

我們的這個 component 其實就只需要

一個 `input` 用來把資料丟進 `todos` 這個陣列

一個 `button` 觸發儲存的行為

一個 `ul li` 結構用來顯示 `todos`

當然下面這樣的做法在實務上不是很好，但希望可以協助你方便觀察理解角色之間的應用，所以我就把他們全部放在一個檔案裡。

開啟 `src/App.js` 編輯程式碼如下
```
/* file: src/App.js */

import React, { Component } from 'react';

export default class App extends Component {
  render() {
    const {todos} = this.props;
    return (
      <div>
        <input type='text' ref='input' />
        <button>Add</button>
        <ul>
        {
          todos.map((todo, index) => <li key={index}>{todo}</li>)
        }
        </ul>
      </div>
    );
  }
}
```
### 測試 component
為了方便觀察所以並沒有把剛剛測試 `store` 的程式碼移除，釐清角色之間的關係而後依據目錄拆分檔案會更有幫助：
```
/* file: src/index.js */

import { addTodo } from './actions'
import { createStore } from 'redux'
import rootReducer from './reducers'
var store = createStore(rootReducer);

import React from 'react'
import { render } from 'react-dom'
import App from './App'

var unsubscribe = store.subscribe(() => {
  console.log(store.getState())
});

console.log('Initial:', store.getState());

store.dispatch(addTodo('Hello, Redux'));

render(
  <App todos={['A', 'B']} />,
  document.getElementById('root')
)
```
### 將 component 與 store 連線
`store` 和我們的 `component` 並不會無緣無故自訂就關聯起來，可以見得的是我們需要一個介面或一個做法來將他們倆關聯起來，而要將 `store` 與我們的 `component` 連線我們就需要 `react-redux`。
```
npm install react-redux -S
```
接著在 `src/index.js` 完成程式碼如下
```
/* file: src/index.js */

import React from 'react'
import { render } from 'react-dom'
import { addTodo } from './actions'
import { createStore } from 'redux'
import App from './App'
import rootReducer from './reducers'
// 匯入 Provider
import { Provider } from 'react-redux'

var store = createStore(rootReducer);

var unsubscribe = store.subscribe(() => {
  console.log(store.getState())
});

console.log('Initial:', store.getState());

store.dispatch(addTodo('Hello, Redux'));

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```
看看上面的程式碼是不是很容易聯想到我們以前從資料庫取資料的流程，我們會需要針對特定的資料庫例如 `MySQL`, `PostgreSQL` 的 `provider` 函式庫，而在 `redux` 我們則是透過 `Provider` 這個方式提供一個介面讓我們的 `component` 能夠和 `store` 連線上，而實際上該如何使用還是得靠元件內部來實作。

最後實作 App 內部
```
/* file: src/App.js */

import React, { Component } from 'react'
import {connect} from 'react-redux'
import {addTodo} from './actions'

class App extends Component {
  render() {
    var {dispatch, todos } = this.props;
    return (
      <div>
        <input type='text' ref='input'/>
        <button onClick={(e) => dispatch(addTodo(this.refs.input.value))}>
          Add
        </button>
        <ul>
        {
          todos.map((todo, index) => <li key={index}>{todo}</li>)
        }
        </ul>
      </div>
    );
  }
}

function select (state) {
  return state;
}

export default connect(select)(App);
```
### select 和 connect 是什麼？
connect 的用途概念上比較容易理解，我們已經有 `Provider` 了，但我們還是需要主動去連線。而 `select` 如果你用資料庫的概念來想也就非常清楚了，雖然我有一個資料庫但我並不總是要全部的資料，透過 select 可以讓我們選取我們要的部分就好。但是在上面的範例我們是全取。

### 總結
如此我們已經走完了一遍最基礎的部分，也對各角色有了概念上的理解，這和實際原理其實有些落差。不過在學習的過程中我希望提供另一個角度的看法。當然小弟很清楚這篇文章還有很多有缺失的地方，所以才會建議當你對 redux 的掌握度有些許的進步之後一定要回頭看看官方的教學。

1. 針對 `App` 的功能操作列出需要的 `action type`
2. 根據 `action type` 實作 `action creator` 與定義回傳的 `action`，記住 `reducer` 需要的資料只能從這來
3. 先構思完整的 `state` 結構，盡可能保持一階的結構類似資料庫正規劃
4. 針對資料的處理 `root reducer` 和 `reducer`
5. 透過 `combineReducers()` 組合 `state` 與對應的 `reducer`，概念上類似於資料庫的 `Table` 列表
6. 一個 `reducer` 純函式 `(state, action) => newState` 是針對 `state` 撰寫處理狀態修改的邏輯
7. 每一個 `reducer` 都需要針對未知的動作類型`(action.type)`做處理，無論如何都要傳回 `state`
8. 透過 `createStore(reducer)` 建立一個 `store`。
9. 透過 `react-redux` 的 `Provider` 來把 `store` 與 `component` 關聯起來
10. `component` 內部透過 1connect1 連線並取得資料，搭配 select (只是一個 filter 的 function)

轉載自： https://segmentfault.com/a/1190000004257207