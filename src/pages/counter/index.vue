<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../images/header.png"/>
    </div>

    <ul class="menu-list">
      <li v-for="(item,index) in theList" :key="index" :id="item.id" class="cuisine" :cTime="item.cTime">{{ item.foodName }}</li>
    </ul>

    <div class="order">
      <button @click="order" :id="Package">点餐</button>
    </div>

    <ul class="nav">
      <li>
        <img src="../images/order.png"/><a @click="">点餐</a>
      </li>
      <li @click="indent">
        <img src="../images/indent.png" class="indent"/><a >订单</a>
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
      theList: [],
      Package: '',
      foodPackageId: ''
    }
  },
  onLoad (option) {
    let _that = this
    console.log(option.userMsg)
    _that.userMsg = option.userMsg
  },
  mounted () {
    let _that = this
    wx.request({
      url: APPLET_URL.url + '/chef/food?r=' + Math.random(),
      data: {},
      method: 'GET',
      success (res) {
        if (res.data) {
          _that.theList = res.data.data
          _that.Package = res.data.packagePrice
          _that.foodPackageId = res.data.foodPackageId
          console.log(res.data)
        }
      }
    })
  },
  methods: {
    order () {
      let _that = this
      wx.redirectTo({
        url: '/pages/ConfirmPayment/main?userMsg=' + _that.userMsg + '&foodPackageId=' + _that.foodPackageId
      })
    },
    myself () {
      wx.redirectTo({
        url: '/pages/myself/main?'
      })
    },
    indent () {
      wx.redirectTo({
        url: '/pages/indent/main?'
      })
    }
  }
}
</script>

<style>

</style>
