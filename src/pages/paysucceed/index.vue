<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../images/header.png"/>
    </div>

    <div class="ordermsg">{{timeMsg}}</div>
    <div class="ordermsg">{{finish}}</div>

    <ul class="nav">
      <li @click="goOrder">
        <img src="../images/theorder.png"/><a >点餐</a>
      </li>
      <li @click="indent">
        <img src="../images/theindent.png" class="indent"/><a @click="indent" style="color: #a4110f;">订单</a>
      </li>
      <li @click="myself">
        <img src="../images/myself.png" class="myself"/><a >我的</a>
      </li>
    </ul>
  </div>
</template>

<script>
import card from '@/components/card'
export default {
  data () {
    return {
      timeMsg: '2019.01.23订单',
      finish: '订单已完成'
    }
  },

  components: {
    card
  },
  methods: {
    goOrder () {
      wx.redirectTo({
        url: '/pages/counter/main?'
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
