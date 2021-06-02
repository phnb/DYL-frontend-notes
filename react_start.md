# react 项目设置

* cd react && yarn install && cd .. 安装全部依赖包
* 在 /public/index.html 第10行、14行修改网站介绍, 在第31行修改网站名

## 模板详情

其中项目添加的依赖和进行的设置包括

#### 前端项目开发

* 在 /src/app/pages 文件夹创建 Page-name 文件夹，包含 Page-name.js 和 Page-name.css 来创建新页面
* 在 /src/app/components 文件夹创建 Comp-name 文件夹，包含 Comp-name.js 和 Comp-name.css 来创建新组件
* 在 /src/app/core 文件夹创建 file-name.js 来编写实现业务逻辑的关键代码

#### React 依赖和设置

* React Router 路由组件
* Antd UI库
* Antd 定制化所需的
    * carco
    * craco-less
    * craco.config.js 中配置的网站配色方案标准
* /src/app 下 components, pages 和 core 文件夹的源码管理方案
* /src/app/App.js 中创建的 react-router 导航根组件
* /public 文件夹内容：
    * 全部 logo 图片文件
    * index.html 的网站名
    * index.html 中 meta设置
        * 网页中的输入框点击不会放大