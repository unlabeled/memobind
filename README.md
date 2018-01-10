## BindCache ##

It **caches/memoizes** result of binding function with its context and args. For the same set it will return identical result every next time.


## Motivation ##
It solves the problem of unnecessary renders for React in cases of usage arrow function or `Function.prototype.bind` in render method. It also keep up with `jsx-no-bind` rule which is used to detact such issues.

```javascript
class MyComponent extends Component {
    onChange = () => undefined

    render () {
        const { param } = this.props
        return (
            <div>
                <SomeComponent someParam={"value"}>
                <OtherComponent callback={this.onChange.bind(this, param, "MyComponent")}/>
            </div>    
        )
    }
}
```
This code will make `OtherComponent` to render every time this render function will be fired.

## Api ##

If you need to bind context:
```typescript
bind(context: any, fn: () => void, args?: any | Array<any>)
```

If you don't need context:
```typescript
bindArgs(fn: () => void, args?: any | Array<any>)
```

- **context** — context
- **fn** — function to bind
- **args** — any number of arguments of any type 


## Usage ##

```shell
npm install bindcache
```

```javascript
import { bind } from "bindcache"

class MyComponent extends Component {
    onChange(param, component) {
        // ...
    }

    render () {
        const { param } = this.props
        return (
            <div>
                <SomeComponent someParam={"value"}>
                <OtherComponent callback={bind(this, this.onChange, param, "MyComponent")}/>
            </div>    
        )
    }
}
```
In the cases there you don't need the context of the function or it is already present/binded you can use `bindArgs` util instead. The previous code can be written like this.

```javascript
import { bindArgs } from "bindcache"

class MyComponent extends Component {
    onChange = (param, component) => {
        // ...
    }

    render () {
        const { param } = this.props
        return (
            <div>
                <SomeComponent someParam={"value"}>
                <OtherComponent callback={bindArgs(this.onChange, param, "MyComponent")}/>
            </div>    
        )
    }
}
```

## How it works ##

The util caches result of binding in native WeakMap so the items with no references can be garbage collected.

## Tips ##

It is not suggested to use this util instead of `Function.prototype.bind` in every case. The original bind function is still faster at least. For example if you need to bind context with no args or your params are static for the component you can bind it one time in constructor function. But it will help you great deal in case of unnecessary renders.