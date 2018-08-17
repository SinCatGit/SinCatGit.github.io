---
layout: post
title: Vue工程搭建
author: sincat
date: 2018-07-22 17:37:15 +0800
category: 技术
tags: vue 前端 

published: true
---

## 创建Vue工程


    # 安装vue cli
    npm install -g vue-cli
    
    # Initialize a webpack project in the directory 'yz_agent'
    vue init webpack  yz_agent
    
    # Change into the directory
    cd yz_agent
    
    # Install dependencies
    npm install
    
    # Fire up the dev server with HMR
    npm run dev
    
## 引入plupload，文件上传

1. 文件上传


    文件上传采用服务器授权，客户端获得授权，然后再客户端上传文件到oss的方式
    
2. 引入plupload


    const my_plupload = require('../assets/js/plupload-2.3.6/plupload.full.min.js')
    const plupload = my_plupload.plupload
    
这样就可以调用了，路径一定要正确。


    
## 多语言支持

1. 在main.js文件添加如下内容


    import VueI18n from 'vue-i18n'
    
    Vue.use(VueI18n) 
    
    const i18n = new VueI18n({
        locale: 'zh-CN',
        messages: {
            'zh-CN': require('./common/lang/zh'), 
            'en-US': require('./common/lang/en') 
        }
    })
    

2. 在main.js的new Vue追加静态变量i18n,如下**部分显示


    new Vue({
    
    el: '#app',
    
    **i18n,**
    
    router,
    
    components: { App },
    
    template: '<App/>'
    
    })

3. 在src目录下，创建目录common/lang文件夹，放置en.js及zh.js

en.js文件内容

    export const m = { 
      lang: 'English',
      login: 'Login',
      logout: 'Login Out',
      issue_asset: 'Issue Asset'
    }
    
zh.js文件内容

    export const m = {
      lang: '中文',
      login: '登录',
      logout: '退出',
      issue_asset: '发行资产'
    }
    
4. 在Welcome.vue放置如下代码

    export default {
      name: 'Welcome',
      data () {
        return {
          msg: '欢迎测试！',
          lang: 'zh-CN'
        }
      },
      methods: {
        changeLangEvent() {
            if ( this.lang === 'zh-CN' ) {
              this.lang = 'en-US';
              this.$i18n.locale = this.lang;
            }else {
              this.lang = 'zh-CN';
              this.$i18n.locale = this.lang;
            }
        },
      }
    }

5.在Welcome.vue给button添加事件

    <button type="button" class="btn btn-primary btn-block" @click="changeLangEvent()">{{ $t('m.lang') }}</button>
    
如上，{{ $t('m.lang') }} 引用文件语言，点击按钮切换语言
    
    
    

    