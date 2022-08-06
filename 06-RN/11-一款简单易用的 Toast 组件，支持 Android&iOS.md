# [react-native-easy-toast](https://github.com/crazycodeboy/react-native-easy-toast/)

> 尊重版权,未经授权不得转载
> 本文出自:贾鹏辉的技术博客官网([https://www.devio.org](https://www.devio.org/))

一款简单易用的 Toast 组件，支持 Android&iOS。

## 目录

- [安装](https://www.devio.org/2016/09/13/一款简单易用的-Toast-组件-支持-Android&iOS/#安装)
- [Demo](https://www.devio.org/2016/09/13/一款简单易用的-Toast-组件-支持-Android&iOS/#demo)
- [如何使用？](https://www.devio.org/2016/09/13/一款简单易用的-Toast-组件-支持-Android&iOS/#如何使用？)
- [API](https://www.devio.org/2016/09/13/一款简单易用的-Toast-组件-支持-Android&iOS/#api)
- [贡献](https://www.devio.org/2016/09/13/一款简单易用的-Toast-组件-支持-Android&iOS/#contribution)

## 安装

- 1.在终端运行 `npm i react-native-easy-toast --save`
- 2.在要使用`Toast`的js文件中添加`import Toast, {DURATION} from 'react-native-toast-easy'`

## Demo

- [Examples](https://github.com/crazycodeboy/react-native-easy-toast/tree/master/examples)

![Screenshots](https://raw.githubusercontent.com/crazycodeboy/react-native-easy-toast/master/examples/Screenshots/react-native-easy-toast-screenshots.gif)

## 如何使用？

> 第一步：

在你的js文件中导入 `react-native-toast-easy`：

```react
import Toast, {DURATION} from 'react-native-toast-easy'
```

> 第二步：

将下面代码插入到`render()`方法中：

```react
 render() {
         return (
             <View style={styles.container}>
                 ...
                 <Toast ref="toast"/>
             </View>
         );
 }
```

> 注意: 请将`<Toast ref="toast"/>` 放在最外层View的底部.

> 第三步：

使用：

```react
 this.refs.toast.show('hello world!');
```

在需要弹出提示框时使用上面代码即可。

### 用例

```react
render() {
        return (
            <View style={styles.container}>
                <TouchableHighlight
                    style={ {padding: 10}}
                    onPress={()=>{
                        this.refs.toast.show('hello world!');
                    }}>
                    <Text>Press me</Text>
                 </TouchableHighlight>
                 <Toast ref="toast"/>
            </View>
            );
    }
```

### 自定义 Toast

```react
render() {
        return (
            <View style={styles.container}>
                <TouchableHighlight
                    style={ {padding: 10}}
                    onPress={()=>{
                        this.refs.toast.show('hello world!',DURATION.LENGTH_LONG);
                    }}>
                    <Text>Press me</Text>
            		</TouchableHighlight>
                <Toast 
                        ref="toast" 
                        style={ {backgroundColor:'red'}} 
                        position='top'
                    />
            </View>         
        );
    }
```

**更多用例:**

[SearchPage.js@GitHubPopular](https://github.com/crazycodeboy/GitHubPopular/blob/develop/js/page/SearchPage.js)

## API

| 属性     | 类型                                        | 可选 | 默认值                                                       | 描述              |
| :------- | :------------------------------------------ | :--- | :----------------------------------------------------------- | :---------------- |
| style    | View.propTypes.style                        | true | {backgroundColor: ‘black’,opacity: OPACITY,borderRadius: 5,padding: 10,} | 自定义Toast的样式 |
| position | PropTypes.oneOf([‘top’,’center’,’bottom’,]) | true | ‘bottom’                                                     | 自定义Toast的位置 |

| 方法                 | 类型     | 可选  | 描述           |
| :------------------- | :------- | :---- | :------------- |
| show(text, duration) | function | false | 弹出提示框     |
| close()              | function | true  | 手动关闭提示框 |

## 贡献

欢迎大家提Issues。如果能为Issues配一个异常或报错的截图，能帮助我快速的定位问题和解决问题。
另外欢迎大家Pull requests，为项目贡献的智慧。

------

**MIT Licensed**
大家可以自由复制和转载。