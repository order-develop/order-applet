<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../../images/header.png"/>
    </div>

    <div class="QRcodeBox">
      <div class="QRcodeTitle">请出示给厨师验证取餐</div>
      <canvas style="width: 100px; height: 100px;" canvas-id="canvasId" class="QRcode"></canvas>
      <img :src="src" alt="">
    </div>

    <ul class="nav">
      <li  @click="goOrder">
        <img src="../../images/theorder.png"/><a >点餐</a>
      </li>
      <li @click="indent">
        <img src="../../images/theindent.png" class="indent"/><a @click="indent" style="color: #a4110f;">订单</a>
      </li>
      <li @click="myself">
        <img src="../../images/myself.png" class="myself"/><a >我的</a>
      </li>
    </ul>
  </div>
</template>

<script>
import card from '@/components/card'
import { code } from '../../utils/code'
const APPLET_URL = require('./../../../static/js/address')
export default {
  data () {
    return {
      orderId: '',
      src: '',
      // 画布的宽高要和object里面的宽高保持一致
      object: {
        text: APPLET_URL.url + 'pages/verification/main?orderId=' + this.orderId, // 二维码的内容
        width: 150, // 二维码的宽
        height: 150, // 二维码的高
        canvasId: 'canvasId'
      }
    }
  },

  components: {
    card
  },
  onLoad (option) {
    let _that = this
    _that.orderId = option.orderId
  },
  mounted () {
    this.getCode(this.object)
  },
  methods: {
    goPay () {
      wx.redirectTo({
        url: '/pages/ConfirmPayment/main?'
      })
    },
    myself () {
      wx.redirectTo({
        url: '/pages/myself/main?'
      })
    },
    goOrder () {
      wx.redirectTo({
        url: '/pages/counter/main?'
      })
    },
    getCode (object) {
      let that = this
      // 调用生成二维码的函数
      that.src = code(object)
    },
    indent () {
      wx.redirectTo({
        url: '/pages/payerror/main?'
      })
    }
  }
}
</script>
