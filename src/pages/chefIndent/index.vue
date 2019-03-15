<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../../images/header.png"/>
    </div>

    <div class="indent-list">
      <div class="time-title">{{cTime}}订单</div>
      <div class="population-box clearfix" @click="examine">
        <div class="population-num">订餐人数</div>
        <div class="population">{{population}}人</div>
      </div>
      <div class="user-list-box">
        <ul class="user-list">
          <li v-for="(item, index) in userName" :key="index">{{item}}</li>
        </ul>
      </div>
    </div>
    <ul class="nav">
      <li  @click="goOrder">
        <img src="../../images/theorder.png"/><a>点餐</a>
      </li>
      <li @click="indent" >
        <img src="../../images/indent.png" class="indent"/><a style="color: #a4110f;">订单</a>
      </li>
      <li @click="myself">
        <img src="../../images/themyself.png" class="myself"/><a >我的</a>
      </li>
    </ul>
  </div>
</template>


<script>
import card from '@/components/card'
const APPLET_URL = require('./../../../static/js/address')

export default {
  data () {
    return {
      userName: [],
      unfold: '﹀',
      population: '',
      cTime: ''
    }
  },
  components: {
    card
  },
  mounted () {
    let _that = this
    wx.request({
      url: APPLET_URL.url + '/chef/foodPackageId?r=' + Math.random(),
      data: {
        foodPackageId: 1
      },
      method: 'GET',
      success (res) {
        if (res.data) {
          _that.population = res.data.count
          _that.cTime = res.data.today
          _that.userName = res.data.userNames
        }
      }
    })
  },
  methods: {
    myself () {
      wx.redirectTo({
        url: '/pages/myself/main?'
      })
    },
    indent () {
      wx.redirectTo({
        url: '/pages/chefIndent/main?'
      })
    },
    goOrder () {
      wx.redirectTo({
        url: '/pages/chef/main?'
      })
    }
  }
}
</script>
