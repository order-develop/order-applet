<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../images/header.png"/>
    </div>

    <div>
      <div class="order-history">今日订单</div>
      <ul class="order-list clearfix">
        <li v-for="(item,index) in list" :key="index"><div class="cTime">{{item.cTime}}订单<div style="float: right">＞</div></div>
          <div v-if="item.isComplete === 1" class="state" @click="goPay(item.orderId, item.userId)">去支付</div>
          <div v-if="item.isComplete === 2" class="state" @click="takeFood(item.orderId, item.userId)">去取餐</div>
          <div v-if="item.isComplete === 3" class="state">已完成</div>
        </li>
      </ul>
    </div>

    <ul class="nav">
      <li @click="goOrder">
        <img src="../images/theorder.png"/><a >点餐</a>
      </li>
      <li>
        <img src="../images/theindent.png" class="indent"/><a @click="indent" style="color: #a4110f;">订单</a>
      </li>
      <li @click="myself">
        <img src="../images/myself.png" class="myself"/><a >我的</a>
      </li>
    </ul>

  </div>
</template>

<script>
const APPLET_URL = require('./../../../static/js/address')

export default {
  data () {
    return {
      timeMsg: '2019.01.23订单',
      finish: '未支付',
      height: {},
      list: [],
      size: 10,
      swiper: null,
      carousel: [],
      promotion: [],
      cates: []
    }
  },
  mounted () {
    let _that = this
    _that.getPromotion()
  },
  methods: {
    getPromotion () {
      let _that = this
      wx.login({
        success: function (res) {
          wx.request({
            url: APPLET_URL.url + '/WX/getOpenId',
            data: {
              code: res.code
            },
            method: 'GET',
            success: function (res) {
              wx.request({
                url: APPLET_URL.url + '/user/login/openId',
                data: {
                  openId: res.data.openid
                },
                method: 'GET',
                success: function (res) {
                  if (res.data) {
                    _that.userName = res.data.data.nickName
                    _that.userTel = res.data.data.tel
                    _that.avatarUrl = res.data.data.avatarUrl
                    wx.request({
                      url: APPLET_URL.url + '/user/order/userId/allStatus?r=' + Math.random(),
                      data: {
                        userId: res.data.data.id
                      },
                      method: 'GET',
                      success (res) {
                        if (res.data) {
                          _that.list = res.data.data
                        }
                      }
                    })
                  }
                }
              })
            }
          })
        }
      })
    },
    goPay (orderId, userId) {
      wx.navigateTo({
        url: '/pages/ConfirmPayment/main?orderId=' + orderId + '&userId=' + userId + '&thePay=1'
      })
    },
    myself () {
      wx.navigateTo({
        url: '/pages/myself/main?'
      })
    },
    goOrder () {
      wx.navigateTo({
        url: '/pages/counter/main?'
      })
    },
    takeFood (orderId, userId) {
      wx.navigateTo({
        url: '/pages/QRcode/main?orderId=' + orderId + '&userId=' + userId
      })
    }
  }
}
</script>

<style>

</style>
