# vite+vue3+uniapp+微信小程序项目搭建

## eslint

```sh
# 根据提示和项目情况选择y/n
npx eslint --init
```

## prettier

```sh
npm i prettier eslint-config-prettier eslint-plugin-prettier -D
```

+ 创建.prettierrc.js文件

```js
// .prettierrc.js
module.exports = {
  printWidth: 100,
  tabWidth: 2,
  useTabs: false, // 是否使用tab进行缩进，默认为false
  singleQuote: true, // 是否使用单引号代替双引号，默认为false
  semi: true, // 行尾是否使用分号，默认为true
  arrowParens: 'always',
  endOfLine: 'auto',
  vueIndentScriptAndStyle: true,
  htmlWhitespaceSensitivity: 'strict',
};````````
```

+ 配置eslintrc

```js
// .eslintrc.js

module.exports = {
  root: true, // 停止向上查找父级目录中的配置文件
  env: {
    browser: true,
    es2021: true,
    node: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:vue/vue3-essential',
    'plugin:prettier/recommended',
    'prettier', // eslint-config-prettier 的缩写
  ],
  parser: 'vue-eslint-parser', // 指定要使用的解析器
  // 给解析器传入一些其他的配置参数
  parserOptions: {
    ecmaVersion: 'latest', // 支持的es版本
    parser: '@typescript-eslint/parser',
    sourceType: 'module', // 模块类型，默认为script，我们设置为module
  },
  plugins: ['vue', 'prettier'], // eslint-plugin- 可以省略
  rules: {
    'vue/multi-word-component-names': 'off'
  },
};
```

+ 添加lint命令
```sh
# package.json

# 可以运行'npm run lint'检查代码
"lint": "eslint --ext .js,.vue,.ts src --fix"
```

## 保存文件自动格式化
1、如果用vscode在.vscode设置
```json
//.vscode/settings.json

{
  // 保存时eslint自动修复错误
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  // 保存自动格式化
  "editor.formatOnSave": true
}
```
2、我习惯用webstore，设置路径：Settings ——> Languages & Frameworks ——> JavaScript ——> Prettier(勾选Run on save for files) 


## 环境变量
> vite官方文档: [环境变量和模式](https://cn.vitejs.dev/guide/env-and-mode.html)

```sh
1. 根目录创建.env.[mode]文件

# .env.development

# 开发环境
NODE_ENV = development
VITE_APP_API_BASE_URL = 'http://10.204.xx.xx:9091'
# 是否在打包时生成 sourcemap
VITE_BUILD_SOURCEMAP = true
# 是否在打包时删除 console 代码
VITE_BUILD_DROP_CONSOLE = false

# .env.test
# .env.production

```
> .env.[mode]文件中的mode可自定义，如`.env.development`对应package.json脚本中的`--mode development`  
> 只有以 VITE_ 为前缀的变量才会暴露给经过 vite 处理的代码

```json
//下面三条命令，分别表示开发环境、测试环境、生产环境的运行和打包命令

"dev:mp-weixin": "uni -p mp-weixin --mode development",
"build:test": "uni build --mode test",
"build:pro": "uni build -p mp-weixin --mode production",
```

2、使用环境变量

+ js,vue 文件中可使用`import.meta.env`获取环境变量，比如：
```js
let baseUrl = import.meta.env.VITE_APP_API_BASE_URL;

let isProd = import.meta.env.MODE === 'production';
```

+ vite.config.js 使用环境变量
```js
// vite.config.js

import { defineConfig, loadEnv } from 'vite';

export default ({ command, mode }) => {
  const env = loadEnv(mode, process.cwd());
  return defineConfig({
    plugins: [
      uni(),
    resolve: {
      alias: {
        '@': path.resolve(__dirname, 'src'),
        '@img': path.resolve(__dirname, 'src/static/images'),
      },
    },
    build: {
      sourcemap: env.VITE_BUILD_SOURCEMAP === 'true',
      minify: 'terser',
      terserOptions: {
        compress: {
          drop_console: env.VITE_BUILD_DROP_CONSOLE === 'true', // 去除 console
        },
      },
      chunkSizeWarningLimit: 1500, // chunk 大小警告的限制（以 kbs 为单位）
    },
  });
};
```


## Css预处理器

```sh
// 1、 安装sass
npm i sass -D   或   yarn add sass -D  

//安装 sass-loader
npm i sass-loader@10.1.1 -D   或   yarn add sass-loader@10.1.1 -D
```

1、全局使用自定义变量

+ 根目录新建样式文件styles  
- vars.scss - 自定义变量  
```js
// vite.config.js
css: {
      preprocessorOptions: {
        scss: {
          additionalData: `@import "@/styles/vars.scss";`,
        },
      },
    }
```       
vue文件使用
```css
 .title {
     color: $font-color;
    }
```     


## uni-ui
> [uni-ui官方文档](https://uniapp.dcloud.net.cn/component/uniui/uni-ui.html)         

```sh
//安装 uni-ui
npm i @dcloudio/uni-ui   或   yarn add @dcloudio/uni-ui
```

```json
配置easycom
使用 npm 安装好 uni-ui 之后，需要配置 easycom 规则，让 npm 安装的组件支持 easycom
打开项目根目录下的 pages.json 并添加 easycom 节点

// pages.json
{
	"easycom": {
		"autoscan": true,
		"custom": {
			// uni-ui 规则如下配置
			"^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
		}
	},
	
	// 其他内容
	pages:[
		// ...
	]
}
```        


## 自动导入API
[unplugin-auto-import](https://github.com/antfu/unplugin-auto-import)    

```sh
npm i unplugin-auto-import -D
```
Vite配置

```js
// vite.config.js
import AutoImport from 'unplugin-auto-import/vite'

  plugins: [
    AutoImport({
      imports: ['vue', 'uni-app'],
      // 可以选择auto-import.d.ts生成的位置，使用ts建议设置为'src/auto-import.d.ts'
      // dts: 'src/auto-import.d.ts'
      // 自动生成'eslintrc-auto-import.json'文件，在'.eslintrc.cjs'的'extends'中引入解决报错
        eslintrc: {
          enabled: true,
        },
    })
  ]
```
+ 原理: 安装的时候会自动生成auto-imports.d文件(默认是在根目录)
+ 其他插件 vue-router, vue-i18n, @vueuse/head, @vueuse/core等自动引入的自动引入请查看文档

```js
// .eslintrc.js
extends: [
  // 解决使用自动导入api报错
  './.eslintrc-auto-import.json',
],

```

接下来就可以全局使用 vue 相关 api，不用一个个手动导入了。哪些 api 可用请参考生成的 `src/auto-import.d.ts` 类型声明文件。


## Pinia
[pinia官方文档](https://pinia.vuejs.org/)

1. 安装
```sh
npm i pinia
```

2. 创建store
```js
// src/store/index.js

import { createPinia } from 'pinia';

const pinia = createPinia();

export default pinia;
export * from './modules/user';
```

3. 挂载store
```js
// src/main.js
import { createSSRApp } from 'vue';
import store from './store';
import App from './App.vue';

export function createApp() {
  const app = createSSRApp(App);
  app.use(store);
  return {
    app,
  };
}
```

4. 创建useUserStore
```js
// src/store/modules/user/index.js

import { defineStore } from 'pinia';

export const useUserStore = defineStore('user', {
  // id: 'user', // id必填，且需要唯一
  state: () => {
    return {
      name: '张三',
    };
  },
  getters: {
    nameLength: (state) => state.name.length,
  },
  actions: {
    updateName(name) {
      this.name = name;
    },
  },
});
```

5. 使用useUserStore
   
```html
<template>
  <div class="pinia">
    <div class="name">用户名:{{ userStore.name }}</div>
    <div class="length">长度:{{ userStore.nameLength }}</div>
    <van-button type="primary" @click="updateName(true)">action修改store中的name</van-button>
    <van-button @click="updateName(false)">patch修改store中的name</van-button>
  </div>
</template>

<script setup>
  import { useUserStore } from '@/store';

  const userStore = useUserStore();

  const updateName = (isAction) => {
    if (isAction) {
      // action 修改 store 中的数据
      userStore.updateName('userStore.updateName方式');
    } else {
      // 未定义 action 时可以用 $patch 方法直接更改状态属性
      // $patch 修改 store 中的数据
      userStore.$patch({
        name: 'userStore.$patch方式',
      });
    }
  };
</script>
```


## 请求封装
封装请求的方式多种多样，根据自己喜欢的方式实现就好，还可以根据需求增加重试或者取消请求等方法

+ 由于是之前的项目升级，用的还是uni.request，当然也可以用flyio进行请求封装

1、request请求统一封装
```js
// src/utils/http/request.js

import { getToken } from '@/utils/auth';
import { useUserStore } from '@/store';

let baseUrl = import.meta.env.VITE_APP_API_BASE_URL;
const request = ({
  url = '',
  data = {},
  method = 'POST',
  header = { token: getToken() },
  hideLoading=false,
  hideMessage,
}) => {
  const userStore = useUserStore();
  
  return new Promise((resolve, reject) => {
    // if (!hideLoading) {
    //   uni.showLoading({});
    // }
    uni.request({
      timeout: 60000,
      method,
      url: baseUrl + url,
      data,
      header,
      success(response) {
        // if (!hideLoading) {
        // 	uni.hideLoading();
        // }
        let res = response.data;
        // 请求成功，状态码不等于0，报错处理
        if (res.resultCode !== 0) {
          if (hideMessage) {
            reject(res || 'Error');
          } else {
            if (res.resultCode === 3 || res.resultCode === -5) {
              // hideMessage 是否隐藏错误提示
              uni.showToast({
                title: res.resultMessage,
                icon: 'none',
                duration: 3000,
              });
            } else if (res.resultCode === -4) {
              //
            } else {
              if (res.resultCode === -1) {
                // to re-login
                uni.showModal({
                  title: '提示',
                  content: '登录失效，请重新登录！',
                  confirmColor: '#0087FF',
                  cancelColor: '#0087FF',
                  success: function (res) {
                    if (res.confirm) {
                      //*清空缓存重新登录
                      userStore.resetToken().then(() => {
                        uni.navigateTo({
                          url: '/subPackagesA/personal/chooseLoginType',
                        });
                      });
                    }
                  },
                });
              } else {
                uni.showToast({
                  title: `操作异常，请联系管理员(${res.resultCode})!`,
                  icon: 'none',
                  duration: 3000,
                });
              }
            }
            reject(res || 'Error');
          }
        } else {
          // 成功直接返回promise
          resolve(res);
        }
      },
      fail(err) {
        reject(err);
      },
    });
  });
};
export default request;

```
2、接口api管理
```js
// src/api/UserService.js

import request from '@/utils/request';
import { requestPort } from '@/utils/requestPort';

export default {
  login(data) {
    return request({
      url: `${requestPort.users}/user/login`,
      method: 'post',
      data,
    });
  },
  logout() {
    return request({
      url: `${requestPort.users}/user/logout/3`,
      method: 'post',
    });
  },
  logoff(data) {
    return request({
      url: `${requestPort.users}/user/logoff`,
      method: 'post',
      data,
    });
  }
};
```

3、使用接口
```html
// vue文件内
<template>
  <view>
     <button type="default" class="logout" @click="logout">注销</button>
  </view>
</template>>
<script setup>
import UserService from '@/api/UserService';

const logout = async () => {
  try {
    const { resultData } = await UserService.logout();
    console.log(resultData, 'resultData');
  } catch (e) {
    console.log(e, 'error');
  }
};
<script>
```
