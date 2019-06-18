
<template>
  <div style="position: relative;width: 100%;height: 100%;padding:10px;">
    <div id="container-map" style="width:100%;"></div>
    <div id="myPageTop" style="position: absolute;top: 10px;background: #fff;">
      <table>
        <tr>
          <td>
            <label>请输入关键字：</label>
          </td>
        </tr>
        <tr>
          <td>
            <input v-model="query.serach" id="tipinput"/><button @click="search">搜索</button>
          </td>
        </tr>
      </table>
    </div>
    <div id="myPageTable" style="position: absolute;top: 85px;background: #fff; width:280px;height:auto">
      <el-table
        :data="deviceList"
        @row-click="handleTableClick" 
        stripe
        style="width: 100%">
        <el-table-column
          prop="deviceName"
          label="设备名称"
          width="280">
        </el-table-column>
      </el-table>
      <el-pagination class="page-pagination"
        @size-change="sizeChange"
        @current-change="pageChange"
        :current-page="query.pageIndex"
        :page-size="query.pageSize"
        layout="total, prev, pager, next"
        :total="total_count" :small="true">
      </el-pagination>
    </div>
    <div id="message" style="position: absolute; top: 20px;right: 20px;">
      <span class="article bg-orange">
        <i class="iconfont icon-gonggao"></i>
        <a class="num-notice" style="position: absolute; top:40px; right: 0px" @click="clicknum">2</a>
      </span>
    </div>
    <div v-if="alarmTemp.length > 0" id="alarmMessage" style="position: absolute; top: 85px;right: 15px; background: #fff; width:150px;height:auto">
      <!--点击显示报警信息列表-->
      <el-table 
        :data="alarmTemp"
        @row-click="handleTableClick1" 
        stripe
        style="width: 100%">
        <el-table-column
          prop="address"
          label="地址"
          width="125">
        </el-table-column>
      </el-table>
      <el-pagination class="page-pagination"
        @size-change="sizeChange1"
        @current-change="pageChange1"
        :current-page="query.pageIndex1"
        :page-size="query.pageSize1"
        :pager-count="5"
        layout="prev, pager, next"
        :total="total_count1"
        small>
      </el-pagination>
    </div>
  </div>
</template>
 
<script>
  var map
import AMap from 'AMap'
export default {
 
  data () {
    return {
      deviceList: [
        {address: '上海市普陀区金沙江路 1518 弄',deviceName:"设备1",center:[104, 30.5],description:"案件方腊时感觉",state:0}, 
        {address: '上海市普陀区金沙江路 1517 弄',deviceName:"设备2",center:[104, 30.523],description:"ajfjaklsfjas",state:1}, 
        {address: '上海市普陀区金沙江路 1519 弄',deviceName:"设备3",center:[104, 30.562],description:"5236541364",state:0}, 
        {address: '上海市普陀区金沙江路 1516 弄',deviceName:"设备4",center:[114, 30.552],description:"ajlfa4464",state:1}
      ],
      alarmList:[
        {address: '新都1号仓库',center:[104, 30.523]},
        {address: '新都2号仓库',center:[114, 30.552]}
      ],
      alarmTemp:[],
      flag:false,

      infoWindow:null,
      // centerList:[[104, 30.5],[104, 30.523],[104, 30.562],[114, 30.552]],
      // contentList:["案件方腊时感觉","ajfjaklsfjas","5236541364","ajlfa4464"],

      query:{
        serach:'',
        pageIndex:1,
        pageSize:10,
        pageIndex1:1,
        pageSize1:10

      },
      total_count:0,
      total_count1:0,

      screenWidth: document.documentElement.clientWidth,//屏幕宽度
      screenHeight: document.documentElement.clientHeight,//屏幕高度
    }
  },
  methods: {
    //添加一系列点标记到地图上并展示对应点的信息
    test1(){
      this.infoWindow = new AMap.InfoWindow({offset: new AMap.Pixel(0, -70)});
      for (var i = 0, marker; i < this.deviceList.length; i++) {
        var marker = new AMap.Marker({
            position: this.deviceList[i].center,
            icon:"//a.amap.com/jsapi_demos/static/demo-center/icons/poi-marker-default.png",
            offset: new AMap.Pixel(-26.5,-68), // 设置点标记偏移量
            // map: map
        });
        marker.content = this.deviceList[i].description
        marker.on('click', this.markerClick);
        map.add(marker)
      }
    },
    
    markerClick(e) {
      this.infoWindow.setContent(e.target.content);
      this.infoWindow.open(map, e.target.getPosition());
    },

    search(){
      //调用接口
    },
    handleTableClick(row, event, column){
      // 设置缩放级别和中心点
      map.setZoomAndCenter(14, row.center);
    },

    handleTableClick1(row, event, column){
      map.setZoomAndCenter(14, row.center);
    },
    clicknum(){
      this.flag = !this.flag
      if(this.flag)
        this.alarmTemp = this.alarmList
      else
        this.alarmTemp = []
    },
    sizeChange: function(val){
      this.query.pageSize = val
      this.refreshDataList()
    },
    pageChange: function(val) {
      this.query.pageIndex = val
      this.refreshDataList()
    },

    sizeChange1: function(val){
      this.query.pageSize = val
      this.refreshDataList()
    },
    pageChange1: function(val) {
      this.query.pageIndex = val
      this.refreshDataList()
    },
  },
  watch:{
    '$store.state.screenWidth':function(val){ //监听屏幕宽度变化//根据不同分辨率调整页面
      //根据不同分辨率实时调整页面
      document.getElementById("container-map").style.height = (Number(document.documentElement.clientHeight)-70) + 'px';
      document.getElementById("myPageTop").style.right = (Number(document.documentElement.clientWidth)-463) + 'px';
      document.getElementById("myPageTable").style.right = (Number(document.documentElement.clientWidth)-495) + 'px'; 
    },
  },
  mounted () {
    //根据不同分辨率调整页面
    document.getElementById("container-map").style.height = (Number(document.documentElement.clientHeight)-10) + 'px';
    document.getElementById("myPageTop").style.right = (Number(document.documentElement.clientWidth)-300) + 'px';
    document.getElementById("myPageTable").style.right = (Number(document.documentElement.clientWidth)-332) + 'px'; 
    //调用后台借口获取设备信息列表
    // this.$get('/api/rail/index').then(response => {
    //   if(response.code == 0){
    //     response.data.forEach(item => {
    //       this.deviceList.push({deviceName: item.name,center:[item.longitude,item.latitude],description:"公里标:"+item.kmFlag})
    //     });
    //     this.total_count = this.deviceList.length

    //     //初始化地图对象，加载地图
    //     map = new AMap.Map('container-map', {
    //       resizeEnable: true,
    //       zoom: 11,
    //       viewMode: '2D',
    //       center: [104, 30.5],
    //       zooms: [4, 18]
    //     })
    //     map.clearMap();  // 清除地图覆盖物
    //     this.test1()
    //   }
    // })
    //初始化地图对象，加载地图
    map = new AMap.Map('container-map', {
      resizeEnable: true,
      zoom: 11,
      viewMode: '2D',
      center: [104, 30.5],
      zooms: [4, 18]
    })
    map.clearMap();  // 清除地图覆盖物
    this.test1()
    //设备列表长度
    this.total_count = this.deviceList.length
  }
}
</script>
<style type="text/css" scoped>
  html,body,#app{
    height: 100%;
    width: 100%;
  }
  .article{ width:60px; height:60px; border-radius:5px; text-align:center; display:inline-block; color:#fff;}
  .article i{ font-size:40px;}
  .bg-orange{ background:#ff9501;}
  .num-notice{ display:inline-block; font-size:12px; background:#f87679; border-radius:50%; width:20px; height:20px; line-height:20px; text-align:center; color:#fff; position:relative; top:-2px; margin-left:5px;}
</style>