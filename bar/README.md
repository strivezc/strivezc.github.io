## 开发

```bash
# 克隆项目
git clone http://117.50.94.27/talk915_new/talk915-admin.git

# 进入项目目录
cd talk915-admin

# 安装依赖
npm install

# 建议不要直接使用 cnpm 安装依赖，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

# 启动服务
npm run dev
```

浏览器访问 http://localhost:9527

## 发布

```bash
# 构建测试环境
npm run build:stage

# 构建生产环境
npm run build:prod
```

## 其它

```bash
# 预览发布环境效果
npm run preview

# 预览发布环境效果 + 静态资源分析
npm run preview -- --report

# 代码格式检查
npm run lint

# 代码格式检查并自动修复
npm run lint -- --fix
```

## 项目结构

```bash
|- talk915-admin
    |- src
    	|- api：接口请求
    	|- assets：静态资源
    	|- components：通用组件
    	|- directive：自定义指令
    	|- filters：自定义过滤器
    	|- icons：图标组件
    	|- layout：布局组件
    	|- router：路由配置
    	|- store：状态管理
    	|- styles：自定义样式
    	|- utils：通用工具方法
    	    |- auth.js：token 存取
    	    |- permission.js：权限检查
    	    |- request.js：axios 请求封装
    	    |- index.js：工具方法
    	    |- http.js：模块化http请求
    	    |- globalMixin.js：全局混合方法
    	|- views：页面
    	|- permission.js：登录认证和路由跳转
    	|- main.js：全局入口文件
    	|- App.vue：全局入口组件
    |- mock：mock数据
    |- dist：build打包文件
    |- public：存放静态文件
```

