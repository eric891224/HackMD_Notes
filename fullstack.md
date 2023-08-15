# Fullstack Note

###### tags: `programming`

[toc]

# Frontend
## CSS
### Syntax
```css
selector {
    property1: value;
    property2: value;
}
```
### Selectors
1. class selector
    ```css!
    .class-name {
        color: white;
    }
    ```
2. id selector (id is usually unique on a page)
    ```css!
    #id {
        background-color: red;
    }
    ```
3. type selector
    ```css!
    html-tag {
        padding: 10px
    }
    ```
#### selector combinations
1. select element that has multiple attrs simultaneously
    ```css!
    .class1.class2 {
        property: value;
    }

    #id.class {
        property: value;
    }

    html.class#id {
        property: value;
    }

    /* etc... */
    ```
2. put a space between to specify ancestor and child
    ```css!
    .ancestor .child {
        property: value
    }
    ```
    > EX: select a `<p>` that has class attr inside `<div>`
    > ```html
    > <div>
    >     <p class='kkk'>Selected</p>
    >     <p>Not Selected</p>
    >     <span>
    >         <p class='kkk'>Selected</p>
    >     </span>
    > </div>
    > ```
    > ```css
    > div p.kkk {
    >     color: red;
    > }
    > ```
3. to share style between mutiple selectors, use comma
    ```css!
    .big, .small {
        color: green;
    }
    ```
### Advanced Selectors

### Box model
![](https://hackmd.io/_uploads/B1937tMK2.png =500x)
```css!
.box {
    /* content */
    height: 100px;
    width: 100px;
    /* padding */
    padding: 20px;
    /* border */
    border: 10px solid black;
    /* margin */
    margin: 50px;
}
```
#### `box-sizing: border-box`
> Default value for `box-sizing` is `content-box`, 
> which means the `element's width/height = content's width/height + padding + border`
> On the other hand, `border-box` makes the `width/height` properties the actual `element's width/height`, and the rest is automatically computed
```css!
.box {
    width: 100px;
    height: 100px;
    /* border-box makes width and height include content, padding and border */
    box-sizing: border-box;
}
```
### Units
```css!
.box {
    /* %: percent of its parent */
    width: 10%;
    /* vw: percent of screen size width */
    width: 50vw;
    /* vh: percent of screen size height */
    height: 25vh;
    
    /* em: scale of font size from parent */
    width: 1em;    /* if font size is 16px, 1em = 16px */
    /* rem: scale of font size from root document */
    width: 1rem;
}
```
### Position
> all elements' default `position` is `static`
#### `relative`
acts like `static` but one can manipulate its top, left, right, bottom
```css!
.relative-el {
    position: relative;
    left: 10px;
}
```
#### `absolute`
One can manipulate its top, left, right, bottom from the parent's container. **However, the parent needs to be set to `relative` or `absolute`**, or the child would be absolute according to the root document.
```css!
.el-parent {
    position: relative;
}

.absolute-el {
    position: absolute;
    top: 0px;
}
```
#### `fixed`
always positioned with respect to the entire page, even for scrolling
```css!
.fixed-el {
    position: fixed;
    top: 0px;
}
```
#### `sticky`
a combination of both `relative` and `absolute`, and as soon as it scrolls outside of the page, it becomes `fixed`.
```css!
.sticky-el {
    position: sticky;
    top: 0px;
}
```
### Display
```css!
.display-el {
    display: block;
    display: inline;
    display: inline-block;
    display: none;
}
```
#### `block`
the default of all elements, which gives the element as much width as possible and force breakline for next element
#### `inline`
the element takes as less space as possible, and cannot change its width/height. All elements with `inline` prop will try to stay on the same line
#### `inline-block`
acts like `inline` but width/height is changeable

### Flexbox
> better used when one-dimensional layout is needed

in flexbox layout, we need:
* flexbox container
* flexbox items wrapped by a flexbox container
    ```htmlmixed
    <div class='flexbox-container'>
        <div class='flexbox-item flexbox-item1'>
            Flexbox item 1
        </div>
        <div class='flexbox-item flexbox-item2'>
            Flexbox item 2
        </div>
    </div>
    ```
#### flexbox-container
for container, the `display: flex` is needed
```css!
.flexbox-container {
    display: flex
}
```
![](https://hackmd.io/_uploads/ry1KpMBt2.png =500x)

properties: 
1. `flex-direction`
    * `row`: default, **horizontal main axis**, vertical cross axis
    * `column`: **vertical main axis**, horizontal cross axis
2. `justify-content`: determine how items are placed along **main axis**

    * `flex-start`: at the start of the main axis
    * `center`: at the center of the main axis
    * `flex-end`: at the end of the main axis
    * `space-between`: evenly place items along main axis, the first item is placed at start and the last item is placed at the end
    * `space-around`: like `space-between`, but start and end has gaps half size of the space
    * `space-evenly`: evenly place items and spaces along main axis, the start, between and end space are of the same size
    * `stretch`:  extend the items' dimension along main axis to better fill the container
3. `align-items`: the same as `justify-content` but the direction is along **cross axis**

#### flexbox-item
items have properties to override individual style set by the container
1. `flex-shrink`: how the item's dimension change when the screen size is shrinking
    * `0`: disable item shrinking
    * `1~n`: decrease item's dimension n times compare to other items
2. `flex-grow`: how the item's dimension change when the screen size is growing
    * `0`: disable item growing
    * `1~n`: increase item's dimension n times compare to other items
4. `align-self`: override `align-content` set by container

## Javascript must-knows
### passed by value or reference
https://www.youtube.com/watch?v=-hBJz2PPIVE&ab_channel=WebDevSimplified
* string, number, undefined, null... are passed by value
* class, object, array... are passed by reference

### debounce
delay a function until the timer goes off, and reset the timer when state changes.

#### vanilla js
```javascript!
const input = document.querySelector("input")
const defaultText = deocument.getElementById("defaultText")
const debounceText = deocument.getElementById("debounceText")

function debounce(cb, delay = 1000) {
    let timeout
    return (...args) => {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
            cb(...args)
        }, delay)
    }
}

const updateDebounceText = debounce((text) => 
    debounceText.textContent = text
)

input.addEventListener("input", e => {
    // without debounce: render constantly/immediately
    defaultText.textContent = e.target.value
    
    // with debounce: render after input stop 
    updateDebounceText(e.target.value)
})
```
#### lodash
```jsx!
import _ from 'lodash';

const component = () => {
    [debounceState, setDebounceState] = useState("")
    
    const debounce = useCallback(
        _.debounce(
            (newValue) => {
                setDebounceState(newValue)
            },
            500
        )
    ,[])
    
    function handleChange(e) {
        debounce(e.target.value)
    }
    
    return (
        <div className="App">
            <input 
                type="text" 
                onChange={handleChange} 
            />
            <p>Debounced Value: {debouncedState}</p>
        </div>
    )
}
```

### throttle
fire function periodically

#### vanilla js
```javascript!
const input = document.querySelector("input")
const defaultText = deocument.getElementById("defaultText")
const throttleText = deocument.getElementById("throttleText")

function throttle(cb, delay = 1000) {
    let shouldWait = false
    let waitingArgs
    const timeoutFunc = () => {
        if (waitingArgs == null) {
            shouldWait = false
        } else {
            cb(...waitingArgs)
            waitingArgs = null
            setTimeOut(timeoutFunc, delay)
        }
    }
    
    return (...args) => {
        if (shouldWait) {
            waitingArgs = args
            return
        }
        
        cb(...args)
        shouldWait = true
        
        setTimeout(timeoutFunc, delay)
    }
}

const updateThrottleText = throttle((text) => 
    throttleText.textContent = text
)

input.addEventListener("input", e => {
    // without throttle: render constantly/immediately
    defaultText.textContent = e.target.value
    
    // with throttle: render periodically 
    updateThrottleText(e.target.value)
})
```

#### lodash
```jsx!
import _ from 'lodash';

const component = () => {
    const [throttleState, setThrottleState] = useState("")
    
    const throttle = useCallback(
        _.throttle((newValue) => {
            setThrottleState(newValue)
        }),
        500,
        {
            'leading': true,    // fire on leading edge of timer
            'trailing': true    // fire on trailing edge of timer
        }
    ,[])
}
```

## React Hooks
### `useState()`
```jsx
import React, { useState } from 'react';
```
#### basic usage
```jsx
const [stateName, setStateFunc] = useState(defaultState)

// Ex
const [count, setCount] = useState(0) // count = 0
setCount(1) // count => 1
```
#### advanced usage
To access previous state, one shouldn't use something like `setCount(count - 1)`
Instead, pass a prevCount argument to `setStateFunc`
```jsx
// access previous state
setCount(prevCount => prevCount - 1) //count => -1
```
Default state passed to initialize the state can also be a function
**The function should be executed only once**
```jsx
// the arrow function will be only executed once
const [count, setCount] = useState(() => {
    // some code...
    return 0
})
```
or
```jsx
// this should be defined outside the component
function countInit() {
    // some code
    return 0
}
// inside the component
const [count, setCount] = useState(() => countInit())
```
Sometimes one may want to **use the default state function after every render**, do this:
```jsx
// directly call the init function without arrow function
const [count, setCount] = useState(countInit())
```
If using an object state, make sure to unpack the object in `setStateFunc`
```jsx
const [color, setColor] = useState({r: 0, g: 0, b: 0})

setColor(prevState => {
    return {...prevState, r: prevState.r + 1}
})
```

### `useEffect()`
```jsx
import React, { useEffect } from 'react';
```
#### basic usage
execute code after every render
```jsx
useEffect(() => {
    // code to be executed
})
```
execute code after any element in the array change
```jsx
useEffect(() => {
    // code to be executed
}, [something_that_may_change])
```
execute code **onMount** (done only once)
```jsx
useEffect(() => {
    // code to be executed
}, [])    // pass an empty array
```
#### advanced usage
resource cleanup using return in useEffect
when the App component is deleted, the function returned by useEffect will be executed
```jsx
const App = () => {
    const [windowWidth, setWindowWidth] = useState(window.innerWidth)
    
    const handleResize = () => {
        setWindowWidth(winsow.innerWidth)
    }
    
    // add event listener when onMount
    useEffect(() => {
        window.addEventListener('resize', handleResize)
        
        // cleanup when App is deleted to avoid performance slowdown
        return () => {
            window.removeEventListener('resize', handleResize)
        }
    }, [])
}
```
* if using return with elements passed, the return code will be executed before the code in the next cycle be done
* use `useLayoutEffect()` when the updates are based on Dom Element postion's measurement (`useEffect` runs asynchronously, while `useLayoutEffect` runs synchronously)

### `useMemo()`
```jsx
import React, { useMemo } from 'react';
```
whenever any state is updated, the component will be re-render
if a very slow function is included in the component, it will be executed at every render, and hence the performance slowdown
```jsx
const [number, setNumber] = useState(0)
const [dark, setDark] = useState(false)
doubleNumber = slowFunction(number) // very slow
const themeStyle = {
    backgroundColor: dark ? 'black' : 'white',
    color: dark ? 'white' : 'black'
}

function slowFunction(number) {
    for (let i=0; i<1000000000; i++) {}
    return number*2
}
```
#### basic usage
`useMemo()` will cache the previous slow function's output to avoid this problem
```jsx
const [number, setNumber] = useState(0)
const [dark, setDark] = useState(false)
// only recalculate the slowFunction on number change
doubleNumber = useMemo(() => {
    return slowFunction(number)
}, [number])
const themeStyle = {
    backgroundColor: dark ? 'black' : 'white',
    color: dark ? 'white' : 'black'
}
```
#### advanced usage
**referential equality**: every time the component re-render, the object defined in the component will be recreated (eg. themeStyle)
despite the same name, these two objects are not the same reference

`useEffect` will monitor reference change instead of value change
if one uses `useEffect` to monitor themeStyle, the code inside will be executed (which is not as expected) due to reference changes
```jsx
const [number, setNumber] = useState(0)
const [dark, setDark] = useState(false)
doubleNumber = useMemo(() => {
    return slowFunction(number)
}, [number])
// themeStyle will be different reference at every render
const themeStyle = {
    backgroundColor: dark ? 'black' : 'white',
    color: dark ? 'white' : 'black'
}
// code inside will be executed whether themeStyle changes or not
useEffect(() => {
    console.log('theme changed')
}, [themeStyle])
```
`useMemo()` can solve this problem by returning previous reference of an object
```jsx
const [number, setNumber] = useState(0)
const [dark, setDark] = useState(false)
doubleNumber = useMemo(() => {
    return slowFunction(number)
}, [number])
// themeStyle will be different reference only when dark changes
const themeStyle = useMemo(() => {
    return {
        backgroundColor: dark ? 'black' : 'white',
        color: dark ? 'white' : 'black'
    }
}, [dark])
// code inside will be executed only when themeStyle changes
useEffect(() => {
    console.log('theme changed')
}, [themeStyle])
```
### `useCallback()`
```jsx
import React, { useCallback } from 'react';
```
similar to `useMemo()`, `useCallback()` is used to avoid **referential equality** issue **for function only**
```jsx
// App.js
// getItem will be recreated at every render
// hence different reference
const getItem = () => {
    return [number, number+1, number+2]
}

// List.js
// we call getItem() using useEffect
// use useEffect to monitor getItem()'s reference
const [items, setItems] = useState([])
// this will be executed every render even if the function is the same
useEffect(() => {
    setItems(getItem())
}, [getItem])
```
#### basic usage
similar to `useMemo()`, `useCallback()` cache the previously returned function
```jsx
// recreate the function only on number change
const getItem = useCallback(() => {
    return [number, number+1, number+2]
}, [number])
```
#### advanced usage
since `useCallback()` return a function, we could pass parameters to the function
this is the second difference between `useMemo()` and `useCallback()`
```jsx
// App.js
// recreate the function only on number change
// additional parameters could be passed
const getItem = useCallback((para) => {
    return [number+para, number+para+1, number+para+2]
}, [number])

// List.js
// we could pass arguments to the function
useEffect(() => {
    setItems(getItem(5))
}, [getItem])
```
### Summary (`useEffect`, `useMemo`, `useCallback`)
| Hook        | return        | usage                                                | when to use                         |
| ----------- | ------------- | ---------------------------------------------------- |:----------------------------------- |
| useEffect   | clean up code | react on monitored value/reference changed           | ...                                 |
| useMemo     | value         | cache value & referential equality problem           | when function takes time to **execute** |
| useCallback | function      | cache function to avoid referential equality problem | when function takes time to be **created**     |

### `useRef()`
```jsx
import React, { useRef } from 'react';
```
#### basic usage
similar to `useState()`, but **the state persists between renders**
```jsx
const [name, setName] = useState('')
const [renderCount, setRenderCount] = useState(1)

// this will cause an infinite loop
useEffect(() => {
    setRenderCount(prevRenderCount => prevRenderCount + 1)
})
```
use `useRef` to solve the problem, which will return an object `{ current: '...' }`
```jsx
const [name, setName] = useState('')
const renderCount = useRef(1) // {current: 1}

// this won't trigger another render
useEffect(() => {
    renderCount.current = renderCount.current + 1
})
```
#### advanced usage
use `useRef()` to reference a Dom element
```jsx
const [name, setName] = useState('')
const inputRef = useRef() // {current: ReactDomElement}

const focus = () => {
    inputRef.current.focus()
}

return (
    <input ref={inputRef} value={name} onChange={e => setName(e.target.value)}/>
    <button onClick={focus}>Focus</button>
)
```
use `useRef()` to store previous state value
```jsx
const [name, setName] = useState('')
const prevRef = useRef('') // {current: ''}

useEffect(() => {
    prevRef.current = name
}, [name])

return (
    <input ref={inputRef} value={name} onChange={e => setName(e.target.value)}/>
    <div>current name: {name}</div>
    <div>previous name: {prevRef.current}</div>
)
```

# Backend
## Cors


# Node pkg
## pm2
```shell
## show pm2 running process
$ pm2 [ls|list|status]

## start process in daemon
## EX: yarn start
$ pm2 start "yarn start" --name app_name
$ pm2 start "npm run start" --name app_name
## $ pm2 start yarn --name app_name -- start
## EX: yarn server
$ pm2 start "yarn server" --name app_name
$ pm2 start "npm run server" --name app_name
## $ pm2 start yarn --name app_name -- server

## kill then restart process
$ pm2 restart app_name

## stop process
$ pm2 stop app_name

## stop and delete process
$ pm2 delete app_name
```
instead of `app_name`, one can use `all` to infer all processes, or id to specify the process
### startup script
```shell
## tell pm2 to maintain startup process
$ pm2 startup
## start desired applications
$ pm2 start...
## save desired startup list
$ pm2 save


## to disable startup
$ pm2 unstartup
## then copy-paste the console message
## if one need to update startup script
## just use pm2 startup again
```