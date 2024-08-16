```shell
npm start
tsc -w

npm install --save-dev webpack webpack-cli webpack-dev-server typescript ts-loader
npm install --save-dev clean-webpack-plugin


// lodash is based on JS, to import lodash in Typescript, we need to install it as follows:
// Translation type package.
npm install --save-dev @types/lodash
npm install --save @types/lodash

npm install --save-dev @types/jquery

npm install --save class-transformer
```





### Install the TypeScript globally in your Mac

In Mac, we need to use "sudo" in front of "npm".

```shell
sudo npm install -g typescript
```

Step 1: coding with app.ts

Step 2: Compile it into JS file. Then app.js was generated.

```shell
tsc app.ts
```





## Setting up React + TypeScript environment

Link: https://create-react-app.dev/docs/adding-typescript/

```shell
npx create-react-app my-app --template typescript
```

I found the command in a course and tried it. It did **NOT** worked. We should use "--template".

```shell
npx create-react-app my-app --typescript   /* Not work */
```

To run the command and generate filies in the current folder, you can use "." instead of my-app.

```shell
npx create-react-app . --template typescript
```



After installing create-react-app with "sudo npm install", you can use create-react-app without using npx.  Now you can create an environment in the current directory with ".". 

```shell
sudo npm install -g create-react-app
create-react-app . --template typescript
```

However, it's **NOT** recommended. If you've previously installed `create-react-app` globally via `npm install -g create-react-app`, we recommend you uninstall the package using `npm uninstall -g create-react-app` to ensure that `npx` always uses the latest version. Global installs of `create-react-app` are no longer supported. (From React Site: https://create-react-app.dev/docs/adding-typescript/)



Run your development server:

```shell
npm start
```



### Install redux:

```shell
npm install redux react-redux axios redux-thunk
npm install @types/react-redux
```



### Install CloudScape ###

Follow the guidance from https://cloudscape.design/get-started/integration/using-cloudscape-components/

```shell
npm install @cloudscape-design/global-styles
npm install @cloudscape-design/components
```

### Install ts-node

After writting ts file, we need to use 'tsc xxx.ts' to generate the js file before running it with 'node xxx.js'. Now we have a way to combine these 2 command lines into 1 single line. All we need to do is installing the ts-node.

```shell
npm install -g ts-node
ts-node xxx.ts
```

### Install React Router

```shell
npm install react-router-dom
```

### Install Node-sass

```shell
npm install node-sass
```



### Install i18next

```shell
npm install react-i18next i18next --save

# if you'd like to detect user language and load translation
npm install i18next-http-backend i18next-browser-languagedetector --save
```

