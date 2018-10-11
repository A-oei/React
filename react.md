## Reatc

### JSX语法

jsx是一种JavaScript语法的拓展语言，在React中官方也推荐使用jsx描述用户界面，使用起来会比较快捷而且易读

jsx不是一门新的语言，可以理解为是一种语法糖，作用就是能够让我们更加直观的在js中创建html标签，jsx最终还是会被编译为JavaScript语法，例如我们看一段jsx代码：

   ```react
   /*jsx*/
   ReactDOM.render(
       <div className="wrap">
           <h3>hello world</h3>
       </div>,
       document.querySelector("#root")
   )
   ```

 上面的jsx语法最终会被编译为JavaScript语法，中间就是通过一个React.createElement的方法来转化，转换后就是下面这段代码

```react
   ReactDOM.render(
          React.createElement(
              'div',
              {className:'wrap'},
              '',
              React.createElement(
                  'h1',
                  null,
                  'hello world'
              )
          ),
          document.querySelector("#root")
    )
```

比较两段代码就可以看出来使用js语法来写的话实在不够简练，所以我们通常还是使用jsx的语法



在使用jsx的时候，需要注意几个点

1. 在使用jsx的地方，要注意我们的代码不能被JavaScript引擎直接解析，所以要在为script标签设置特殊的type属性， 阻止JavaScript引擎的解析

   ```react
   <script type="text/react"></script>
   ```

2. 在jsx中只能有一个根节点，如果存在多个根节点会报错，jsx同时也可以进行标签的嵌套

   ```react
   ReactDOM.render(
   	<div> /*根节点*/
           <p>hello world</p>
       </div>,
       document.querySelector("#root")
   )
   ```

3. 在jsx中，我们虽然写的时候看起来比较像是直接写html，但实际上还是JavaScript，所以需要使用JavaScript的语法，例如我们在为标签添加class属性时，不能写class，而是要使用JavaScript语法的className，这样才能被正确的解析

   ```react
   ReactDOM.render(
       <div className="wrap">
       </div>,
       document.querySelector("#root")
   )
   ```

   同样的其他一些属性也需要转化为驼峰写法，如`tabindex` 则对应着 [`tabIndex`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex) 

4. 在jsx中使用单标签，需要在结尾处使用/>，否则不能够被正确的解析

   ```react
   ReactDOM.render(
       <div>
           <img src="1.png"/>
       </div>,
       document.querySelector("#root")
   )
   ```

5. 在jsx中可以任意的使用JavaScript的表达式，但是需要将表达式放置在{}中

   ```react
   const obj = {
       name: "tom",
       age: (age) => {
           return age;
       }
   }
   ReactDOM.render(
       <div>
           <p>我是{obj.name}，年龄是{obj.age(18)}，2+2是{3 + 5}，0>1的结果是{0 > 1 ? "true" : "false"}</p>
       </div>,
       document.querySelector("#root")
   )
   /*编译结果*/
   //我是tom，年龄是18，2+2是8，0>1的结果是false
   ```

   由于jsx本身就是JavaScript，所以我们也可以通过条件判断来决定控制模板的内容

   ```react
   const obj = {
       name: "tom",
       age: (age) => {
           return age;
       }
   };

   function judgment(user) {

       let component;
       component = user ?
           <div>
               <p>我是{obj.name}，年龄是{obj.age(18)}，2+2是{3 + 5}，0>1的结果是{0 > 1 ? "true" : "false"}</p>
           </div> :
           <div>
               出现错误了哦
           </div>

       return component;
   }
   ReactDOM.render(
       judgment(true),
       document.querySelector("#root")
   )
   ```

#### 控制样式

React中并没有提供其他框架中的例如ng-show、v-if等命令，这些都是需要我们自己手动去写的

1. 控制元素的显示/隐藏

   ```react
   class Control extends React.Component {
       constructor(...args) {
           super(...args);
           
           this.state = {
               flag: "block"
           }
       }

       fn() {
           this.setState({
               flag: this.state.flag == "block" ? "none" : "block"
           })
       }

       render() {
           return <div onClick={this.fn.bind(this)}>
               <button>显示/隐藏</button>
               <div className="box" style={{display: this.state.flag}}>
               </div>
           </div>
       }
   }

   ReactDOM.render(
       <Control/>,
       document.querySelector("#root")
   )
   ```
2. 控制class样式

   ```react
   class Control extends React.Component {
       constructor(...args) {
           super(...args);

           this.state = {
               flag: true
           }
       }

       fn() {
           this.setState({
               flag:!this.state.flag
           })
       }

       render() {
           return <div>
                   <p className={this.state.flag?'red size':'yellow'} onClick={this.fn.bind(this)}>是否渲染</p>
               </div>
       }
   }
   ReactDOM.render(
       <Control/>,
       document.querySelector("#root")
   )
   ```

在实际开发中上面的方法都不是很直观，推荐使用**classnames库**

##### 条件判断

   ```react
   class Control extends React.Component {
       constructor(...args) {
           super(...args);
   
           this.state = {
               flag: false
           }
       }
   
       fn() {
           this.setState({
               flag:!this.state.flag
           })
       }
   
       render() {
           return <div onClick={this.fn.bind(this)}>
                   <p>是否渲染</p>
                   {
                       this.state.flag
                           ? <div className="box"></div>
                           : null
                   }
               </div>
   
       }
   }
   
   ReactDOM.render(
       <Control/>,
       document.querySelector("#root")
   )
   ```
### 组件

组件这个概念现在也是非常流行了，如果之前没有使用过其他框架的话可以用一个相对比较通俗的例子来理解组件的概念，Moto之前推出过好几款模块化的手机，该手机包括屏幕，处理器，内存，电池，摄像头等，都支持自由更换拼装，然后组装成一个完整的手机

![组件](.\img\组件.jpg)

现在我们同样可以利用框架，将一个页面中分为导航栏，轮播图，列表等不同的模块，然后我们分别写这些模块的代码，在需要使用的页面将需要的模块自由进行拼装，这些模块就是我们说的组件

在React中有两种定义组件的方式

1. 使用函数定义组件

   ```react
   function Hello() {
       return <div> hello world</div>
   }

   ReactDOM.render(
       Hello(),
       document.querySelector("#root")
   )
   ```

2. 使用类定义组件

   ```react
   class Hello extends React.Component {
       render() {
           return <div>hello world</div>
       }
   }

   ReactDOM.render(
       <Hello/>,
       document.querySelector("#root")
   )
   ```

   注意，**使用组件时，组件名称一定要是大写字母开头的，否则的话会被认为是普通的html文本进行解析**，同时也需要注意的是组件内只允许有一个根元素

#### 组件的嵌套

在React中，组件之间是可以嵌套使用的

```react
class Hello extends React.Component {
    render() {
        return <div>hello</div>
    }
}

class Wrold extends React.Component {
    render() {
        return <div>
            <Hello />
            <h3>world</h3>
        </div>
    }
}

ReactDOM.render(
    <Wrold/>,
    document.querySelector("#root")
)
```



####容器类组件

这个概念类似于Vue中的插槽，如果没有Vue知识的可以将这个理解为在在使用某个组件时我们在该组件内插入一些DOM元素，而且要保证这些DOM元素能够被正确渲染

##### props.children

```react
class FancyBorder extends React.Component {
    render() {
        return (
            <div className="box">
                {this.props.children}
            </div>
        )
    }
}
ReactDOM.render(
    <FancyBorder>
        <p>Lorem ipsum dolor sit amet.</p>
    	<p>Lorem ipsum dolor.</p>
    </FancyBorder>,
    document.querySelector("#root")
)
```

这里就借用一下Vue的概念，我们将组件中设定了`{this.props.children}` 的部分称为**插槽**

在需要插入来自调用者的DOM的部分写入`{props.children}`，之后调用者插入的代码就会在我们写入了`{props.children}`的位置上被展示

我们此时可以打印一下`props.children`，可以发现得到的是一个数组的结果

![props.children打印](.\img\props.children打印.png)

所以我们可以有另外的一种形式插入DOM，就是通过调用`props.children`内不同下标值来对应渲染不同的元素

```react
class FancyBorder extends React.Component {
    handleSlot(){
        console.log(this.props.children)
    }
    render() {
        return (
            <div className="box" onClick={this.handleSlot.bind(this)}>
                <div className="child">
                    {this.props.children[0]}
                </div>
                {this.props.children[1]}
            </div>
        )
    }
}
ReactDOM.render(
    <FancyBorder>
        <p>Lorem ipsum dolor sit amet.</p>
        <p>Lorem ipsum dolor.</p>
    </FancyBorder>,
    document.querySelector("#root")
)
```

##### 指定入口

如果在组件内有多个插槽并且我们想要指定某一段代码在插入的时候插入到组件的指定的插槽，使用下标值得方式可能不是那么方便，这个时候需要我们约定一个针对指定插槽的属性

```react
class FancyBorder extends React.Component {
    render() {
        return (
            <div className="box">
                <div className="child">
                    {this.props.one}
                </div>
                {this.props.two}
            </div>
        )
    }
}
ReactDOM.render(
    <FancyBorder
        one={
            <p>Lorem ipsum dolor sit amet.</p>
        }
        two={
            <p>Lorem ipsum dolor.</p>
        }
    />,
    document.querySelector("#root")
)
```

#### 组件之间的通信

##### 父组件传递信息到子组件

```react
class Child extends React.Component {
    render() {
        return <div>hello {this.props.name}</div>
    }
}

class Parent extends React.Component {
    render() {
        return <div>
            <Child name="world"/>
        </div>
    }
}

ReactDOM.render(
    <Parent/>,
    document.querySelector("#root")
)
```

props方法时官方提供的接口，我们在使用组件时，在引用子组件处，将要传递的内容以设置属性的方式添加到子组件，在子组件中通过`this.props[父组件设置的属性名]`的格式就可以获取到父组件传递的信息，通过props可以传递字符串、数字、对象、数组、函数 ，**子组件在可以通过this.props来查看当前所有接收到的值**

###### 传递对象

```javascript
class Child extends React.Component {
    click(e) {
        console.log(this.props.data,"child");
        e.stopPropagation();
    }
    render() {
        return <div className="child" onClick={this.click.bind(this)}>hello {this.props.data.name}</div>
    }
}
class Parent extends React.Component {
    constructor() {
        super();
        this.state = {
            obj: {
                name: "tom",
                age: 18
            }
        }
    }
    click() {
        console.log(this,"parent")
    }
    render() {
        return <div className="parent" onClick={this.click.bind(this)}>
            <Child data={this.state.obj}/>
        </div>
    }
}
ReactDOM.render(
    <Parent/>,
    document.querySelector("#root")
)
```

###### 传递事件

```javascript
class Child extends React.Component {
    render() {
        return <div className="child" onClick={this.props.data.bind(this)}></div>
    }
}

class Parent extends React.Component {
    click(e) {
        console.log(this)
        e.stopPropagation()
    }

    render() {
        return <div className="parent" onClick={this.click.bind(this)}>
            <Child data={this.click}/>
        </div>
    }
}
ReactDOM.render(
    <Parent/>,
    document.querySelector("#root")
)
```

传递事件的时候可以通过bind改变我们最初事件的this的指向，这里我们看打印的结果，this的指向在child中指向的是child组件

##### 状态提升

状态提升这个概念其实可以理解为我们在其他框架中使用的姜子组件的信息传递给父组件的一个过程，但是在react中并没有提供直接的方法，需要我们自己进行处理，实现的代码如下：

```react
class Child extends React.Component {
    click(i,e) {
        i++;
        this.props.P_click(i);
         e.stopPropagation();
    }
    render() {
        return <div className="child" 
        onClick={this.click.bind(this,this.props.index)}>
               </div>
    }
}

class Parent extends React.Component {
    constructor() {
        super();
        this.state = {
            index: 1
        }
        this.P_Click = this.P_Click.bind(this)
    }

    change(data) {
        this.setState({
            index: data
        });
    }
    render() {
        return <div className="parent">
            {this.state.index}
            <Child P_click={this.change} index={this.state.index}/>
        </div>
    }
}

ReactDOM.render(
    <Parent/>,
    document.querySelector("#root")
)
```

我们首先来说一下大概的思路

- 在父组件中设置一个方法change，在该方法中设置改变我们指定的变量值的处理

  ```react
  change(data) {
  	this.setState({
  		index: data
  	});
  }
  ```

- 将该方法传递给子组件，同时也将我们要要改变的父组件的值传递给子组件

  ```react
   <Child P_click={this.change} index={this.state.index}/>
  ```

- 在子组件操作时，例如点击时，调用我们在父组件中传入的change方法，并改变由父组件发送过来的数据(index)，回传给change方法

  ```react
  <div className="child" onClick={this.click.bind(this,this.props.index)}></div>
  ```

  ```react
  click(i,e) {
  	i++;
  	this.props.P_click(i)
      e.stopPropagation();
  }
  ```

- change方法被调用，通过this.setState响应的改变了父组件中index值



可以看出来确实很麻烦，由于没有像vue那样提供$emit方法，所以需要我们自己迂回写一系列操作，包括如果我们想要在同级的组件之间传递数据，我们需要 `子组件数据 => 父组件 => 子组件兄弟组件`

推荐使用Redux来进行组件之间的数据传递

### 响应式的更新数据



### 生命周期

![react生命周期](.\img\react生命周期.jpg)

##### 接收请求数据放置位置

组件首次渲染时请求的数据处理应该放置在componentDidMount ，componentDidMount是指第一次插入DOM完毕，无论在同步或者异步的情况下都只会触发一次，而且在16.3之后，react开始异步渲染页面，在异步渲染的情况下，componentWillMount中写的方法可能会被多次调用，并且可能会导致内存泄漏的问题，而且在官方说明中componentDidMount并没有存在比componentWillMount的慢一点的情况，componentDidMount会立即执行，在执行之后会立即执行render方法对页面进行渲染

下面是一官方对于该问题的解释

> componentDidMount() is invoked immediately after a component is mounted. Initialization that requires DOM nodes should go here. If you need to load data from a remote endpoint, this is a good place to instantiate the network request. 





##### 循环遍历

```react
class HelloWord extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            list: []
        }
        this.add = this.add.bind(this)
    }

    componentDidMount() {
        this.setState({
            list: [1, 2]
        })
    }

    render() {
        return <div>
            <p>列表元素：</p>
            <ul>
                {
                    this.state.list.map((item, index) => (
                        <li>{item}</li>
                    ))
                }
            </ul>
            <button onClick={this.add}>点击添加一个列表元素</button>
        </div>
    }

    add() {
        this.state.list.push(parseInt(Math.random() * 1000))
        this.setState({
            list: this.state.list
        })
    }
}

ReactDOM.render(
    <HelloWord/>,
    document.querySelector("#root")
)
```
##### key

在执行上诉代码之后，我们会看到一行报错

>react.js:369 Warning: Each child in an array or iterator should have a unique "key" prop.Check the render method of `Lists`.

这是React提示需要在为我们通过遍历渲染的li添加key值，采用如下操作

```react
<li key={index}>{item}</li>
```

React是不会去渲染之前已经存在的元素，这也是React的高效性的原因之一，key值可以帮助React识别那些元素发生了变化，那些没有变化，从而使React在渲染时保留那些之前已经被渲染的元素

还是上面的代码，我们打开控制台，在为第一个li添加一个临时的color，然后点击添加按钮，可以发现这个li的样式依然被保持了，说明React没用重新渲染该元素

![React不会重复渲染元素](.\img\React不会重复渲染元素.gif)

在设置key值得时候我们可以设置为如数组元素的下标值，但是推荐使用数据的id作为key值，下标值作为key会存在一个问题是如果我们需要对展示的数据重新排序，会导致渲染速度变慢

```react
<li key={item.id}>{item}</li>
```
### 表单

##### 关于value

在react中我们写一个如下的代码：

```react
ReactDOM.render(
    <input type="text" value="react"/>,
    document.querySelector("#root")
)
```

然后我们尝试在输入框中输入文字会发现不论输入什么内容，输入框中会依然保持是react，这是因为在我们创建input的时候，value就已经被react劫持了

react也会特别贴心的进行提示

> Failed prop type: You provided a `value` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultValue`. Otherwise, set either `onChange` or `readOnly`. in input

根据提示，我们可以发现我们需要设置为如下的格式

```react
ReactDOM.render(
    <input type="text" defaultValue="react" />,
    document.querySelector("#root")
)
```

这个时候我们就可以正常的修改表单数据了，同样的会被react劫持的还有一个属性，就是checked，在使用时同样需要转换为React提供的`defaultChecked`

##### 关于双向数据绑定和单项数据绑定

react中是没有双向绑定这个概念的，实际的react其实可以理解为是一个单向的数据绑定，所有的顶层具有的state做为数据流通过props向下传递给子组件，然后在数据改变的时候重新调用render，重而重新渲染页面

