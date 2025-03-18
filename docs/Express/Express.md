



```shell
npm init
npm install express --save
npm install nodemon --save-dev
```

Only `start` and `test` are special keyword, you can use `npm start` instead of  `npm run start` and `npm test` instead of `npm run test`



Install body-parser so we can easily parse the req/res. 

```shell
npm install body-parser --save
```



From scratch:

```shell
mkdir backend
cd backend
npm init
npm install --save express body-parser
npm install --save-dev nodemon

# Optional
npm install --save uuid
npm install --save express-validator
npm install --save mongodb
npm install --save mongoose
npm install --save mongoose-unique-validator

npm install --save multer (handle file upload)
npm install --save bcryptjs (secure password, convert them to unreadable string)
npm install --save jsonwebtoken

touch app.js
```

Update package.json file with:

```json
  "scripts": {
    "start": "nodemon app.js",
  }
```

Update app.js, and then use `npm start` to start the development server.



```aab
```

Transaction: Add new place

![image-20231012094510622](/Users/xuhi/Library/Application Support/typora-user-images/image-20231012094510622.png)

Transaction: Delete place

![image-20231012100110143](/Users/xuhi/Library/Application Support/typora-user-images/image-20231012100110143.png)



In package.json, under `scripts`, `start` is a special name, which you can use `npm start` instead of `npm run start`. Only `start` and `test` are special name that you can omit the word `run`. 
