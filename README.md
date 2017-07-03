#  仿饿了么移动端商家店铺
## 基于vue2的SPA应用
### 使用技术
  * webpack
  * vue-router
  * axios
  * es6
  * less
## 上图
![Alt Text](https://github.com/ChipingLew/el-app/blob/master/el.app.gif)
## 进入项目根目录，运行 node prod.server.js
  * 启动服务 localhost:8088
## 项目结构
<br>├── build              // 构建服务和webpack配置
<br>├── config             // 项目不同环境的配置
<br>├── dist               // 项目build目录
<br>├── index.html         // 项目入口文件
<br>├── src                // 生产目录
<br>│   ├── assets         // 图片资源
<br>│   ├── common         // 公共的css js 资源
<br>│   ├── components     // 各种组件
<br>│   ├── App.vue        // 主页面
<br>│   └── main.js        // Webpack 预编译入口
<br>├── package.json       // 项目依赖目录
<br>├── data.json          // 模拟后台数据文件

## 实现的功能
* 商品滚动
* 商品联动
* 加入购物车，移除购物车
* 显示评论 评论筛选
* 商品详情
