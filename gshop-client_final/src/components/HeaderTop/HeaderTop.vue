<template>
  <header class="header">
    <slot name="left"></slot>
    <span class="header_title">
      <span class="header_title_text ellipsis">
        <p>

        </p>
      </span>
    </span>
    <slot name="right"></slot>
  </header>
</template>

<script>

  export default {
    props: {
    },
    mounted() {
      //定义一个空的位置构造函数
    function Location() {
    };
    //定义一个可以获得经纬度的方法
    Location.prototype.getLocation = function (callback) {
        var options = {
            enableHighAccuracy: true,
            maximumAge: 1000
        };
        this.callback = Object.prototype.toString.call(callback) == "[object Function]" ?
            callback :
            function (address) {
                alert(address.province + address.city);
                console.log("getLocation(callbackFunction) 可获得定位信息对象");
            };
        var thisSelf = this;
        if (navigator.geolocation) {
            //浏览器支持geolocation
            navigator.geolocation.getCurrentPosition(function (position) {
                //经度
                var longitude = position.coords.longitude;
                //纬度
                var latitude = position.coords.latitude;
                thisSelf.loadMapApi(longitude, latitude);
            }, thisSelf.onError, options);
        } else {
            //浏览器不支持geolocation
            alert('您的浏览器不支持地理位置定位3333');
        }
    };
    //定义一个可以解析经纬度的方法，调用百度的api
    Location.prototype.loadMapApi = function (longitude, latitude) {
        var thisSelf = this;
        var oHead = document.getElementsByTagName('HEAD').item(0);
        var oScript = document.createElement("script");
        oScript.type = "text/javascript";
        oScript.src = "http://api.map.baidu.com/getscript?v=2.0&amp;ak=FGtEcZ8t5ca35RLkPXGd1fPQfr0CtRT1;services=&amp;t=20140930184510";
        oHead.appendChild(oScript);
        oScript.onload = function (date) {
            var point = new BMap.Point(longitude, latitude);
            var gc = new BMap.Geocoder();
            gc.getLocation(point, function (rs) {
                var addComp = rs.addressComponents;
                thisSelf.callback(addComp);
            });
        }
    };
    //定义出现查询位置出现意外的方法
    Location.prototype.onError = function (error) {
        switch (error.code) {
            case 1:
                alert("位置服务被拒绝");
                break;
            case 2:
                alert("暂时获取不到位置信息");
                break;
            case 3:
                alert("获取信息超时");
                break;
            case 4:
                alert("未知错误");
                break;
        }
    };
    //调用
    var local = new Location();
    local.getLocation(function (res) {
        var str = "";
        var i = ""
        for (i in res) {
            str = res[i] + str;
        }
        document.querySelector("p").innerHTML = str;
    })
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .header
    background-color #02a774
    position fixed
    z-index 100
    left 0
    top 0
    width 100%
    height 45px
    .header_search
      position absolute
      left 15px
      top 50%
      transform translateY(-50%)
      width 10%
      height 50%
      .icon-sousuo
        font-size 25px
        color #fff
    .header_title
      position absolute
      top 50%
      left 50%
      transform translate(-50%, -50%)
      width 50%
      color #fff
      text-align center
      .header_title_text
        font-size 20px
        color #fff
        display block
    .header_login
      font-size 14px
      color #fff
      position absolute
      right 15px
      top 50%
      transform translateY(-50%)
      .header_login_text
        color #fff
</style>