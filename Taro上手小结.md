---
title: Taro上手小结
date: 2019-03-05 10:52:10
tags: 微信小程序
---

因为公司的开发需求和技术选型，接触到了Taro，目前小程序多端编译最热的框架，下面是一些最基础的总结。

<!-- more -->

## 安装/使用Taro

    $ npm install -g @tarojs/cli //全局安装
    $ taro init myApp //创建自己的项目
    $ taro build --type weapp --watch //编译成微信小程序
    $ taro update self //更新项目
    $ taro update project //更新依赖
    $ taro info //环境及依赖检测

## 目录结构

    ├── dist                   编译结果目录
    ├── config                 配置目录
    |   ├── dev.js             开发时配置
    |   ├── index.js           默认配置
    |   └── prod.js            打包时配置
    ├── src                    源码目录
    |   ├── components         公共组件目录
    |   ├── pages              页面文件目录
    |   |   ├── index          index 页面目录
    |   |   |   ├── banner     页面 index 私有组件
    |   |   |   ├── index.js   index 页面逻辑
    |   |   |   └── index.css  index 页面样式
    |   ├── utils              公共方法库
    |   ├── app.css            项目总通用样式
    |   └── app.js             项目入口文件
    └── package.json

## 页面结构

    import Taro, { Component } from '@tarojs/taro'
    import { View, Text } from '@tarojs/components'
    import './index.scss'

    export default class Index extends Component {
    // 页面配置相当于index.json
    config = {
        navigationBarTitleText: '首页'
    }

    componentWillMount () { }  /* 相当于onLoad但是如果需要使用options参数在taro中用this.$router.params来获取 */

    componentDidMount () { } //相当于onReady

    componentWillUnmount () { } //相当于onUnload

    componentDidShow () { } //相当于onshow

    componentDidHide () { } //相当于onHide

    render () { //页面JS必须有一个render函数，函数返回JSX代码
        return (
        <View className='index'>
            <Text>1</Text>
        </View>
        )
    }
    }

## 组件化（props）

- Taro 的组件同样是继承自Component组件基类，与页面类似，组件也必须包含一个render函数，返回JSX代码。与页面相比，组件没有自己的config，同时组件的生命相比页面来说多了一个**componentWillReceiveProps**，表示当父组件（或页面）发生更新时将带动子组件进行更新时调用的方法。
- 组件从概念上看就像是函数，它可以接收任意的输入值（称之为“props”），并返回一个需要在页面上展示的Taro元素。props具有只读性，一个声明的组件决不能修改它自己的props。

        // welcome.js
        class Welcome extends Component {
        render () {
            return <View><Text>Hello, {this.props.name}<Text></View>
        }
        }

        // app.js
        class App extends Component {
        render () {
            return <Welcome name="Wallace" />
        }
        }

## state

    import Taro, { Component } from '@tarojs/taro'

    class Clock extends Component {
    constructor (props) {
        super(props)
        this.state = { date: new Date() }
    }

    componentDidMount () { //相当于onReady
        //这里值得注意，id被存在this里而不是state里
        this.timerID = setInterval(
        () => this.tick(),
        1000
        );
    }

    componentWillUnmount () { //相当于onUnload
        clearInterval(this.timerID)
    }

    tick () {
        this.setState({
        date: new Date()
        });
    }

    render() {
        return (
        <View>
            <Text>Hello, world!</Text>
            <Text>现在的时间是 {this.state.date.toLocaleTimeString()}.</Text>
        </View>
        )
    }
    }

正确地使用State：

- 不要直接更新状态
- 状态更新一定是异步的
- state更新会被合并

## 事件处理

    class Toggle extends Component {
    constructor (props) { //构造方法初始化
        super(props)
        this.state = {isToggleOn: true}
    }

    onClick = (e) => {
        e.stopPropagation() //阻止事件冒泡，区别catchEvent
        this.setState(prevState => ({ //prevState是setState的一个参数，可以理解成this.state
        isToggleOn: !prevState.isToggleOn
        }))
    }

    render () {
        return (
        <button onClick={this.onClick}>
        {*任何组件的事件传递都要以on开头*}
            {this.state.isToggleOn ? 'ON' : 'OFF'}
        </button>
        )
    }
    }

    // 事件处理传参
    <button onClick={this.onDeleteRow.bind(this, id)}>Delete Row</button>

## 列表渲染和条件渲染

条件渲染：

    // LoginStatus.js
    class LoginStatus extends Component {
    render () {
        const isLoggedIn = this.props.isLoggedIn
        // 这里最好初始化声明为 `null`，初始化又不赋值的话
        // 小程序可能会报警为变量为 undefined
        let status = null
        if (isLoggedIn) {
        status = <Text>已登录</Text> //元素变量
        } else {
        status = <Text>未登录</Text>
        }

        return (
        <View>
            {status}
        </View>
        )
    }
    }

    // app.js
    import LoginStatus from './LoginStatus'
    class App extends Component {
    render () {
        return (
        <View>
            <LoginStatus isLoggedIn={true} />
        </View>
        )
    }
    }

    //使用逻辑运算符&&
    class LoginStatus extends Component {
    render(){
    const isLoggedIn = this.props.isLoggedIn
    return (
        <View>
            {isLoggedIn && <Text>已登录</Text>}
            {!isLoggedIn && <Text>未登录</Text>}
        </View>
    )}
    }

    //使用三元表达式
    class LoginStatus extends Component {
    render () {
        const isLoggedIn = this.props.isLoggedIn
        return (
        <View>
        {isLoggedIn
            ? <Text>已登录</Text>
            : <Text>未登录</Text>
        }
        </View>
        )
    }
    }

列表渲染：

    class App extends Componenet {
    state = {
        posts: [
        {id: 1, imgurl: 'https://elcometolearning.dev'},
        {id: 2, imgurl: 'https://elcometolearning.dev'}
        ]
    }
    render () {
        const { posts } = this.state //推荐使用解构赋值
        <Swiper
          className='swiper'
          indicatorColor='#999'
          indicatorActiveColor='#333'
          circular
          indicatorDots
          autoplay
        >
          {posts.map((item) => { //使用map来处理数组
            return (
              <SwiperItem className='swiper-img' key={item.id}>
                <Image className='swiper-img' src={item.imgurl}
                  mode='center'
                />
              </SwiperItem>
            )
          })}
        </Swiper>
    }
    }

列表渲染时的key必须有，它的作用是当DOM中的某些元素被增加或删除的时候帮助小程序识别哪些元素发生了变化。key的最好选择就是后端传给你的元素id。

## 我遇到过的问题

- 在非app.js使用静态图片，编译后小程序内并没有复制过去。解决办法：用 import 引入对应的图片。
- ESLint插件报错。解决办法：npm全局安装。
- 注意编译顺序，页面代码结构也需要参照编译顺序。
- Windows环境下安装sass/less失败。解决办法：使用cnpm安装。