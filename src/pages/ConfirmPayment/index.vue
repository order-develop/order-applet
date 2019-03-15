<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../../images/header.png"/>
    </div>

    <div class="oPay-counter clearfix">
      <div class="today clearfix">
        <div class="today-text">今日套餐</div>
        <div class="money-box clearfix">
          <div class="count-box clearfix">
            <div class="count clearfix">
              <div @click="decrement" class="Degression">－</div>
              <p class="count-num">{{ content }}</p>
              <div @click="increment" class="Ascending">＋</div>
            </div>
          </div>
          <div class="money">
            <div class="symbol">￥</div>
            {{ univalence }}
          </div>
        </div>
      </div>
      <div class="menuList-Box">
        <ul class="list">
          <li v-for="(item,index) in theList" :key="index" :id="item.id" class="menuList" :cTime="item.cTime">-&nbsp;{{ item.foodName }}</li>
        </ul>
      </div>
    </div>
    <div class="order">
      <button @click="paySucceed">支付成功</button>
    </div>
    <div class="nav">
      <div class="pay-box clearfix">
        <div class="total">
          <div class="summation">合计</div>
          <div class="money">
            <div class="symbol">￥</div>
            {{ MoneyTotal }}
          </div>
        </div>
        <div class="goPay" @click="goPay" >去支付&nbsp;＞&nbsp;</div>
      </div>
    </div>
  </div>
</template>

<script>
const APPLET_URL = require('./../../../static/js/address')
export default {
  // computed: {
  //   count () {
  //     return store.state.count
  //   }
  // },
  data () {
    return {
      univalence: '',
      MoneyTotal: '',
      theList: [],
      userMsg: {},
      foodPackageId: '',
      userId: '',
      orderId: '',
      tel: '',
      content: 1
    }
  },
  onLoad (option) {
    let _that = this
    _that.foodPackageId = option.foodPackageId
    _that.orderId = option.orderId
    _that.userMsg = option.userMsg
    _that.userId = option.userId
  },
  mounted () {
    let _that = this
    if (_that.orderId != null) {
      wx.request({
        url: APPLET_URL.url + '/user/order/userId/orderId?=' + Math.random(),
        data: {
          orderId: _that.orderId
        },
        method: 'GET',
        success: function (res) {
          console.log(res.data)
          _that.userId = res.data.data.userId
          _that.tel = res.data.data.tel
          _that.MoneyTotal = res.data.data.packagePrice
          _that.content = res.data.data.packageNumber
          console.log(_that.MoneyTotal)
        }
      })
      wx.request({
        url: APPLET_URL.url + '/chef/food?r=' + Math.random(),
        data: {},
        method: 'GET',
        success (res) {
          if (res.data) {
            _that.theList = res.data.data
          }
        }
      })
    } else {
      wx.login({
        success: function (res) {
          wx.request({
            url: APPLET_URL.url + '/WX/getOpenId?=' + Math.random(),
            data: {
              code: res.code
            },
            method: 'GET',
            success: function (res) {
              console.log(res.data)
              wx.request({
                url: APPLET_URL.url + '/user/login/openId?=' + Math.random(),
                data: {
                  openId: res.data.openid
                },
                method: 'GET',
                success: function (res) {
                  if (res.data) {
                    _that.userId = res.data.data.id
                    _that.userMsg = res.data.data
                    wx.request({
                      url: APPLET_URL.url + '/chef/food?r=' + Math.random(),
                      data: {},
                      method: 'GET',
                      success (res) {
                        if (res.data) {
                          _that.theList = res.data.data
                          _that.univalence = res.data.packagePrice
                          _that.MoneyTotal = res.data.packagePrice
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
    }
  },
  methods: {
    increment () {
      // store.commit('increment')
      this.content += 1
      this.MoneyTotal = this.univalence * this.content
    },
    decrement () {
      if (this.content < 2) {
        return
      }
      // store.commit('decrement')
      this.content -= 1
      this.MoneyTotal = this.univalence * this.content
    },
    payError () {
      wx.redirectTo({
        url: '/pages/payerror/main?'
      })
    },
    paySucceed () {
      let _that = this
      wx.request({
        url: APPLET_URL.url + '/user/order/orderId?=' + Math.random(),
        data: {
          orderId: _that.orderId
        },
        method: 'PUT',
        header: {
          'content-type': 'application/x-www-form-urlencoded'
        },
        success (res) {
          if (res.data) {
            wx.redirectTo({
              url: '/pages/paysucceed/main?'
            })
          }
        }
      })
    },
    goPay () {
      let _that = this
      wx.request({
        url: APPLET_URL.url + '/user/order/userId/ordering?r=' + Math.random(),
        data: {
          userId: _that.userId,
          foodPackageId: _that.foodPackageId,
          packagePrice: _that.univalence,
          tel: _that.tel,
          packageNumber: _that.content
        },
        method: 'POST',
        success (res) {
          if (res.data) {
            wx.request({
              url: APPLET_URL.url + '/WX/pay?r=' + Math.random(),
              data: {
                out_trade_no: res.data.data.orderId,
                money: res.data.minuteTotalPrice,
                openid: res.data.openId
              },
              method: 'POST',
              success () {
              }
            })
          }
        }
      })
    }
  }
}
</script>

<style>
.counter-warp {
  text-align: center;
}
</style>
