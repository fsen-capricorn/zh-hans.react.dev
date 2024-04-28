---
title: Hook 的规则
---

<Intro>
Hook 是使用 JavaScript 函数定义的，但它们代表了一种特殊的可重用的 UI 逻辑，并且对它们可以被调用的位置有限制。
</Intro>

<InlineToc />

---

## 只在顶层调用 Hook {/*only-call-hooks-at-the-top-level*/}

在 React 中，以 `use` 开头命名的函数被称为 **[Hook](/reference/react)**。

**不要在循环、条件语句、嵌套函数或 `try`/`catch`/`finally` 代码块中调用 Hook**。相反，你应该在 React 函数组件的顶层使用 Hook，且在任何提前返回之前。你只能在 React 渲染函数组件时调用 Hook：

* ✅ 在 [函数组件主体](/learn/your-first-component) 的顶层调用它们。
* ✅ 在 [自定义 Hook 主体](/learn/reusing-logic-with-custom-hooks) 的顶层调用它们。

```js{2-3,8-9}
function Counter() {
  // ✅ Good: 在函数组件顶层
  const [count, setCount] = useState(0);
  // ...
}

function useWindowWidth() {
  // ✅ Good: 在自定义 Hooks 顶层
  const [width, setWidth] = useState(window.innerWidth);
  // ...
}
```

不支持在其他任何情况下调用以 `use` 开头的 Hook，例如：

* 🔴 不要在条件语句或循环中调用 Hook。
* 🔴 不要在条件性的 `return` 语句之后调用 Hook。
* 🔴 不要在事件处理函数中调用 Hook。
* 🔴 不要在类组件中调用 Hook。
* 🔴 不要在传递给 `useMemo`、`useReducer` 或 `useEffect` 的函数内部调用 Hook。
* 🔴 不要在 `try`/`catch`/`finally` 代码块中调用 Hook。

如果你违反了这些规则，你可能会看到以下错误：

```js{3-4,11-12,20-21}
function Bad({ cond }) {
  if (cond) {
    // 🔴 Bad: inside a condition (to fix, move it outside!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad() {
  for (let i = 0; i < 10; i++) {
    // 🔴 Bad: inside a loop (to fix, move it outside!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad({ cond }) {
  if (cond) {
    return;
  }
  // 🔴 Bad: after a conditional return (to fix, move it before the return!)
  const theme = useContext(ThemeContext);
  // ...
}

function Bad() {
  function handleClick() {
    // 🔴 Bad: inside an event handler (to fix, move it outside!)
    const theme = useContext(ThemeContext);
  }
  // ...
}

function Bad() {
  const style = useMemo(() => {
    // 🔴 Bad: inside useMemo (to fix, move it outside!)
    const theme = useContext(ThemeContext);
    return createStyle(theme);
  });
  // ...
}

class Bad extends React.Component {
  render() {
    // 🔴 Bad: inside a class component (to fix, write a function component instead of a class!)
    useEffect(() => {})
    // ...
  }
}

function Bad() {
  try {
    // 🔴 Bad: inside try/catch/finally block (to fix, move it outside!)
    const [x, setX] = useState(0);
  } catch {
    const [x, setX] = useState(1);
  }
}
```

你可以使用 [`eslint-plugin-react-hooks` 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks) 来捕获这些错误。

<Note>

[自定义 Hook](/learn/reusing-logic-with-custom-hooks) **可以** 调用其他 Hook（这正是它们的主要目的）。之所以可以这样做，是因为自定义 Hook 也应该只在函数组件渲染时被调用。

</Note>

---

## 仅在 React 函数中调用 Hook {/*only-call-hooks-from-react-functions*/}

不要在常规的 JavaScript 函数中调用 Hook。相反，你可以：

✅ 在 React 函数组件中调用 Hook。
✅ 在 [自定义 Hook](/learn/reusing-logic-with-custom-hooks#extracting-your-own-custom-hook-from-a-component) 中调用 Hook。

遵循这条规则，你可以确保组件中的所有状态逻辑在其源代码中清晰可见。

```js {2,5}
function FriendList() {
  const [onlineStatus, setOnlineStatus] = useOnlineStatus(); // ✅
}

function setOnlineStatus() { // ❌ Not a component or custom Hook!
  const [onlineStatus, setOnlineStatus] = useOnlineStatus();
}
```
