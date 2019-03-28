<template>
  <div class="counter-warp">
    <div class="myself-heard">
      <div class="head-portrait"><img :src="avatarUrl" class="avatar"></div>
      <div class="user-mag">
        <div class="username">{{userName}}</div>
        <div class="usertel">℡：{{userTel}}</div>
      </div>
    </div>

    <div>
      <div class="order-history">历史订单</div>
      <ul class="order-list clearfix">
        <li v-for="(item,index) in list" :key="index" class="index"><div class="cTime">{{item.shortDate}}订单<div style="float: right">＞</div></div>
          <div v-if="item.status === 0" class="state">未支付</div>
          <div v-if="item.status === 1" class="state">已支付</div>
          <div v-if="item.status === 2" class="state">过期未支付</div>
        </li>
      </ul>
    </div>

    <ul class="nav">
      <li  @click="goOrder">
        <img src="../images/theorder.png"/><a>点餐</a>
      </li>
      <li @click="indent">
        <img src="../images/indent.png" class="indent"/><a  >订单</a>
      </li>
      <li>
        <img src="../images/themyself.png" class="myself"/><a style="color: #a4110f;">我的</a>
      </li>
    </ul>
  </div>
</template>

<script>
const APPLET_URL = require('./../../../static/js/address')
export default {
  data () {
    return {
      userMsg: {},
      list: [],
      userName: '',
      userTel: '',
      avatarUrl: '',
      userId: '',
      pageNum: '',
      pageSize: '',
      total: '',
      pages: '',
      pageslength: ''
    }
  },
  mounted () {
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
                  _that.userId = res.data.data.id
                  _that.getUserId()
                }
              }
            })
          }
        })
      }
    })
  },
  components: {
  },
  methods: {
    goOrder () {
      wx.navigateTo({
        url: '/pages/counter/main?'
      })
    },
    indent () {
      wx.navigateTo({
        url: '/pages/indent/main?'
      })
    },
    getUserId () {
      let _that = this
      wx.request({
        url: APPLET_URL.url + '/user/userId?r=' + Math.random(),
        data: {
          userId: _that.userId,
          pageNum: _that.pageNum,
          pageSize: _that.pageSize
        },
        method: 'GET',
        success (res) {
          if (res.data) {
            _that.list = res.data.data.list
            _that.pageNum = res.data.data.nextPage
            _that.pageSize = res.data.data.pageSize
            _that.total = res.data.data.total
            _that.pages = res.data.data.pages
          }
        }
      })
    },
    getRegisterInfo () {
      let listNum = []
      wx.createSelectorQuery().selectAll('.index').boundingClientRect(function (rects) {
        rects.forEach(function (rect) {
          listNum.push(rect.dataset)
        })
        _that.pageslength = listNum.length
      }).exec()
      let _that = this
      if (_that.total > _that.pageslength) {
        wx.request({
          url: APPLET_URL.url + '/user/userId?r=' + Math.random(),
          data: {
            userId: _that.userId,
            pageNum: _that.pageNum,
            pageSize: _that.pageSize
          },
          method: 'GET',
          success (res) {
            if (res.data.data.list) {
              if (res.data.data.nextPage && res.data.data.nextPage > 0) {
                res.data.data.list.forEach(function (item) {
                  _that.list.push(item)
                })
                _that.pageNum = res.data.data.nextPage
                _that.pageSize = res.data.data.pageSize
              }
            }
          }
        })
      }
    }
  },
  onReachBottom: function () {
    let _that = this
    _that.getRegisterInfo()
  }
}
</script>
