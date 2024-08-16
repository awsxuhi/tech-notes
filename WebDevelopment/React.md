### When we use setState, to make sure we have the latest prevState

By using "...todos, Below code won't ensure you got the latest data:

```javascript
setTodos([...todos, {id: Math.random().toString(), text: text}]);
```

We need to change it to below using functions to always get the latest data of previous todo list.

```javascript
setTodos(prevTodos => [...prevTodos, {id: Math.random().toString(), text: text}]);
```



### When we failed to start development server

The error is as follows:

```shell
node:internal/modules/cjs/loader:998
  throw err;
  ^

Error: Cannot find module 'C:\Users\Hammad\ngo_project\index.js'
    at Module._resolveFilename (node:internal/modules/cjs/loader:995:15)
    at Module._load (node:internal/modules/cjs/loader:841:27)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:81:12)
    at node:internal/main/run_main_module:23:47 {
  code: 'MODULE_NOT_FOUND',
  requireStack: []
}

Node.js v18.12.1
```

Try to use below lines to fix it:

```shell
npm cache clean --force
rm -rf node_modules
rm package-lock.json
npm install
```

