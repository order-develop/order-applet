<template>
  <div class="container">
    <div class="header-img">
      <img src="../images/header.png"/>
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

  mounted () {
    // 一进来看看用户是否授权过
    this.getSetting()
  },

  components: {
    card
  },

  methods: {
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
      } else if (this.phone.length === 11 || reg.test(this.phone || this.motto !== '' || this.motto != null || this.phone !== '' || this.phone != null)) {
        this.getSetting()
      }
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
                      console.log('register ==> ' + red.data.code)
                      if (red.data.code === 1) {
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
                      } else {
                        _that.getSetting()
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
    },
    getSetting () {
      wx.getSetting({
        success: function (res) {
          if (res.authSetting['scope.userInfo']) {
            wx.getUserInfo({
              success: function (res) {
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
                            if (res.data.code === 1) {
                              if (res.data) {
                                wx.redirectTo({
                                  url: '/pages/counter/main?userMsg=' + JSON.stringify(res.data.data)
                                })
                              }
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
    }
  }
}
</script>
