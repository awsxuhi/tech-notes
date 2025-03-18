

```shell
Steps:

# 假设项目的根目录是~/CodingProjects/LibreChat/
cd ~/CodingProjects/LibreChat/
# Install deps based on package-lock.json
npm ci

# 在packages/data-provider下生成dist目录，和两个文件：index.js和index.es.js
# index.js在使用commonjs加载方式的时候使用，而index.es.js在使用es模块加载方式的时候使用。可以看到当前目录下package.json包含
#  "main": "dist/index.js",
#  "module": "dist/index.es.js",
cd packages/data-provider && npm run build

# 这个packages/data-provider目录被以符号链接的方式出现在根目录的node_modules中，这样在项目中的client目录下
# 编译前端代码的时候，librechat-data-provide会作为一个包，从中可以import东西

cd ~/CodingProjects/LibreChat/
cd client && npm run build

cd ~/CodingProjects/LibreChat/
npm run backend
# 上面这个命令等同于：cross-env NODE_ENV=production node api/server/index.js，在本地的3080启动web服务
```

