<template>
  <div id="product_details">
    <!-- <mt-header fixed title="商品详情" style="font-size:1.2rem;height: 3rem;"></mt-header> -->
    <div class="swipeImg">
      <mt-swipe :auto="3000">
        <mt-swipe-item v-for="(img,index) in imgs" :key="index"> <img :src=img alt=""></mt-swipe-item>
      </mt-swipe>
    </div>
    <!-- 	<div style="width:100px;height:100px;background-color: #aaa;color:#fff" @click="clearStor">
			清缓存
		</div> -->
    <!--商品名称-->
    <div class="productInfo">
      <div class="infoOne">
        <p class="eggName">{{title}}</p>
        <!-- <div class="moneyReward">
          <img class="iconMoney" src="../../img/shangjin.png" />
          <p class="moneyTitle">赚赏金</p>
        </div> -->
      </div>
      <div class="infoTwo">
        <p class="active1">包月发送</p>
        <p class="active2">快递 {{freight}}元</p>
        <p class="active3">月销 {{sale}}</p>
      </div>
    </div>
    <!--选择套餐-->
    <div class="chooseSpecial" @click="showToggle">
      <p class="choose">选择套餐</p>
      <img src="../../img/right.png" alt="">
    </div>
    <!--全部评价-->
    <div class="all_evaluate">
      <div class="titleBox">全部评价({{comments}})</div>
      <div class="userEvaluate" v-for="(item,index) in clist" :key="index">
        <div class="evaluateBox">
          <img :src=item.img class="touxiang">
          <div class="cenmte" >
            <span class="userName">{{item.username}}</span>
            <span class="userPhone">{{item.phone}}</span>
          </div>
          <div class="zan">
            <img src="../../img/zan-weixuanzhong.png" alt="" class="xin" @click="goZan" v-show="isZan">
            <img src="../../img/zan-xuanzhong.png" alt="" class="xin" @click="goCancelZan" v-show="isCancel">
            <p class="praise">好评</p>
          </div>
        </div>
        <div class="content">{{item.content}}</div>
      </div>
      <div class="evaluateBtn" @click="goEvaluateAll">全部评价</div>
    </div>
    <!--商品详情-->
    <div class="productDetails">
      <p v-html="content"></p>
    </div>
    <!-- <div class="scrollbottomWx" v-if="showClose" @click="closeModel">
      <img src="../../img/white.png" alt="">
    </div> -->
    <!-- <div class="xiaosanjiao" @click="closeModel" v-if="showClose"></div> -->

    <!-- <div class="scrollbottom" @click="closeModel" v-if="showClose">

      <ul class="scrolLton" ref="con1" :class="{anim:animate==true}">
        <li v-for="(item,index)  in sendList " :key="index"> <img :src="item.img" alt="">{{item.title}}
        </li>
      </ul>
    </div> -->
    <!--立即购买-->
    <div class="buyAndRob">
       <div class="rob" @click="targetPrd">
        <p>购买多月套餐</p>
      </div>
      <div class="buy" @click="showToggle">
        <p>购买单月套餐</p>
      </div>
    </div>
    <!--蒙版-->
    <div class="box" @touchmove.prevent id="box" v-show="isShow " @click="hideMind"></div>
    <img src="../../img/shareHint.png" alt="" @click="hideMind" class="shareHint" v-if="isShow && showShare">
    <!--选择规格-->
    <div class="choice-style" v-show="isShowAt">
      <div class="choice-box">
        <div class="goods-details">
          <img src="../../img/swiper.png" alt="#" class="good-picture">
          <div class="money">
            <p class="price">￥{{sellPrice}}
              <span class="greyIcon">原价￥{{marketPrice}}</span>
            </p>
            <p class="style">库存{{stock}}件</p>
          </div>
          <!--<img src="../../img/close@2x.png" alt="#" class="close"  @click="hideToggle">-->
        </div>
        <div class="choice">
          <p class="colors">套餐规格</p>
          <div class="color-choice">
            <div v-for="(list,key) in packageList" :key="key" class="color" :class="{active:iskey==key}" @click="choice(key)">{{list.number}}枚/{{list.dateTime}}个月</div>
          </div>
        </div>
        <p class="infuse">每月配送30枚，共{{dateTime}}个月</p>
        <div class="numbers">
          <p class="number">购买数量</p>
          <div class="modified">
            <div class="low" @click="sum"></div>
            <input type="number" v-model="amount" class="count" readonly="readonly">
            <div class="plus" @click="sums">+</div>
          </div>
        </div>
        <div class="yes" @click="goConfirmation">确认</div>
      </div>
    </div>
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
      //      slideshow:[],//轮播
      iskey: -1, //价格选择 颜色判断
      isShow: false, //蒙版
      isShowAt: false, //规格
      clist: [],
      marketPrice: "",
      isMaNum: 1,
      isFalse: false, //领蛋提醒
      isGetEgg: false, //领取提醒
      isXiadan: false, //领取提醒-下单
      showShare: false,
      animate: false,
      img: "",
      phone: "",
      comments: 0,
      showClose: true,
      content: "",
      contents: "",
      eggNum: "",
      freight: "",
      id: 16,
      imgs: [],
      isFreight: "",
      isHot: 0,
      packageList: [],
      title: "",
      isZan: true, //未点击的“赞”
      isCancel: false, //点击过的“赞”
      sellPrice: "",
      stock: "",
      number: 0,
      dateTime: "",
      price: 0,
      sale: "0",
      avgPrice: "",
      sendList: [],
      amount: 1, //input数量
      openid: "",
      targetUrl: "",
      appId: "",
      nonceStr: "",
      timestamp: "",
      signature: "",
      activityId: 12,
      isPayOrder: 0,
      isApply: 1
    };
  },
  methods: {
    closeModel() {
      this.showClose = false;
    },
    // 点击‘全部评价’
    goEvaluateAll() {
      this.$router.push({
        path: "/evaluateAll"
      });
    },
    // 点击显示选择套餐
    showToggle() {
      this.isShowAt = !this.isShowAt;
      this.isShow = !this.isShow;
    },
    // 点击蒙版，套餐选择宽隐藏
    hideToggle() {
      this.isGetEgg = false;
      this.isShow = false;
      this.isFalse = false;
      this.isXiadan = false;
    },
    hideMind() {
      this.isShowAt = false;
      this.isGetEgg = false;
      this.isShow = false;
      this.showShare = false;
      this.isFalse = false;
      this.isFalse = false;
      this.isXiadan = false;
    },
    // 点击确认跳转到“order_confirmation”页面，确认订单
    goConfirmation() {
      this.$router.push({
        path: "/order_confirmation",
        query: {
          packageId: this.packageId,
          number: this.amount,
          openid: localStorage.getItem("openid"),
          sellPrice: this.sellPrice
        }
      });
    },
    // 点击赚赏金跳到 make_money 页面
    goMakeMoney() {
      this.$router.push({
        path: "/make_money"
      });
    },
    // 点击“免费抢蛋”跳到“”抢蛋进度的活动页面
    goRobEgg() {
      this.isShow = !this.isShow;
      this.showShare = !this.showShare;
    },
    //立即领取
    goRobEggLin() {
      if (this.isPayOrder == 1) {
        this.isMaNum = 1;
        this.$router.push({ path: "/Brought_egg_success" });
      } else {
        this.isShow = true;
        this.isXiadan = true;
      }
    },
    // '点'赞 + '取消' 赞
    goZan() {
      this.isZan = !this.isZan;
      this.isCancel = !this.isCancel;
    },
    goCancelZan() {
      this.isZan = !this.isZan;
      this.isCancel = !this.isCancel;
    },
    //规格选中后的样式
    choice: function(i) {
      this.iskey = i;
      this.sellPrice = this.packageList[i].sellPrice;
      this.stock = this.packageList[i].stock;
      this.number = this.packageList[i].number;
      this.dateTime = this.packageList[i].dateTime;
      this.avgPrice = this.packageList[i].avgPrice;
      this.price = this.sellPrice / this.stock;
      this.packageId = this.packageList[i].id;
      this.marketPrice = this.packageList[i].marketPrice;
    },
    //input数量
    sums: function() {
      this.amount++;
    },
    sum: function() {
      if (this.amount >= 2) {
        this.amount--;
      } else {
        Toast({
          message: "数量不能小于1",
          duration: 1500
        });
      }
    },
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
          $this.moun();
        }
      });
    },
    targetPrd() {
      this.$router.push("product_details");
    },
    moun() {
      var $this = this;
      var baseUrl = BaseUrl + "api/activityProductDetail";
      var data = qs.stringify({
        id: 17,
        openid: localStorage.getItem("openid")
      });
      axios({
        method: "post",
        url: baseUrl,
        type: "json",
        data: data
      }).then(info => {
        console.log(info);
        $this.title = info.data.data.title;
        $this.freight = info.data.data.freight;
        $this.activityId = info.data.data.activityId;
        $this.sendList = info.data.data.sendList;
        $this.imgs = info.data.data.imgs;
        $this.packageList = info.data.data.packageList;
        $this.sale = info.data.data.sale;
        $this.comments = info.data.data.comments;
        $this.dateTime = info.data.data.packageList.dateTime;
        $this.avgPrice = info.data.data.packageList[0].avgPrice;
        $this.sellPrice = info.data.data.packageList[0].sellPrice;
        $this.marketPrice = info.data.data.packageList[0].marketPrice;
        $this.stock = info.data.data.packageList[0].stock;
        $this.eggNum = info.data.data.eggNum;
        $this.isApply = info.data.data.isApply;
        $this.clist = info.data.data.clist;
        $this.isPayOrder = info.data.data.isPayOrder;
        $this.phone = info.data.data.clist[0].phone;
        $this.content = info.data.data.content;
        $this.img = info.data.data.clist[0].img;
        $this.contents = info.data.data.clist[0].content;
        $this.choice(0);
      });
    }
  },
  created() {
  },
  mounted() {
    if (!localStorage.getItem("openid")) {
      if (utils.getQueryString("code")) {
        this.logins();
      } else {
        this.login();
      }
    } else {
      this.moun();
    }
  },
  components: {}
};
</script>

<style lang="scss" scoped>
#product_details {
  .mint-header {
    background: #cc3e36;
  }
  .swipeImg {
    width: 100%;
    height: 23.875rem;
    img {
      width: 100%;
      height: 100%;
    }
  }
  /*商品名称*/
  .productInfo {
    padding-bottom: 0.5rem;
    background-color: #fff;
    border-bottom: 2px solid #f1f1f1;
    .infoOne {
      display: flex;
      align-items: center;
      .eggName {
        width: 65%;
        height: 3rem;
        line-height: 3rem;
        font-size: 1.2rem;
        color: #2a1011;
        margin: 0.5rem 1rem;
        overflow: hidden;
        white-space: normal;
        text-overflow: ellipsis;
      }
      .moneyReward {
        width: 34%;
        height: 2.5rem;
        background: #e5e5e4;
        border-radius: 2rem 0 0 2rem;
        display: flex;
        .iconMoney {
          width: 1.5rem;
          height: 1.5rem;
          padding-left: 0.9rem;
          padding-top: 0.4rem;
        }
        .moneyTitle {
          margin-left: 0.5rem;
          color: #c9161d;
          line-height: 2.5rem;
          display: inline-block;
        }
      }
    }
    .infoTwo {
      width: 90%;
      display: flex;
      align-items: center;
      margin: 0rem 1rem;
      color: #8b8989;
      line-height: 2.5rem;
      font-size: 0.9rem;
      .active1 {
        width: 35%;
        text-align: left;
      }
      .active2 {
        width: 30%;
        text-align: center;
      }
      .active3 {
        width: 40%;
        text-align: right;
      }
    }
  }
  /*选择套餐*/
  .chooseSpecial {
    width: 100%;
    margin: 0 auto;
    font-size: 1rem;
    height: 4rem;
    line-height: 4rem;
    display: flex;
    background-color: #fff;
    align-items: center;
    .choose {
      width: 92%;
      padding-left: 0.7rem;
    }
    img {
      width: 1.5rem;
      height: 1.5rem;
    }
  }
  /*全部评价*/
  .all_evaluate {
    padding-bottom: 1.5rem;
    background-color: #fff;

    .titleBox {
      background-color: #f1f1f1 !important;
      font-size: 1rem;
      color: #000;
      padding-left: 1rem;
      line-height: 3rem;
    }
    .userEvaluate {
      background-color: #fff;
      .evaluateBox {
        display: flex;
        align-items: center;
        padding-left: 0.8rem;
        padding-top: 0.8rem;
        .touxiang {
          width: 2.5rem;
          height: 2.5rem;
          border-radius: 50%;
          margin-right: 0.3rem;
        }
        .cenmte {
          height: 3rem;
          margin-left: 0.5rem;
          width: 50%;
          .userName {
            height: 50%;
            display: block;
            font-size: 14px;
            overflow: hidden;
          }
          .userPhone {
            color: #c1c5c8;
            font-size: 12px;
            width: 5rem;
            // margin-right: 5%;
          }
        }
        .zan {
          width: 30%;
          margin: 0 auto;
          margin-left: 10%;

          display: flex;
          text-align: left;
          align-items: center;
          position: relative;
          float: right;
          .xin {
            width: 1.8em;
            height: 2rem;
            margin-right: 0.3rem;
          }
          .praise {
            color: #c1c5c8;
            font-size: 14px;
          }
        }
      }
      .content {
        width: 90%;
        white-space: initial;
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        margin: 0.5rem auto;
      }
    }
    .evaluateBtn {
      color: #000;
      font-size: 1rem;
      width: 8rem;
      line-height: 2.5rem;
      background-color: #fff !important;
      margin: 0 auto;
      margin-top: 2rem;
      margin-bottom: 1rem;
      text-align: center;
      border: 1px solid #1c1c1c;
      border-radius: 5rem;
    }
  }
  /*商品详情*/
  .productDetails {
    margin-bottom: 3.5rem;
    img {
      width: 100%;
    }
  }
  // 定时器
  .scrollbottomWx {
    position: fixed;
    bottom: 7rem;
    right: 3%;
    z-index: 99;

    img {
      width: 1.5rem;
      height: 1.5rem;
      display: block;
    }
  }

  .scrollbottom {
    position: fixed;
    bottom: 4.5rem;
    background-color: rgba(73, 72, 72, 0.8);
    overflow: hidden;
    width: 95%;
    height: 4rem;
    margin-left: 10px;
    z-index: 98;
    border-radius: 0.3rem;

    .anim {
      transition: all 0.5s;
      margin-top: -30px;
    }
    .scrolLton {
      position: absolute;
      left: 0;
      height: 40rem;
      list-style: none;
      margin: 0;
      padding: 0;
      width: 100%;

      li {
        height: 4rem;
        color: #fff;
        font-size: 0.9rem;
        line-height: 4rem;
        overflow: hidden;
        img {
          width: 3rem;
          height: 3rem;
          margin: 0.6rem;
          float: left;
        }
      }
    }
  }
  /*立即购买*/

  .buyAndRob {
    width: 100%;
    display: flex;
    align-items: center;
    bottom: 0;
    position: fixed;
    height: 48px;
    background: #ffffff;
    .rob {
      width: 50%;
      height: 46px;
      line-height: 46px;
      text-align: center;
      background-color: #fff;
      border: 1px solid #cc3e36;
      color: #cc3e36;
    }
    .buy {
      width: 50%;
      height: 46px;
      line-height: 46px;
      font-size: 1rem;
      text-align: center;
      border: 1px solid #cc3e36;
      background: #cc3e36;
      color: #ffffff;
    }
  }
  /*蒙版*/
  .box {
    opacity: 0.9;
    background: #000;
    z-index: 998;
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
  /*领取提醒*/
  .getRemind {
    background: #fff;
    width: 90%;
    color: #000;
    text-align: center;
    position: fixed;
    top: 30%;
    left: 1rem;
    z-index: 999;
    padding-bottom: 1rem;
    border-radius: 0.3rem;
    .remindTop {
      font-size: 20px;
      line-height: 3rem;
    }
    .remindCenter {
      height: 3rem;
      line-height: 2rem;
      font-size: 16px;
    }
    .color-choice {
      display: flex;
      align-items: center;
      flex-wrap: wrap;
      margin-top: 1rem;
      .color {
        border: 1px solid #79797b;
        color: #000;
        border-radius: 5rem;
        width: 30%;
        margin: 0.5rem 0.3rem;
        line-height: 2.3rem;
        text-align: center;
      }
      .active {
        border: 1px solid #c01a20;
        color: #c01a20;
      }
    }
    .btns {
      width: 100%;
      font-size: 0.9rem;
      margin-top: 2rem;
      display: flex;
      align-items: center;
      justify-content: center;
      .cancel {
        width: 40%;
        line-height: 2.5rem;
        border: 1px solid #030000;
        border-radius: 2rem;
        color: #050505;
        margin-right: 2%;
      }
      .true {
        width: 40%;
        line-height: 2.5rem;
        background: #c9161d;
        border-radius: 2rem;
        color: #ffffff;
        margin-left: 2%;
      }
    }
  }
  /*领蛋提醒*/
  .remindOut {
    background: #fff;
    width: 90%;
    color: #000;
    text-align: center;
    position: fixed;
    top: 30%;
    left: 1rem;
    z-index: 999;
    border-radius: 0.3rem;
    .remindTop {
      font-size: 20px;
      line-height: 3rem;
    }
    .remindCenter {
      height: 3rem;
      line-height: 2rem;
      font-size: 16px;
    }
    .remindBot {
      .btn {
        display: inline-block;
        width: 30%;
        margin: 1rem;
        line-height: 2.5rem;
        border-radius: 2rem;
      }
      .btn1 {
        border: 1px solid #ccc;
      }
      .btn2 {
        background: #c9161d;
        color: #ffffff;
      }
    }
  }
  /*领取提醒-下单*/
  .getRemind_xiadan {
    background: #fff;
    width: 90%;
    color: #000;
    text-align: center;
    position: fixed;
    top: 30%;
    left: 50%;
    transform: translate(-50%);
    z-index: 999;
    border-radius: 0.3rem;
    .remindTop {
      font-size: 20px;
      line-height: 3rem;
    }
    .remindCenter {
      height: 3rem;
      line-height: 2rem;
      font-size: 16px;
    }
    .color-choice {
      display: flex;
      align-items: center;
      justify-content: center;
      flex-wrap: wrap;
      margin-top: 1rem;
      width: 100%;
      .color {
        border: 1px solid #79797b;
        color: #000;
        border-radius: 5rem;
        width: 36%;
        margin: 0.5rem 0.3rem;
        line-height: 2.3rem;
        text-align: center;
      }
      .active {
        border: 1px solid #c01a20;
        color: #c01a20;
      }
    }
    .infuse {
      color: #eb2f36;
      font-size: 1rem;
      padding-left: 1rem;
      line-height: 3rem;
    }
    .btns {
      width: 100%;
      font-size: 0.9rem;
      margin: 2rem auto;
      display: flex;
      align-items: center;
      justify-content: center;
      .cancel {
        width: 40%;
        line-height: 2.5rem;
        border: 1px solid #030000;
        border-radius: 2rem;
        color: #050505;
        margin-right: 2%;
      }
      .true {
        width: 40%;
        line-height: 2.5rem;
        background: #c9161d;
        border-radius: 2rem;
        color: #ffffff;
        margin-left: 2%;
      }
    }
  }
  /*选择规格*/
  .choice-style {
    .choice-box {
      position: fixed;
      background: #fff;
      width: 100%;
      height: 31rem;
      bottom: 0;
      z-index: 999;
      overflow-y: auto;
      overflow-x: hidden;
      .goods-details {
        display: flex;
        padding: 1rem 0 0 1rem;
        .good-picture {
          width: 6rem;
          height: 6rem;
        }
        .money {
          padding-left: 1rem;
          .price {
            font-size: 1.2rem;
            line-height: 1.0683760684rem;
            text-align: left;
            color: #c01a20;
            padding-top: 0.5555555556rem;
            margin-bottom: 0.5555555556rem;

            .greyIcon {
              margin-left: 0.5rem;
              color: rgb(95, 92, 92);
              text-decoration: line-through;
              font-size: 1rem;
            }
          }
          .style {
            font-family: PingFangSC-Regular;
            font-size: 1rem;
            color: #333;
            text-align: left;
            margin: 0.2136752137rem 0 0 0.5128205128rem;
          }
        }
        .close {
          width: 1rem;
          height: 1rem;
          position: relative;
          float: right;
          right: -7.8rem;
        }
      }
      .choice {
        .colors {
          margin: 2rem 0 0 0;
          padding-left: 1rem;
          font-size: 1.2rem;
        }
        .color-choice {
          display: flex;
          align-items: center;
          flex-wrap: wrap;
          margin-top: 1rem;
          .color {
            border: 1px solid #79797b;
            color: #000;
            border-radius: 5rem;
            width: 32%;
            margin: 0.5rem 0.3rem;
            line-height: 2.3rem;
            text-align: center;
          }
          .active {
            border: 1px solid #c01a20;
            color: #c01a20;
          }
        }
      }
      .infuse {
        color: #eb2f36;
        font-size: 1rem;
        padding-left: 1rem;
        line-height: 3rem;
      }
      .numbers {
        display: flex;
        align-items: center;
        .number {
          padding-left: 1rem;
          font-size: 1.2rem;
          line-height: 3rem;
        }
        .modified {
          display: flex;
          align-items: center;
          justify-content: center;
          border-radius: 5rem;
          border: 1px solid #79797b;
          width: 9rem;
          height: 2.3rem;
          position: relative;
          right: -5rem;
          .low {
            background: #000;
            width: 1rem;
            height: 0.1rem;
            margin-right: 0.3rem;
          }
          input {
            border: none;
            width: 4.6rem;
            text-align: center;
            font-size: 1.4rem;
          }
          .plus {
            font-size: 2rem;
            margin-left: 0.3rem;
            margin-top: -0.2rem;
          }
        }
      }
      .yes {
        font-size: 1.3rem;
        color: #c01a20;
        border-top: 1px solid #79797b;
        position: fixed;
        bottom: 0;
        width: 100%;
        text-align: center;
        line-height: 3.5rem;
      }
    }
  }
}
</style>