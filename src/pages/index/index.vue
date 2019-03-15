<template>
  <div class="container" @click="clickHandle('test click', $event)">
    <div class="header-img">
      <img src="../../images/header.png"/>
    </div>
    <div class="info-group clearfix">
      <div class="name clearfix"><div>姓名</div><input type="text" class="input-style"  v-model="motto"></div>
      <div class="phone clearfix"><div>电话</div><input type="text" class="input-style"  v-model="phone"></div>
    </div>
    <div class="bound">
      <button @click="binding" open-type="getUserInfo" @getuserinfo="bindGetUserInfo" :userId="userId" :openId="openId">绑定</button>
    </div>
  </div>
</template>

<script>
import card from '@/components/card'
const APPLET_URL = require('./../../../static/js/address')
export default {
  data () {
    return {
      motto: '',
      phone: '',
      userInfo: {},
      userId: '',
      openId: '',
      userMsg: ''
    }
  },

  components: {
    card
  },
  mounted () {
    // 一进来看看用户是否授权过
    this.getSetting()
  },
  methods: {
    bindViewTap () {
      const url = '../logs/main'
      wx.navigateTo({ url })
    },
    getUserInfo () {
      // 调用登录接口
      wx.login({
        success: () => {
          wx.getUserInfo({
            success: (res) => {
              console.log(res)
              this.userInfo = res.userInfo
              console.log(this.userInfo)
              this.address = this.userInfo.country + ' ' + this.userInfo.province + ' ' + this.userInfo.city
              console.log(this.address)
            }
          })
        }
      })
    },
    clickHandle (msg, ev) {
      console.log('clickHandle:', msg, ev)
    },
    binding () {
      let reg = /^[0-9]+.?[0-9]*$/
      if (this.motto === '' || this.motto == null || this.phone === '' || this.phone == null) {
        wx.showModal({
          title: '提示',
          content: '姓名或电话号不可为空',
          success: function (res) {
            if (res.confirm) {
              console.log('用户点击确定')
            } else {
              console.log('用户点击取消')
            }
          }
        })
      } else if (this.phone.length !== 11 || !reg.test(this.phone)) {
        wx.showModal({
          title: '提示',
          content: '请输入正确手机号',
          success: function (res) {
            if (res.confirm) {
              console.log('用户点击确定')
            } else {
              console.log('用户点击取消')
            }
          }
        })
      }
      // 判断小程序的API，回调，参数，组件等是否在当前版本可用。  为false 提醒用户升级微信版本
      if (wx.canIUse('button.open-type.getUserInfo')) {
        // 用户版本可用
      } else {
        console.log('请升级微信版本')
      }
    },
    getSetting () {
      wx.getSetting({
        success: function (res) {
          if (res.authSetting['scope.userInfo']) {
            wx.getUserInfo({
              success: function (res) {
                /* 用户已经授权过 */
                console.log('用户已经授权过')
                wx.login({
                  success: function (res) {
                    wx.request({
                      url: APPLET_URL.url + '/WX/getOpenId',
                      data: {
                        code: res.code
                      },
                      method: 'GET',
                      success: function (res) {
                        console.log(res.data)
                        wx.request({
                          url: APPLET_URL.url + '/user/login/openId',
                          data: {
                            openId: res.data.openid
                          },
                          method: 'GET',
                          success: function (res) {
                            if (res.data) {
                              wx.redirectTo({
                                url: '/pages/counter/main?userMsg=' + JSON.stringify(res.data.data)
                              })
                            }
                          }
                        })
                      }
                    })
                  }
                })
              }
            })
          } else {
            console.log('用户还未授权过')
          }
        }
      })
    },
    bindGetUserInfo (e) {
      if (e.mp.detail.rawData) {
        let Uname = this.motto
        let Uphone = this.phone
        console.log(Uname + '和' + Uphone)
        console.log(e.mp.detail)
        let _that = this
        wx.login({
          success: function (res) {
            if (res.code) {
              wx.request({
                url: APPLET_URL.url + '/WX/getOpenId',
                data: {
                  code: res.code
                },
                method: 'GET',
                success: function (res) {
                  console.log(res.data.openid)
                  _that.openId = res.data.openid
                  wx.request({
                    url: APPLET_URL.url + '/user/register?r=' + Math.random(),
                    data: {
                      realName: Uname,
                      tel: Uphone,
                      nickName: e.mp.detail.userInfo.nickName,
                      gender: e.mp.detail.userInfo.gender,
                      roleId: 0,
                      avatarUrl: e.mp.detail.userInfo.avatarUrl,
                      openId: res.data.openid
                    },
                    method: 'POST',
                    success (red) {
                      if (red.data) {
                        wx.request({
                          url: APPLET_URL.url + '/user/login/openId',
                          data: {
                            openId: res.data.openid
                          },
                          method: 'GET',
                          success: function (res) {
                            if (res.data) {
                              wx.redirectTo({
                                url: '/pages/counter/main?userMsg=' + JSON.stringify(res.data.data)
                              })
                            }
                          }
                        })
                      }
                    }
                  })
                }
              })
            }
          }
        })
      } else {
        console.log('用户按了拒绝按钮')
      }
    }
  },
  created () {
    // 调用应用实例的方法获取全局数据
    this.getUserInfo()
  }
}
</script>
