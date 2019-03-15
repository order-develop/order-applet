<template>
  <div class="counter-warp">
    <div class="header-img">
      <img src="../../images/header.png"/>
    </div>

    <div class="AddFood">
      <ul class="AddList clearfix">
        <li v-for="(item,index) in foodlist" :key="index" class="food-box clearfix">
          <input type="text" v-model="item.foodName" :id="item.id" class="this-input" :disabled="item.disabled" ref="'input' + item.id" />
          <button @click="edit(item.id)" class="edit" v-show="item.btnStatus == 0" :id="item.id">编辑</button>
          <button @click="keep(item.id)" class="keep" v-show="item.btnStatus == 1">保存</button>
          <button @click="del(item.id)" class="del">删除</button>
        </li>
      </ul>
      <div class="add-btn clearfix">
        <input type="text" v-model="food" >
        <div class="btn-group">
          <button @click="AddFood" class="addfood">添加菜品</button>
          <button @click="SaveFood" class="savefood">保存菜单</button>
        </div>
      </div>
    </div>

    <ul class="nav">
      <li  @click="goOrder">
        <img src="../../images/order.png"/><a style="color: #a4110f;">点餐</a>
      </li>
      <li @click="indent" >
        <img src="../../images/indent.png" class="indent"/><a >订单</a>
      </li>
      <li @click="myself">
        <img src="../../images/myself.png" class="myself"/><a >我的</a>
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
      foodlist: [],
      food: ''
    }
  },
  components: {
    card
  },
  mounted () {
    let _that = this
    _that.GetList()
  },
  methods: {
    indent () {
      wx.navigateTo({
        url: '/pages/chefIndent/main?'
      })
    },
    myself () {
      wx.navigateTo({
        url: '/pages/myself/main?'
      })
    },
    GetList () {
      let _that = this
      wx.request({
        url: APPLET_URL.url + '/chef/food?r=' + Math.random(), // 仅为示例，并非真实的接口地址
        data: {},
        method: 'GET',
        success (res) {
          if (res.data) {
            res.data.data.forEach(function (item) {
              item.btnStatus = 0
              item.disabled = true
              _that.foodlist.push(item)
            })
          }
        }
      })
    },
    AddFood () {
      let _that = this
      if (_that.food === '' || _that.food == null || _that.food === '' || _that.food == null) {
        wx.showModal({
          title: '提示',
          content: '菜品名不可为空',
          success: function (res) {
            if (res.confirm) {
              console.log('用户点击确定')
            } else {
              console.log('用户点击取消')
            }
          }
        })
      } else {
        let newFoot = _that.food
        wx.request({
          url: APPLET_URL.url + '/chef/foodName?r=' + Math.random(),
          data: JSON.stringify({
            foodName: newFoot
          }),
          dataType: 'JSON',
          method: 'POST',
          success (res) {
            if (res.data) {
              let dataList = []
              dataList.push(JSON.parse(res.data))
              let food = {foodName: _that.food, id: dataList[0].data.id, disabled: true, btnStatus: 0}
              _that.foodlist.push(food)
              _that.food = null
            }
          }
        })
      }
    },
    del (id) {
      let _that = this
      _that.foodlist.some((item, i) => {
        if (id === item.id) {
          wx.showModal({
            title: '提示',
            content: '是否删除此菜品',
            success: function (res) {
              if (res.confirm) {
                wx.request({
                  url: APPLET_URL.url + '/chef/' + id + '?r=' + Math.random(),
                  data: {},
                  dataType: 'JSON',
                  method: 'DELETE',
                  success (res) {
                    if (res.data) {
                      _that.foodlist.splice(i, 1)
                    }
                  }
                })
              } else {
                console.log('用户点击取消')
              }
            }
          })
          return true
        }
      })
    },
    edit (id) {
      let _that = this
      _that.foodlist.some((item, i) => {
        if (id === item.id) {
          item.btnStatus = 1
          item.disabled = !item.disabled
          item.editName = item.foodName
        }
      })
    },
    keep (id) {
      let _that = this
      _that.foodlist.some((item, i) => {
        if (id === item.id) {
          wx.showModal({
            title: '提示',
            content: '是否保存此菜品',
            success: function (res) {
              if (res.confirm) {
                wx.request({
                  url: APPLET_URL.url + '/chef/foodPackageId?r=' + Math.random(), // 仅为示例，并非真实的接口地址
                  data: {
                    foodName: item.foodName,
                    foodId: item.id
                  },
                  method: 'PUT',
                  header: {
                    'content-type': 'application/x-www-form-urlencoded'
                  },
                  success (res) {
                    if (res.data) {
                      item.foodName = item.editName
                      item.foodName = res.data.data.foodName
                      item.disabled = !item.disabled
                      item.btnStatus = 0
                    }
                  }
                })
              } else {
                item.foodName = item.editName
                item.disabled = !item.disabled
                item.btnStatus = 0
              }
            }
          })
          return true
        }
      })
    },
    SaveFood () {

    }
  }
}
</script>
