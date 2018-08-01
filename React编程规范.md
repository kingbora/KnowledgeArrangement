## 一、工程结构
### 1.1 工程目录
目前大致工程目录如下：
```jsx
├─ src                         //源码
│  ├─ components               //展示组件，嵌套在容器组件中，通过props获取容器组件中的数据，负责展示和渲染
│  │  ├─ DemoComponent
│  │  │  ├─ index.tsx
│  │  │  └─ style.scss
│  │  └─ TestComponent
│  │     ├─ index.tsx
│  │     └─ style.scss
│  ├─ containers              //容器组件，负责业务流程逻辑的处理，如发送网络请求，处理请求数据
│  │  ├─ DemoContainer
│  │  │  ├─ reducer.ts        //响应action，处理并更新state
│  │  │  ├─ saga.ts           //发起http请求，分发请求之后的结果
│  │  │  ├─ action.ts         //传递数据到store
│  │  │  ├─ constant.ts       //定义事件常量
│  │  │  ├─ index.tsx
│  │  │  └─ style.scss
│  │  └─ TestContainer
│  │     ├─ reducer.ts
│  │     ├─ saga.ts
│  │     ├─ action.ts
│  │     ├─ constant.ts
│  │     ├─ index.tsx
│  │     └─ style.scss
│  ├─ images                  //存放图片资源
│  │  ├─ Common
│  │  ├─ DemoComponent
│  │  ├─ TestComponent
│  │  ├─ DemoContainer
│  │  └─ TestContainer
│  ├─ mock                    //存放静态json数据，多用于调试
│  ├─ styles                  //全局样式
│  ├─ utils                   //工具库
│  ├─ app.tsx                 //入口文件，配置store，热加载等
│  ├─ config.tsx              //api接口
│  ├─ routes.ts               //页面路由
│  ├─ reducers.ts             //合并reducer
│  ├─ sagas.ts                //合并saga
│  ├─ index.html              //入口html文件
│  └─ tsconfig.json           //TypeScript编译配置文件
├─ www
├─ .babelrc
├─ .gitignore
├─ Jenkinsfile
├─ webpack.config.js
├─ webpack.vendor.config.js
└─ package.json
```
> 注：<br>
> 1、其中components和containers文件夹中的文件夹命名规则以大驼峰式命名法（或叫Pascal命名法）为准，即每个单词采用大写字母，单词之间不以空格、连接号或底线连接。<br>
> 2、images文件夹中的图片资源的命名以单词底线连接规则，如`login_bg.jpg`；common文件夹存放公共资源文件

### 1.2 文件示例
#### 1.2.1 components中的文件
```jsx
import * as React from 'react';
//props声明
interface DemoComponentProps {
    dataSource: any;
}
//state声明
interface DemoComponentState {
    isLoading: boolean;
}

export default class DemoComponent extends React.Component<DemoComponentProps, DemoComponentState> {
    construct(props: DemoComponentProps) {
        super(props);

        this.state = {
            isLoading: false
        };
    }

    render() {
        return (
            <div>123</div>
        );
    }
}
```

> 1、组件继承于Component或PureComponent可自行调整，PureComponent能够进行数据的浅比较，减少不必要的`render`，提高性能，但使用了PureComponent不能再声明`shouldComponentUpdate`钩子函数。<br>
> 2、state和props中的变量以小驼峰式命名规则为准，即小写字母开头，其他单词大写，单词之间不以空格、连接号或底线连接。

#### 1.2.2 containers中的文件
```jsx
import * as React from 'react';
import { connect } from 'react-redux';
import { getUserInfo } from './action';
//组件自身props声明，从其他组件传入的值
interface DemoComponentOwnProps {
    dataSource: any;
}
//从store传入组件的值，即进过reducer处理的值
interface DemoComponentStateProps {
    launchData?: any;
}
//发送事件的方法
interface DemoComponentDispatchProps {
    getUserInfo?: Function;
}
//state声明
interface DemoComponentState {
    isLoading: boolean;
}

//connect修饰器
@(connect<DemoComponentStateProps, DemoComponentOwnProps & DemoComponentDispatchProps, DemoComponentState>(
    (state: any) => (
        {
            launchData: state.demoReducer.launchData
        }
    ),
    (dispatch: any) => (
        {
            getUserInfo: () => {
                //多个参数使用对象形式进行传参
                dispatch(getUserInfo());
            }
        }
    )
) as any)
export default class DemoComponent extends React.Component<
  DemoComponentStateProps & DemoComponentOwnProps & DemoComponentDispatchProps,
  DemoComponentState
  > {
    construct(props: DemoComponentStateProps & DemoComponentOwnProps & DemoComponentDispatchProps) {
        super(props);

        this.state = {
            isLoading: false
        };
    }

    render() {
        return (
            <div>123</div>
        );
    }
}
```

> 1、组件继承于Component或PureComponent可自行调整，PureComponent能够进行数据的浅比较，减少不必要的`render`，提高性能，但使用了PureComponent不能再声明`shouldComponentUpdate`钩子函数。<br>
> 2、state和props中的变量以小驼峰式命名规则为准，即小写字母开头，其他单词大写，单词之间不以空格、连接号或底线连接。<br>
> 3、constant以全大写为主，单词之间以底线连接，如:
> ```jsx
> export const SHOW_MODAL = 'CUSTOM_MODAL/SHOW_MODAL'
> ```
> 4、其他文件的变量命名以小驼峰式命名为准。

### 1.3 React代码规范
#### 1.3.1 基本代码风格
* 代码缩进
```jsx
//能在一行显示时，结尾空格分隔
<Foo bar="bar" baz="baz" />

//bad，不能在一行显示时
<Foo bar="bar" baz="baz" ... />

//good
<Foo 
  bar="bar"
  baz="baz"
  ...
/>
```
* 双引号使用
```jsx
//bad
<Foo bar='bar' />

//good
<Foo bar="bar" />

//bad
<Foo style={{ left: "20px" }} />

//good
<Foo style={{ left: '20px' }} />
```
* 总是使用回调函数方式定义ref
```jsx
//bad，这样定义就会弱化语法类型，如：单词写错了对应不上不会报错提示
<Foo ref="myRef" />

//good
<Foo ref={ref => this.myRef = ref} />
```
* 将多行JSX标签写在`()`中
```jsx
//bad
render() {
    return <MyComponent>
        <MyChild />
    </MyComponent>;
}

//good
render() {
    return (
        <MyComponent>
            <MyChild />
        </MyComponent>
    );
}

//good，单行可以不需要()
render() {
    return <MyComponent />;
}
```
* JSX map返回
```jsx
render() {
    //推荐取props或state上的值通过这种方式取，不建议直接通过`this.props.`或`this.state.`取
    const { data } = this.props;
    return (
        <ul>
            {
                //若直接返回元素，无中间计算过程，无需return，使用`()`进行返回
                data.map((item: any) => (
                    <li key={item.id}>
                        {
                            //若存在计算，则通过`{}`包裹，使用return返回JSX标签
                            item.map((piece: any) => {
                                const body = <span key={piece.id}>123</span>;
                                //do something
                                return body;
                            }
                        }
                    </li>
                ))
            }
        </ul>
    )
}
```
* 方法书写规范
```jsx
//bad
render() {
    return <button onClick={() => this.handleClick()}>123</button>
}

//good，当无参数时
render() {
    return <button onClick={this.handleClick}>123</button>
}

//good，但有参数时
render() {
    return <button onClick={(e) => this.handleClick(e, other)}>123</button>
}
```

#### 1.3.2 影响性能的代码风格
* 在JSX标签中bind`this`
> 因为在每次`render`过程中，再调用`bind`都会新建一个新的函数，浪费资源
```jsx
//bad
class A extends React.Component {
    handleClick() {
        //do something
    }

    render() {
        return <button onClick={this.handleClick.bind(this)}>123</button>
    }
}

//good
class A extends React.Component {
    constructor(props) {
        super(props);

        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        //do something
    }

    render() {
        return <button onClick={this.handleClick}>123</button>
    }
}
```
* 使用const常量
> 因为在每次`render`过程中，都会新建一个新的变量，浪费资源
```jsx
//bad
render() {
    return <div style={{color: 'red'}}>123</div>
}

//good
render() {
    const textColor = {color: 'red'};
    return <div style={textColor}>123</div>
}
```
* 对循环JSX标签使用key
> 不推荐直接使用index，最好为唯一标识
```jsx
render() {
    return (
        <ul>
            {
                data.map((item: any) => (
                    <li key={item.id}>123</li>
                ))
            }
        </ul>
    )
}
```
* 少用`display:none`来隐藏元素
> 使用return null而不是CSS的display:none来控制节点的显示隐藏。保证同一时间页面的DOM节点尽可能的少
```jsx
//bad
render() {
    const { isShow } = this.props;
    return (
        <div>
            <span style={{ display: isShow ? 'block' : 'none' }}>123</span>
        </div>
    )
}

//good
render() {
    const { isShow } = this.props;
    return (
        <div>
            {
                isShow &&
                <span>123</span>
            }
        </div>
    )
}
```
* 避免兜底值字面量
> 有时我们会在`render`函数中创建一个兜底的值来避免`undefined`报错。在这些情况下，最好在组件外创建一个兜底的常量而不是创建一个新的字面量。
```jsx
//bad
render() {
    let arr = [];
    if (this.props.things) {
        arr = this.props.things;
    }

    return <Foo things={ things } />;
}
//bad
render() {
  // 这在功能上和前一个例子一样
  return <Foo things={ this.props.things || [] } />
}

//good
// 在组件外部声明
const NO_THINGS = [];

render() {
  return <Foo things={ this.props.things || NO_THINGS } />
}
```
* 不滥用{...this.props}
> 只传递component需要的props即可，传递的太多，或者层次传的太深，都会加重`shuoldComponentUpdate`其里面的数据比较负担

* 慎用`setState`
> 与视图渲染无关的，但需存储数据的，不要放在`state`中，每次`setState`都会引起不必要的`render`，尽管视图并未需要更新。

### 1.4 优化篇
#### 1.4.1 组件细分
#### 1.4.2 灵活使用shouldComponentUpdate
#### 1.4.3 reselect
#### 1.4.4 immutable.js的救赎
