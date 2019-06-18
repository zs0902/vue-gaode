# gaodeditu
项目概述：
通过在vue中引入高德js，实现地图的加载呈现，以及在地图中实现设备坐标定位，并在设备列表点击的同时进行定位点的切换和地图视图的切换，在点击坐标点的时候可以弹出相关介绍信息。

第一步：在index.html中引入高德js
<script type="text/javascript" rel="preconnect" src="https://webapi.amap.com/maps?v=1.4.14&key=63bb6349795a9b5d64c1774a7acd953d"></script>

第二步：在build/webpack.base.conf.js中配置module.exports
加入：externals: {
  
        AMap: 'AMap',
    
    //     AMapUI: 'AMapUI'
    
  },
  
第三步：在build/vue-loader.conf.js中加入配置：
 chainWebpack: config => {
    config.externals = {
        'AMap': 'AMap'
       }
  }

第四步：在vue文件中引入：import AMap from 'AMap'

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
