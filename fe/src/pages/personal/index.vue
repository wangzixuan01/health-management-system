<template>
  <!-- 个人中心组件 -->
  <div class="personal-wraper">
    <explain :explainName="state.explainName"></explain>

    <!-- 个人信息 -->
    <div class="personal-msg-wraper">
      <div class="head">
        <img :src="state.userInfo.headpic" width="80" height="80" alt="头像" />
      </div>
      <div class="phone">
        <img
          src="./images/phone.png"
          width="30"
          height="30"
          class="phone-ico"
          alt="手机"
        />
        <span class="phone-num">{{ state.userInfo.phone }}</span>
      </div>
      <router-link
        tag="a"
        :to="{ path: '/personal-edit' }"
        class="setting"
      ></router-link>
    </div>

    <footer-nav :navName="state.navName"></footer-nav>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from 'vue'

import Explain from '@/components/header-explain/index.vue'
import FooterNav from '@/components/footer-nav/index.vue'

import { getUserInfo } from '@/api/personal'
import router from '@/router'
import { Dialog } from 'vant'

interface UserInfo {
  phone: string
  headpic: string
}

interface CouponRecord {
  num: number
  coupon_type: string
}

interface Coupon {
  coupon_explain: string
  coupon_ico_path: string
  coupon_name: string
  coupon_recived_num: number
  id: number
  status: number
}

interface State {
  explainName: string
  navName: string
  isUnionpay: boolean
  isVisa: boolean
  isJinnang: boolean
  isGaodaowu: boolean
  userInfo: UserInfo
  unionNum: number
  visaNum: number
  jinnangNum: number
  gaodaowuNum: number
  unionCouponList: Coupon[]
  visaCouponList: Coupon[]
  jinnangCouponList: Coupon[]
  gaodaowuCouponList: Coupon[]
}

export default defineComponent({
  components: {
    Explain,
    FooterNav
  },
  setup(props: {}, context: {}) {
    const state: State = reactive<State>({
      explainName: '个人中心',
      navName: 'personal',
      isUnionpay: false,
      isVisa: false,
      isJinnang: false,
      isGaodaowu: false,
      userInfo: { phone: '', headpic: 'https://search-operate.cdn.bcebos.com/df98ac65f7f787e498d593ecfc1d75e1.jpeg' },
      unionNum: 0,
      visaNum: 0,
      jinnangNum: 0,
      gaodaowuNum: 0,
      unionCouponList: [],
      visaCouponList: [],
      jinnangCouponList: [],
      gaodaowuCouponList: []
    })

    const uid: number = window.sessionStorage.uid
    if (uid) {
      _getUserInfo(uid)
    } else {
      router.push({ path: '/account/login' })
    }

    function _getUserInfo(id: number) {
      try {
        getUserInfo(id)
          .then(res => {
            console.log(res.data)
            const { code, data, message } = res.data
            if (code === 0) {
              const { headpic, phone, name, gender, age } = data
              state.userInfo.phone = phone
            } else {
              Dialog.alert({ message })
            }
          })
          .catch(error => {
            Dialog.alert({
              message: error
            })
          })
      } catch (e) {
        router.push({ name: 'Login' })
      }
    }

    return {
      state
    }
  }
})
</script>

<style scoped lang="less" rel="stylesheet/less">
@import './tour-app-personal.less';
@import '../../static/less/coupon.less';

.gift-list-wraper {
  .title {
    margin-bottom: 10px;
    border-left: 5px solid #ffb000;
    padding-left: 10px;
    font-size: 15px;
  }
  .gift-item {
    .brief {
      position: relative;
      margin-bottom: 10px;
      height: 55px;
      background-color: #fff;
      .gift-ico {
        display: inline-block;
        position: absolute;
        top: 10px;
        left: 10px;
        width: 35px;
        height: 35px;
        background-repeat: no-repeat;
        background-position: center center;
        background-size: 35px 35px;
      }
      .unionpay-ico {
        background-image: url('./images/list_unionpay.png');
      }
      .visa-ico {
        background-image: url('./images/list_visa.png');
      }
      .jinnang-ico {
        background-image: url('./images/jinnangtuan.png');
      }
      .gaodaowu-ico {
        background-image: url('./images/list_dutyfree.png');
      }
      .gift-name {
        display: inline-block;
        position: absolute;
        top: 10px;
        left: 55px;
        height: 35px;
        line-height: 35px;
      }
      .flex-ico {
        display: inline-block;
        position: absolute;
        right: 16px;
        top: 22px;
        width: 12px;
        height: 12px;
        background-repeat: no-repeat;
        background-position: center center;
        background-size: 12px 12px;
      }
      .flex-ico-down {
        background-image: url('./images/flex_down.png');
      }
      .flex-ico-up {
        background-image: url('./images/flex_up.png');
      }
    }
  }
}

.gift-detail-on {
  display: block;
}

.gift-detail-off {
  display: none;
}
</style>
