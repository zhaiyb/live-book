###要吃饭，也要时间。不如来"吃时间"。
---
####业务需求：{#biz}

* 实时查看各餐馆当前排队情况
* 考虑到目前是在PC上开发，定位为供餐馆使用的信息实时更新系统

#### 技术需求：{#tec}

* UI层：[ant-design](https://ant.design/index-cn)
* 前端业务层：react、redux、redux-saga（或者叫做dva）
* 服务器端：[express](http://www.expressjs.com.cn/)
* 数据库：[mongodb](https://www.mongodb.com/)
* 走通一次CRUD

####准备&&建站{#start}

1. 在[FCC](https://learn.freecodecamp.org/)上刷一遍express和[mongoose](http://mongoosejs.com/)。（一天）

2. 查看antd的官网，尝试了[create-react-app](https://github.com/facebook/create-react-app)，发现有些太纯粹了，只是react+webpack+babel，而既然已经知道了redux这种不可逆技能，干脆再学一下[dva](https://github.com/dvajs/dva/blob/master/README_zh-CN.md)。（一天）

3. 学习dva，就又牵扯出了[umi](https://umijs.org/)和[roadhog](https://github.com/sorrycc/roadhog/blob/master/README_zh-cn.md)，甚至[next.js](https://nextjs.org/)，不过不打算深入使用了。只是感到从一点出发，动辄就能牵出整个体系，用的人多一点，再活跃热闹点，就又是一个生态了。哦，为了更好的理解dva，需要知道[redux-saga](https://github.com/redux-saga/redux-saga)是如何工作的。（一天）

4. 保持前后断分离，需要开启两个服务，一个后端的node服务器，提供api接口和index.html；一个前端服务充当存放js文件的cdn。

5. 用[dva-cli](https://github.com/dvajs/dva-cli)脚手架搭建前端业务层。

6. 用[express-generator](https://www.npmjs.com/package/express-generator)脚手架搭建node层。**注意：**使用.env配置文件的话，需要`npm i dotenv`参考[dotenv](https://github.com/motdotla/dotenv)。

7. 用dva搭建前端业务层。**注意：**使用router4，根路由‘/’是否需要加`exact`一定要谨慎。

8. 前后端联调要加CORS配置。eg.

   ```js
   //设置跨域访问
   app.use((req, res, next) => {
       res.header("Access-Control-Allow-Origin", "*");
       res.header("Access-Control-Allow-Headers", "X-Requested-With");
       res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
       res.header("X-Powered-By",' 3.2.1');
       res.header("Content-Type", "application/json;charset=utf-8");
       //处理中文参数
       //req.query = JSON.parse(decodeURIComponent(JSON.stringify(req.query)));
       next();
   });
   ```

9. 数据库就用[mLab](https://mlab.com/home)了，后续有需要再本地搭建mongodb。

#### 代码经验{#experience}

1. get参数中文，则前端`encodeURIComponent(param)`，后端做对应的`decodeURIComponent`，目前是做到了app层级的中间件中。

   ```js
   //处理中文参数
   app.use((req, res, next) => {
   	req.query = JSON.parse(decodeURIComponent(JSON.stringify(req.query)));
   }
   ```

2. mongoose做like查询时，如果正则是变量，以`var stream = collection.find({"FirstName": new RegExp(val)}).stream();`的形式。

   > You need to create a regular expression object from the string using the `RegExp` constructor as the `/.../` syntax is only for use with literals.

3. 查询参数不固定，则动态创建queryObj，eg.(结合上述第2条)

   ```js
   let queryObj = {};
   Object.keys(req.query).forEach((key)=>{
       queryObj[key] = new RegExp(req.query[key])
   });
   FoodBiz.find(queryObj,callback);
   ```

   ​