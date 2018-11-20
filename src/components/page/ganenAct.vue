<template>
  <div id="ganenAct">
    <div class="progressOne" >
        <div class="imgPic">
          <img src="../../img/ganenH.jpg" alt="">
        </div>
        <div  class="descInfo">
          <p>伍福许愿奖池：<br/>
               <span style="padding-left: 20px"> A：樱花护手霜套装</span><br/>
                <span style="padding-left: 20px">B: 松茸黑枸杞 </span><br/>
                <span style="padding-left: 20px">C：厨房锅具套装</span><br/>
                <span style="padding-left: 20px">D：陶瓷咖啡杯套装</span><br/>
               <span style="padding-left: 20px"> E：好太太洗衣液</span>
                等等.....总价值50万大奖等你来拿！</p>
              <p> 温暖治愈你的心和胃，这里是一个惊喜不断的公众号</p>
              <p>长按识别上方二维码，关注公众号输入'<span style="color:red">1</span>'选你想要的礼物</p>
        </div>
       <div class="descInfoF">
          <p>报名活动时间: 11月20日至11月22日</p>
          <!-- <p>开奖时间: 22日</p> -->
          <p>联系电话：18017116456</p>
          <p>公司电话: 021-51853776</p>
          <p>活动负责人: 李小姐</p>
          <p>地址：上海市闵行区七宝镇吴宝路428号台上创意园A6#216</p>
       </div>
        <div class="fx splitY">
          <div class="buttOne redCol" @click="sendShare">分享好友领礼物</div>
        </div>
        <div class="bottomIMg">
           <p class="Address">本活动最终解释权由伍福世嘉健康管理有限公司所有</p>
            <div class="logoPic">
              <img src="../../img/logo.png" alt="">
            </div>
        </div>
        
    </div>
    <!-- 蒙版 -->
      <div class="box" id="box" @click="isShow=false" v-if="isShow"></div>
      <img src="../../img/shareHint.png" @click="isShow=false"  class="shareHint" v-if="isShow">
  </div>
</template>
<script>
import { Header } from "mint-ui";
import axios from "axios";
import qs from "qs";
import { Toast } from "mint-ui";
import { Indicator } from "mint-ui";

import utils from "../common/common.js";

export default {
  name: "product_details",
  data() {
    return {
      openid: "",
      activityIdMine: "",
      isShow: false
    };
  },
  methods: {
    //首页相关信息+openid存
    login: function() {
      var $this = this;
      var data = qs.stringify({
        paramUrl: window.location.href
      });
      var baseUrl = BaseUrl + "index/getCode";
      axios({
        method: "POST",
        url: baseUrl,
        type: "json",
        data: data
      }).then(function(data) {
        let datas = data.data.data;
        location.href = datas.url;
      });
    },
    //获取openid
    logins: function() {
      var $this = this;
      var baseUrl = BaseUrl + "index/getUserOpenid";
      var data = qs.stringify({
        code: utils.getQueryString("code")
      });
      axios({
        method: "post",
        url: baseUrl,
        type: "json",
        data: data
      }).then(function(data) {
        if (data.data.status == 1) {
          $this.openid = data.data.data.openid;
          localStorage.setItem("openid", $this.openid);
        }
      });
    },
    //绑定关系
    bindRela() {
      var $this = this;
      var data = qs.stringify({
        activtyId: $this.getQueryString("activityIdOther"),
        openid: localStorage.getItem("openid")
      });
      var baseUrl = BaseUrl + "index/bindRelation";
      axios({
        method: "POST",
        url: baseUrl,
        type: "json",
        data: data
      }).then(function(data) {
        console.log(data);
      });
    },
    getQueryString(name, url) {
      url = url || window.location.search.substr(1);
      console.log(url);
      var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
      var r = url.match(reg);
      if (r != null) return decodeURI(r[2]);
      return null;
    },
    //获取活动id
    getAct() {
      var that = this;
      var data = qs.stringify({
        openid: localStorage.getItem("openid")
      });
      axios({
        url: BaseUrl + "/index/personalCenter",
        method: "post",
        data: data,
        type: "json"
      }).then(info => {
        var data = info.data.data;
        that.activityIdMine = data.myActivityId;
        that.WxZd();
      });
    },
    WxZd() {
      var $this = this;
      var data = qs.stringify({
        originUrl: window.location.href
      });
      var baseUrl = BaseUrl + "index/getSignPackage";
      axios({
        method: "POST",
        url: baseUrl,
        type: "json",
        data: data
      }).then(
        function(res) {
          var info = res.data.data.signPackage;
          this.appId = info.appId;
          this.nonceStr = info.nonceStr;
          this.timestamp = info.timestamp;
          this.signature = info.signature;
          wx.config({
            debug: false,
            appId: this.appId, // 和获取Ticke的必须一样------必填，公众号的唯一标识
            timestamp: this.timestamp, // 必填，生成签名的时间戳
            nonceStr: this.nonceStr, // 必填，生成签名的随机串
            signature: this.signature, // 必填，签名，见附录1
            //需要分享的列表项:发送给朋友，分享到朋友圈，
            jsApiList: ["onMenuShareAppMessage", "onMenuShareTimeline"]
          });
          //处理验证失败的信息
          wx.error(function(res) {
            console.log(res);
          });
          //处理验证成功的信息
          wx.ready(function() {
            //分享到朋友圈
            wx.onMenuShareTimeline({
              title: "感恩送礼~你想要的，快快告诉伍福农场!", // 分享标题
              link:
                "http://egg.wufuapp.com/ganenAct?openid=" +
                $this.openid +
                "&activityIdOther=" +
                $this.activityIdMine,
              imgUrl: "http://egg.wufuapp.com//static/img/ganenAct.jpg", // 分享图标
              success: function(res) {},
              cancel: function(res) {
                console.log(res);
              }
            });
            //分享给朋友
            wx.onMenuShareAppMessage({
              title: "感恩送礼~你想要的，快快告诉伍福农场!", // 分享标题
              desc: "这是一份来自伍福农场对你满满爱的包裹，请注意查收！", // 分享描述
              link:
                "http://egg.wufuapp.com/ganenAct?openid=" +
                $this.openid +
                "&activityIdOther=" +
                $this.activityIdMine,
              imgUrl: "http://egg.wufuapp.com//static/img/ganenAct.jpg", // 分享图标
              type: "", // 分享类型,music、video或link，不填默认为link
              dataUrl: "", // 如果type是music或video，则要提供数据链接，默认为空
              success: function(res) {},
              cancel: function(res) {
                console.log(res);
              }
            });
          });
        }.bind(this)
      );
    },
    sendShare() {
      this.isShow = true;
    }
  },
  created() {
    if (!localStorage.getItem("openid")) {
      if (utils.getQueryString("code")) {
        this.logins();
      } else {
        this.login();
      }
    }
    if (utils.getQueryString("activityIdOther")) {
      this.bindRela();
      this.WxZd();
    } else {
      this.getAct();
    }
  },
  mounted() {}
};
</script>
<style lang="scss" scoped>
#ganenAct {
  position: relative;
  height: 100%;
  .imgPic {
    img {
      width: 100%;
    }
  }

  .descInfo {
    margin: 0.65rem;
    font-size: 14px;
    line-height: 1.6rem;
  }
  .descInfoF {
    margin: 0.65rem;
    font-size: 14px;
    line-height: 1.6rem;
  }
  .Address {
    font-size: 14px;
    text-align: center;
  }
  .bottomIMg {
    width: 100%;
    height: 3rem;
    margin-top: 1rem;
    color: #dcdddd;
    .logoPic {
      width: 35%;
      margin: 0 auto;
      img {
        width: 100%;
      }
    }
  }
  /*蒙版*/
  .box {
    opacity: 0.56;
    background: #000;
    z-index: 9;
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }
  .shareHint {
    width: 80%;
    margin: 0 auto;
    display: block;
    z-index: 999;
    position: fixed;
    top: 3rem;
    left: 3.3rem;
  }
  .progressOne {
    .splitY {
      height: 3rem;
      text-align: center;
      position: relative;
      .buttOne {
        width: 35%;
        height: 3rem;
        line-height: 3rem;
        position: absolute;
        font-size: 0.8rem;
        left: 50%;
        transform: translateX(-50%);
        top: 0;
        border: 1px solid #000;
        background-color: #fff;
        border-radius: 1.4rem;
        color: #000;
      }
    }
  }
}
</style>

