# Muuri-React

> A React friendly API wrapper arround [Muuri](https://github.com/haltu/muuri)

[![NPM](https://img.shields.io/npm/v/muuri-react.svg)](https://www.npmjs.com/package/muuri-react) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

## Acknowledgements

**([demo](https://codesandbox.io/s/muuri-react-pqtbx) avaible on codesandbox)**

A React `component` is provided with the following features:
- The muuri instance and the grid element are `generated` (on mount) and `destroyed` (on unmount) `automatically`.
- Access to the `muuri` instance using the React [ref](https://reactjs.org/docs/refs-and-the-dom.html#creating-refs) API.
- The `items` are passed as `children`. To add or remove items you can simply re-render the component with different children. The component will find out the `added`/`removed` items and it will take care to add/remove them from the muuri instance.
    - If you want to see the adding animation remember to set the display property of the item to `none`.
    - When you use a list of components as children it's is critical to add the [key](https://reactjs.org/docs/lists-and-keys.html) prop to each component <br> (**DON'T USE THE [INDEX AS KEY](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)**). 
- Provide `sort`/`filter` props. The grid will be automatically sorted/filtered when the corresponding prop change or an items is added.
    - If you don't provide a primitive value (e.g. array, function) you can memoize it to avoid useless sorting/filtering. 
- Provide `onMount`/`onUnmount` props.

## Install

```bash
npm install --save muuri-react
```

## Usage

You can check the codesandbox [demo](https://codesandbox.io/s/muuri-react-pqtbx).
```jsx
import React, { useState, useRef } from 'react'
import { MuuriComponent } from 'muuri-react';

const App = () => {
  // Get the Muuri instance
  const muuriRef = useRef()
  // states
  const [sort, setSort] = useState('By Name')
  const [filter, setFilter] = useState('By Color')
  const [items, setItems] = useState([
    { id: 1 },
    { id: 2 },
    { id: 3 }
  ])

  // Just add and remove items in the children 
  // without using the muuri methods .add() & .remove()
  const add = (id) => setItems(items.concat({ id })) 
  const remove = (id) => setItems(items.filter(item => item.id !== id))

  // Generate the items on each render,
  // The component will find out the added/removed items and will 
  // take care to add/remove them from the muuri instance
  // (The use of the key prop is critical)
  const children = items.map(item => <Item key={item.id}></Item>)

  // Pass the filter and the sort props.
  // The component will call the .filter() and .sort() method
  // each time the provided values change or an item is added.
  return (
    <MuuriComponent
      sort={sort}
      filter={filter}
      ref={muuriRef}
      onMount={(muuri) => {/* do something... */}}
      onUnmount={(muuri) => {/* do something...*/}}
      options={{
        layoutDuration: 400,
        layoutEasing: "ease",
        dragEnabled: true,
        dragSortInterval: 50,
        dragReleaseDuration: 400,
        dragReleseEasing: "ease"
      }}>
      {children}
    </MuuriComponent>
  )
}

// A component that represents an item
const Item = () => {
  return (
    // Use 'display: none' to see the adding animation
    <div className="item" style="display: none">
      <div className="item-content">
        My item
      </div>
    </div>
  )
}
```

The new items will be added in the muuri instance in the same position in which they have been added in the children array.

```js
// instead of muuri.add(item, { index })
const addAt = (id, index) => {
  setItems([
    ...items.slice(0, index),
    { id },
    ...items.slice(index)
  ])
}
```

## Props

```jsx
import { MuuriComponent } from 'muuri-react';
```

| Name | Type | Description |
| --- | --- | -- |
| `ref` | `React.Ref` | The ref for the Murri instance. |
| `options` | `object` | The options passed to the muuri instance, the grid element is automatically generated and used internally by the library. Note that the muuri instance is generated only when the component in mounted.  |
| `sort` | `array` / `function` / `string` | The sort value. The `.sort()` method will be called automatically when this prop change or when an item is added.  |
| `filter` | `function` / `string` | The filter value. The `.filter()` method will be called automatically when this prop change or when an item is added. |
| `sortOptions` | `object` | The sort options used with the sort prop.  |
| `filterOptions` | `object` | The filter options used with the filter prop. |
| `onMount` | `function` | If provided this function will be called when the method is `mounted`, the first param passed is the muuri instance. This is a good place to bind the muuri events. |
| `onUnmount` | `function` | If provided this function will be called when the method is `unmounted`, the first param passed is the muuri instance. Note that the instance is automatically destroyed after this method has been called. |

## Limitations

- The the grid element is automatically setted as the drag container to avoid bug if the component re-render while an item is dragging.
- You shouldn't use the add/remove method or set the items in the grid options. The items setted in this way will be removed on the next render.
- This library is made with hooks so a react version > 16.8 is needed.

> ⚠️ The name of this package is **muuri-react** (react-muuri is a different package)

## License

Licensed under [The MIT license](https://github.com/mjtischler/react-muuri/blob/develop/LICENSE.md).
