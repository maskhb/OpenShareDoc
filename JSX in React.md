
# React Basics Quick Look

## 1. What is JSX?

Here is an example of JSX syntax:
```javascript
const element = <h1>Hello, world!</h1>;
```
This syntax is neither HTML nor a string, but is called JSX, which is a syntax used in React to describe UI and styles. JSX will eventually be compiled into valid JavaScript statements for calling (the compiler parses JS syntax when encountering `{`, and HTML rules when encountering `<`).



## 2. Embedded Expressions

You can use curly braces `{}` to embed any valid JavaScript expression in JSX, such as `2 + 2`, `user.firstName`, and `formatName(user)`. For example:
```javascript
const user = {
    firstName: 'Bin',
    lastName : 'Hong'
};

const elem = (
    <h1>Hello, {formatName(user)}</h1>
);

ReactDOM.render(
    element,
    document.getElementById('app')
)
```



## 3. JSX is also an Expression

 JSX itself is an expression, so it can be used like other expressions to assign to a variable, be a function argument, or be a function return value, etc. For example:
```javascript
function getGreeting(user) {
    if (user) {
        return <h1>Hello, {formatName(user)}</h1>
    }
    return <h1>Hello, Guest!</h1>;
}
```
**Note**: 1. In JSX, do not use quotes when declaring attributes. If quotes are used when declaring attributes, they will be parsed as strings rather than expressions. For example:
```javascript
<div firstName="{user.firstName}" lastName={user.lastName}></div>
```
This will be parsed as:
```javascript
<div firstName="{user.firstName}" lastName="Hong"></div>
```
Therefore, when using string literals, quotes can be used, but when they need to be parsed as expressions, quotes should not be used. 2. Some attribute names need to be specially handled in JSX. For example, `class` should be replaced with `className`, and `tabindex` should be replaced with `tabIndex`. This is because `class` is a reserved word in JavaScript. At the same time, attributes should be named using `camelCase` instead of HTML attribute naming conventions. 3. JSX itself has undergone anti-injection processing. For HTML code that is not explicitly written, it will not be parsed as HTML DOM, and ReactDOM will treat them all as strings and convert them into strings before rendering, thus preventing XSS attacks. 4. If a JSX tag is closed, then the ending `/>` needs to be used. In addition, JSX tags can be nested, which is the same as in HTML.



## 4. What is JSX Essence?

JSX is compiled by babel, and babel actually compiles JSX to `React.createElement()` calls. The following is an example of JSX code:
```javascript
const element = (
    <h1 className="greeting">
        Hello, world!
    </h1>
);
```
This is equivalent to the following statement:
```javascript
const elem = React.createElement(
    'h1',
    {className: 'greeting'},
    'Hello, world!'
);
```
The `React.createElement()` method performs some bug checks first, and then returns an object similar to the following example:
```javascript
const element = {
    type: 'h1',
    props: {
        className: 'greeting',
        children: 'Hello, world'
    }
}
```
This type of object is called a `React element`, which represents everything that appears on the screen. React builds the DOM and keeps the data and UI in sync by reading these objects.



## 5. Element Rendering

An element (`elements`) is the smallest unit that makes up a React application, and an element describes what you want to see on the screen, such as:
```javascript
const element = <h1>Hello, world</h1>;
```
Unlike DOM elements, React elements are pure objects and have low creation costs. React also optimizes them to only update the DOM when necessary. In addition, the concept of elements and components is different, and components are made up of elements.



## 6. Rendering Elements into the DOM

In React, `ReactDOM.render()` is used to render React elements into a DOM. For example:
```javascript
ReactDOM.render(
    element,
    document.getElementById('root')
)
```
React elements are immutable, so once an element is created, its content or attributes cannot be changed. An element is like a frame in an animation, representing the UI at a certain point in time. If you need to use elements to create a variable UI interface, you need to use `setInterval`, such as:
```javascript
function tick() {
    const element = (
        <div>Now is {new Date().toLocaleTimeString()}</div>
    );
    ReactDOM.render(
        element,
        document.getElementById('root')
    );
}
setInterval(tick, 1000);
```
In actual development, most React applications only call `ReactDOM.render()` once, so a better way is to use `stateful components`.



## 7. Components and Props

Components (`component`) can divide the UI into independent and reusable parts, so we only need to focus on building each individual component. Conceptually, components are like functions: they accept any input (called props), and return React elements. There are two ways to define components in React: function definition and class definition.



### 1. Function Definition Component

This is the simplest way to define a component, just like writing a JavaScript function, such as:
```javascript
function Welcome (props) {
    return <h1>Hello, {props.name}</h1>;;
}
```



### 2. Class Definition Component

You can also use ES6 classes to define a component, as follows:
```javascript
class Welcome extends React.Component {
    render () {
        return <h1>Hello, {this.props.name}<h1>;
    }
}
```
This method is more flexible than the function definition method.



### 3. Component Rendering

Previously, the React elements we encountered only presented a single DOM tag, such as:
```javascript
const element = <div></div>
```
However, React elements can also be user-defined components, such as:
```javascript
const element = <Welcome name="Tom" />
```
In the `Welcome` component, a property `name="Tom"` is declared, and this property is passed to the component in the form of `props.name`. The following is the way to do it:
```javascript
function Welcome (props) {
    return <h1>Hello, {props.name}</h1>;
}
```
At this point, for the following code:
```javascript
ReactDOM.render(
    <Welcome name="Billy" />,
    document.getElementById('root')
)
```
It will finally be presented as `<h1>Hello, Billy</h1>`. During this process, the following things happen:
- Call `ReactDOM.render()` on the `<welcome name="Billy"></welcome>` element
- React calls the `Welcome` component with `{ name: 'Billy' }` as the props argument
- `Welcome` completes the rendering and returns the `<h1>Hello, Billy</h1>` element
- ReactDOM calculates the minimum update cost and then updates the DOM



### 4. Combining Components

Components can be combined, that is, components can refer to other components within them, such as:
```javascript
function Welcome (props) {
    return <h1>Hello, {props.name}</h1>;
}

function App () {
    return (
        <div>
            <Welcome name="Tom" />
            <Welcome name="Jack" />
            <Welcome name="Mike" />
        </div>
    )
}

ReactDOM.render(
    <App />,
    document.getElementById('root')
)
```
**Note**: In React, components must return a single root element, which is why the `App` component needs to be wrapped in a `<div>` tag. The following approach is incorrect (because it has three root elements):
```javascript
function App () {
    return (
        <Welcome name="Tom" />
        <Welcome name="Jack" />
        <Welcome name="Mike" />
    )
}
```



### 5. Properties are Read-Only

Consider the following situation:
```javascript
function sum (a, b) {
    return a + b;
}
```
This function is called a `pure function`: it does not modify its input values and always returns the same result for the same input. On the contrary, a `non-pure function` is one that modifies its input parameters within the function. For example:
```
function withdraw (account, amount) {
    account.total -= amount;
}
```
`non-pure function` modify input parameters within the function. In React, whether a component is defined using a function or a class, we should not modify its own properties (`props`). Although React is flexible, it has a strict rule: all React components must use their props like pure functions.



## 8. State and Lifecycle

Using class-defined components has some additional benefits, such as having local state. Here is an example of a class-defined component:
```javascript
class Clock extends React.Component {
    render () {
        return (
            <div>
                <h1>Hello, world!</h1>
                <h2>Now is {this.props.date.toLocaleTimeString()}</h2>
            </div>
        );
    }
}
```
It is important to note that:
- The class name is the component name (for both function-defined and class-defined components, the first letter of the component name must be UpperCase, and it inherits from `React.Component`)
- Use the `render()` method to return the content to be rendered

State belongs to the component itself. We can initialize the state in the constructor of the class, such as:


### 1. Adding State to the Class

```
constructor (props) {
    super(props)
    this.state = {
        date: new Date()
    }
}
```
Now we can use `this.state.xxx` in the `render()` function to refer to a state.

In an application, there are often many components. When a component is destroyed, it is very important to reclaim and release the resources it occupies. In the case of the `clock application`, we need to set a timer when it is first rendered to the DOM, and we need to clear this timer when the corresponding DOM is destroyed. In this case, React provides lifecycle hook functions for us to use. In React, there are three phases of the lifecycle: 1) `Mount` has been inserted into the real DOM 2) `Update` is being re-rendered 3) `Unmount` has been removed from the real DOM. Corresponding to these three phases, there are lifecycle hook functions:
- `componentWillMount`
- `componentDidMount`
- `componentWillUpdate(newProps, nextState)`
- `componentDidUpdate(prevProps, prevState)`
- `componentWillUnmount()`

In addition, there are two special state handling functions:
- `componentWillReceiveProps(nextProps)` called when a loaded component receives new parameters
- `shouldComponentUpdate(nextProps, nextState)` called when the component determines whether to re-render

Therefore, we can implement a clock application based on lifecycle hook functions, as follows:
```javascript
class Clock extends React.Component {
    constructor (props) {
        super(props);
        this.state = {
            date: new Date()
        }
    }
    tick () {
        this.setState({
            date: new Date()
        });
    }
    componentDidMount () {
        this.timerId = setInterval(() => {
            this.tick()
        }, 1000);
    }
    componentWillUnmount () {
        clearInterval(this.timerId);
    }
    render () {
        return (
            <div>Now is {this.state.date.toLocaleTimeString()}</div>
        );
    }
}
```
It is important to note that: 1) `state` that is not used in `render()` should not be declared in `state`. 2) `this.state.xxx = xxx` should not be used directly to change the value of a `state`, but rather `this.setState()`. For example:
```javascript
setName () {
    this.setState({
        name: 'Billy'
    })
}
```
`this.setState()` automatically overrides the corresponding property in `this.state` and triggers `render()` to re-render. 3) State updates may be asynchronous. React can combine multiple `setState()` calls into a single call to improve performance. Since `this.props` and `this.state` may be asynchronously updated, you should not rely on their values to calculate the next state. In this case, you can pass a function to `setState`, such as:
```javascript
this.setState((prevState, props) => ({
    counter: prevState.counter + props.increment
}));
```


## 9. Event Handling

React elements handle events similar to DOM elements, but there are some differences. Here are the differences:
1. React events use `camelCase` naming ( `onClick` instead of `onclick`).
2. In JSX, we pass the event handler instead of a string. For example, in HTML:
```javascript
//JS
<button onclick="increment()">ADD</button>
```
In React, the description would be:
```javascript
//JSX
<button onClick={increment}>ADD</button>
```
Another difference is that in native DOM, we can return `false` to prevent the default behavior, but this does not work in React. In React, we need to explicitly use `preventDefault()` to prevent the default behavior. For example:
```javascript
function ActionLink () {
    function handleClick (e) {
        e.preventDefault();
        alert('Hello, world!');
    }

    return (
        <a href="#" onClick={handleClick}>Click Me</a>
    );
}
```
Here, the `event` in the event callback function is specially processed by React (following the W3C standard), so we can use it with confidence without worrying about cross-browser compatibility issues. **Note**: When using event callback functions, we need to pay special attention to the `this` binding issue, because in React, **except for the constructor and lifecycle hook functions where `this` is automatically bound to the current component, in other cases it is not automatically bound to `this`point to the current component.** Therefore, we need to pay attention to binding `this` ourselves. Usually, in a class component, when using event callback functions, we need to bind the `this`point to the callback method in the `constructor` of the component, such as:
```javascript
class Counter extends React.Component {
    constructor (props) {
        super(props);
        this.state = {
            counter: 0
        }
        // Here the bindings point to 
        this.increment = this.increment.bind(this);
    }
    increment () {
        this.setState({
            counter: this.state.counter + 1
        });
    }
    render () {
        return (
            <div>
                The counter now is: {this.state.counter}
                <button onClick={this.increment}>+1</button>
            </div>
        );
    }
}
```
Of course, we also have another way to use **arrow functions** to bind the `this`pointer, which is to use the **experimental** property initialization syntax, such as:
```javascript
class Counter extends React.Component {
    increment: () => {
        this.setState({
            counter: this.state.counter + 1
        });
    }
    //...
}
```

3) Pass parameters to event handlers. We can pass additional parameters to event handlers in two ways:
```javascript
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

It should be noted that when using arrow functions, the parameter `e` needs to be explicitly passed, while when using `bind`, it does not need to be explicitly passed ( `e` will be passed as the **last parameter** to the event handler).



## 10. Conditional Rendering

In React, we can create different components to encapsulate the functionality we need. We can also conditionally render only a portion of the content of a component based on its state. Conditional rendering is designed for this purpose. In React, we can write conditional rendering statements as we would in JavaScript, such as:
```javascript
function Greet(props) {
    const isLogined = props.isLogined; // The props of the functional component are passed in as parameters
    if (isLogined) {
        return <div>Hello!</div>;
    }
    return <div>Please sign in</div>;
}

ReactDOM.render(
    <Greet isLogined={true} />,
    document.getElementById('root')
);
```

This will render:
```javascript
<div>Hello!</div>
```

### 1. Store elements in variables
We can also use variables to store elements, such as:
```javascript
function LogBtn(props) {
    var button;
    const isLogined = props.isLogined;
    if (isLogined) {
        button = <button>Logout</button>
    } else {
        button = <button>Login</button>
    }
    return <div>You can {button}</div>;
}

ReactDOM.render(
    <LogBtn isLogined={false} />,
    document.getElementById('root')
);
```

### 2. Use the && operator for rendering
Because of the nature of the JavaScript syntax regarding the `&&` operator, we can also use the `&&` operator to accomplish conditional rendering, such as:
```javascript
function LogBtn(props) {
    var button;
    const isLogined = props.isLogined;
    return (
        <div>Hello
        {!isLogined && ( <button>Please Login</button> )}
        </div>
    )
}
```
When `props.isLogined` is `false`, it will render:
```javascript
<div>Hello <button>Please Login</button></div>
```

### 3. Use the ternary operator for rendering
We may have noticed that JSX can be used as an expression, so we can naturally use the ternary operator for rendering, such as:
```javascript
function LogBtn (props) {
    const isLogined = props.isLogined;
    return (
        <div>You can 
            <button>{isLogined? 'Logout' : 'Login'}</button>
        </div>
    )
}
```

### 4. Prevent the entire component from rendering

Sometimes, we may want to prevent the entire component from rendering, rather than just partially. In this case, we can return `null` in the `render()` function to achieve the desired effect, such as:
```javascript
function LogBtn (props) {
    const isLogined = props.isLogined;
    const isShow = props.isShow;
    if (isShow) {
        return (
            <div>You can 
                <button>{isLogined? 'Logout' : 'Login'}</button>
            </div>
        )
    }
    return null;
}
```
**Note**: Returning `null` from a component does not affect the triggering of the component's lifecycle, such as `componentWillUpdate` and `componentDidUpdate`.

## 11. List Rendering and Keys

In JavaScript, we can use the `map()` function to operate on an array list, such as:
```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(number => number*2);
console.log(doubled); // get[2, 4, 6, 8, 10]
```
Similarly, in React, we can use `map()` for list rendering, which relies on React's ability to directly render virtual DOM arrays. For example:
```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map(number => {
    return (
        <li>{number}</li>
    )
});

ReactDOM.render(
    <ul>{listItems}</ul>,
    {/*
        The above code is equivalent to
    <ul>{
        [
            <li>{1}</li>
            <li>{2}</li>
            <li>{3}</li>
            <li>{4}</li>
            <li>{5}</li>
        ]
    }</ul>,

    */}
    document.getElementById('root')
)
```

This will result in:
```html
<ul>
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>
```

Of course, we can also encapsulate this functionality better, such as:
```javascript
function NumberList (props) {
    const numbers = props.numbers;
    const listItems = numbers.map(number => {
        return (
            <li>{number}</li>
        )
    });

    return <ul>{listItems}</ul>
}
```

When we run this code, we find that the console prompts: `Each child in an array or iterator should have a unique "key" prop`. To solve this problem, we need to assign a `key` to each item in the list. Usually, we can use the following methods to provide `key`:
- Use the ID of the data item itself, such as:
- Use the index subscript (`index`), such as:
```javascript
const listItems = numbers.map((number, index) => {
    <li key="{index}">{number}</li>
});
```
However, React does not recommend using index subscripts in lists that need to be reordered, as it can become slow.

**Note**: Only when an item needs to be distinguished from its siblings within an array do we need to use `key`. `key` does not need to be globally unique, only unique within an array. `key` serves as a hint for React and is not passed on to components. If we need the same value within a component, we can pass it under a different name, such as:
```javascript
const content = posts.map(post => (
    <post key="{post.id}" id="{post.id}" title="{post.title}">
));
</post>
```

## 12. Forms

Forms in React are different from regular DOM elements in that form elements are designed to maintain their own internal state. In React,mutable states are typically stored in the component's `this.state`, and can only be updated using the `setState()` method.

### 1. Controlled Components

In HTML, `<input>`, `<textarea>`, and `<select>` form elements maintain their own state and update based on user input. However, in React, variable states are typically stored in the component's `this.state`, and can only be updated using the `setState()` method, such as:
```javascript
class NameForm extends React.Component {
    constructor (props) {
        super(props);
        this.state = {
            value: ''
        }
        this.handleChange = this.handleChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }
    handleChange (event) {
        this.setState({
            value: event.target.value
        });
    }
    handleSubmit (event) {
        alert('Your name is '+this.state.value);
        event.preventDefault();
    } 
    render () {
        return (
            <form onSubmit={this.handleSubmit}>
            Name: <input type="text" value={this.state.value} onChange={this.handleChange} />
            <input type="submit" value="Submit" />
            </form>
        )
    }
}
```
Unlike in HTML, where `<textarea>` is written as `<textarea></textarea>`, in React, it is written as `<textarea value...></textarea>`. For HTML `<select>` tags, the common practice is:
```html
<select>
    <option value="A">A</option>
    <option value="B" selected>B</option>
    <option value="C">C</option>
</select>
```
However, in React, it is not necessary to add `selected` to the option that needs to be selected. Instead, only a value needs to be passed in, and the corresponding option will be automatically selected, such as:
```javascript
<select value="C">
    <option value="A">A</option>
    <option value="B">B</option>
    <option value="C">C</option>
</select>
```
In this example, option C will be selected by default.

### 2. Solution for Multiple Inputs

Usually, a form has multiple inputs, and if we add event handlers for each input, it can become quite cumbersome. A better solution is to use the `name` attribute, and then select what to do based on `event.target.name`. For example:
```javascript
class Form extends React.Component {
    constructor (props) {
        super(props);
        this.state = {
            name: '',
            gender: 'male',
            attend: false,
            profile: ''
        };
        this.handleInputChange = this.handleInputChange.bind(this);
        this.handleSubmit = this.handleSubmit.bind(this);
    }
    handleInputChange (event) {
        const target = event.target;
        const value = target.type==='checkbox'? target.checked : target.value;
        const name = target.name;
        this.setState({
            [name]: value
        });
    }
    handleSubmit (event) {
        this.setState({
            profile: `name：${this.state.name}，${this.state.gender}，${this.state.attend? 'will' : 'will not'} attend activity`
        });
        event.preventDefault();
    } 
    render () {
        return (
            <form>
            <p>name：<input name="name" value={this.state.name} onChange={this.handleInputChange} /></p>
            <p>gender：
                <select name="gender" value={this.state.gender} onChange={this.handleInputChange}>
                    <option value="male">male</option>
                    <option value="female">female</option>
                </select>
            </p>
            <p>participation：<input name="attend" type="checkbox" onChange={this.handleInputChange} checked={this.state.attend} /></p>
            <input type="submit" value="Submit" onClick={this.handleSubmit} />
            <p>profile: {this.state.profile}</p>
            </form>
        )
    }
}
```

### 3. Uncontrolled Components

In most cases, using `controlled components` to implement forms is preferred. In a `controlled component`, form data is handled by the React component. If you want form data to be handled by the DOM (i.e., data is not stored in the React state, but in the DOM), then you can use `uncontrolled components`. To use `uncontrolled components`, you can simply use `ref` without having to write event handlers for each state update. For example:
```javascript
class NameForm extends React.Component {
    constrcutor(props) {
        super(props)
    }
    handleSubmit(event) {
        console.log('A name was submitted: ', this.input.value)
        event.preventDefault()
    }
    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                Name: <input type="text" ref={input => this.input = input} />
                </label>
                <input type="submit" value="submit" />
            </form>
        )
    }
}
```

For `uncontrolled components`, if you want to specify a default value, you can use `defaultValue`, such as:
```javascript
<input type="text" defaultValue="Hello" ref={input => this.input = input} />
```
Correspondingly, for `type="checkbox"` and `type="radio"`, use `defaultChecked`


## 13. State hoisting

When several components need to share state data, state hoisting can be used. The core idea is to extract the data and manage it in the nearest common parent component, which then passes the state down to its children via props. For example, to implement a currency conversion component, we can follow this approach:

```javascript
function USD2RMB (amount) {
    return amount * 7.20;
}

function RMB2USD (amount) {
    return amount * 0.14;
}

function convert (amount, typeFn) {
    return typeFn(amount);
}
```

In this situation, where we want the value in the USD input form to update synchronously when a value is entered in the RMB form, it would be difficult to achieve if the RMB component manages its own state. Therefore, we need to elevate this state to the parent component for management. Here's how we can do it:

```javascript
class CurrencyInput extends React.Component {
    constructor (props) {
        super(props)
    }
    handleChange =(event)=> {
        this.props.onInputChange(event.target.value)
    }
    render () {
        const value = this.props.value
        const type = this.props.type
        return (
            <p>{type}: <input type="text" value={value} onChange={this.handleChange} /></p>
        );
    }
}
```

Finally, we define a common parent component:

```javascript
class CurrencyConvert extends Component {
    constructor (props) {
        super(props);
        this.state = {
            type: 'RMB',
            amount: 0
        }
    }
    handleRMBChange =(amount)=> {
        this.setState({
            type: 'RMB',
            amount
        });
    }
    handleUSDChange =(amount)=> {
        this.setState({
            type: 'USD',
            amount
        });
    }
    render () {
        const type = this.state.type;
        const amount = this.state.amount;
        const RMB = type==='RMB'? amount : convert(amount, USD2RMB);
        const USD = type==='USD'? amount : convert(amount, RMB2USD);
        return (
            <div>
                <p>Please Input:</p>
                <CurrencyInput type="RMB" value={RMB} onInputChange={this.handleRMBChange} />
                <CurrencyInput type="USD" value={USD} onInputChange={this.handleUSDChange} />
            </div>
        );
    }
}
```

## 14. Composition vs Inheritance

React encourages the use of composition rather than inheritance. Here are some situations where React suggests using composition:
1. When the parent component does not know what the content of the child component might be, `props.children` can be used, such as:
```javascript
function Article (props) {
    return (
        <section>
            <aside>sidebar</aside>
            <article>{props.children}</article>
        </section>
    );
}

function App () {
    return (
        <Article>This is an article</Article>
    );
}
```

This will render:
```html
<section>
    <aside>sidebar</aside>
    <article>This is an article</article>
</section>
```
2. We can also customize the name, because JSX is actually converted into valid JavaScript expressions, so we can have:
```javascript
function Article (props) {
    return (
        <section>
            <aside>{props.aside}</aside>
            <article>{props.children}</article>
        </section>
    );
}

function App () {
    return (
        <Article aside={
            <h1>This is a sidebar</h1>
        }>This is an article</Article>
    );
}
```

This will render:
```html
<section>
    <aside><h1>This is a sidebar</h1></aside>
    <article>This is an article</article>
</section>
```

On Facebook's website, thousands of components are used, but in practice, no cases have been found where inheritance is necessary to solve the problem. Properties and composition provide a clear and safe way to customize the style and behavior of components. Components can accept any elements, including basic data types, React elements, and functions. If you need to reuse UI-agnostic functionality between components, it should be extracted into a separate JavaScript module so that functions, objects, and classes can be imported and used without extending components.# React Basics Quick Look


React encourages the use of composition rather than inheritance. Here are some situations where React suggests using composition:
1. When the parent component does not know what the content of the child component might be, `props.children` can be used, such as:
```javascript
function Article (props) {
    return (
        <section>
            <aside>sidebar</aside>
            <article>{props.children}</article>
        </section>
    );
}

function App () {
    return (
        <Article>This is an article</Article>
    );
}
```

This will render:
```html
<section>
    <aside>sidebar</aside>
    <article>This is an article</article>
</section>
```
2. We can also customize the name, because JSX is actually converted into valid JavaScript expressions, so we can have:
```javascript
function Article (props) {
    return (
        <section>
            <aside>{props.aside}</aside>
            <article>{props.children}</article>
        </section>
    );
}

function App () {
    return (
        <Article aside={
            <h1>This is a sidebar</h1>
        }>This is an article</Article>
    );
}
```

This will render:
```html
<section>
    <aside><h1>This is a sidebar</h1></aside>
    <article>This is an article</article>
</section>
```

On Facebook's website, thousands of components are used, but in practice, no cases have been found where inheritance is necessary to solve the problem. Properties and composition provide a clear and safe way to customize the style and behavior of components. Components can accept any elements, including basic data types, React elements, and functions. If you need to reuse UI-agnostic functionality between components, it should be extracted into a separate JavaScript module so that functions, objects, and classes can be imported and used without extending components.
