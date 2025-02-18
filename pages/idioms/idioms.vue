<template>
  <view class="container">
    <view class="title">
      <view class="options">
        <view class="left" @click="$refs.withdrawalDialogRef.open()">
          <image mode="widthFix" src="/static/images/weixin_pay_icon.png"/>
          <text>提现</text>
        </view>

        <view class="grade">第{{ currentIdiomsIndex + 1 }}关</view>

        <view class="right">
          <image mode="widthFix" src="/static/images/hongbao.png"/>
          <text>{{ totalMoney }}</text>
        </view>
      </view>

      <view class="sub-title">看图猜成语</view>
    </view>

    <view class="sudoku-board">
      <image mode="widthFix" :src="`/static/images/idiomsPics/${idiomsList[currentIdiomsIndex].idioms}.jpg`"/>

      <view class="input-box">
        <text>答案：</text>
        <input v-model="idioms" @focus="showError = false; focus = true" :focus="focus" />
        <view class="error" v-show="showError" @click="showError = false; focus = true">错误！</view>
      </view>

      <view class="submit">
        <button @click="submit">确认</button>
      </view>
    </view>

    <uni-popup ref="hongbaoRef" :mask-click="false" background-color="#ffffff" border-radius="5px 5px 5px 5px">
      <view class="hongbao-dialog">
        <view class="title">恭喜获得红包</view>
        <view class="money1">
          <image mode="widthFix" src="/static/images/hongbao.png"/>
          <text>￥{{ money }}</text>
        </view>
        <view class="money2">已自动存入余额</view>

        <view class="btn">
          <button type="primary" size="mini" @click="$refs.hongbaoRef.close(); idioms = ''; generateNextLevel()">
            下一关
          </button>
        </view>
      </view>
    </uni-popup>

    <uni-popup ref="withdrawalDialogRef" background-color="#ffffff" border-radius="5px 5px 5px 5px">
      <view class="withdrawal-dialog">
        <view class="title">提现</view>
        <input type="number" v-model="withdrawalMoney" placeholder="请输入提现金额">
        <view class="btn">
          <button @click="$refs.withdrawalDialogRef.close()">
            取消
          </button>

          <button type="primary" @click="withdrawal">
            确定
          </button>
        </view>
      </view>
    </uni-popup>

    <view class="notice-dialog" v-if="showNoticeDialog" @click="jumpUrl('/pages/noticeList/noticeList')">
      <view class="left">
        <image mode="widthFix" src="/static/images/horn.png"/>
      </view>

      <view class="right">
        <text>微信支付</text>
        <text>微信支付：您收到一笔活动奖励</text>
      </view>
    </view>
  </view>
</template>

<script>
import { mapState } from 'vuex'

function shuffleArray(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]]; // 使用解构赋值交换元素
  }
  return array;
}

export default {
  data() {
    return {
      idiomsList: shuffleArray([
        {
          idioms: '一厢情愿',
        },
        {
          idioms: '一叶知秋',
        },
        {
          idioms: '一字千金',
        },
        {
          idioms: '一手遮天',
        },
        {
          idioms: '一枕黄粱',
        },
        {
          idioms: '一见钟情',
        },
        {
          idioms: '一路顺风',
        },
        {
          idioms: '七情六欲',
        },
        {
          idioms: '七窍生烟',
        },
        {
          idioms: '七零八落',
        },
        {
          idioms: '三五成群',
        },
        {
          idioms: '三令五申',
        },
        {
          idioms: '三言两语',
        },
        {
          idioms: '三顾茅庐',
        },
        {
          idioms: '不堪入目',
        },
        {
          idioms: '东张西望',
        },
        {
          idioms: '举一反三',
        },
        {
          idioms: '乘人之危',
        },
        {
          idioms: '九死一生',
        },
        {
          idioms: '书香门第',
        },
        {
          idioms: '了如指掌',
        },
        {
          idioms: '事半功倍',
        },
        {
          idioms: '云开日出',
        },
        {
          idioms: '井底之蛙',
        },
        {
          idioms: '亡羊补牢',
        },
        {
          idioms: '亭亭玉立',
        },
        {
          idioms: '人仰马翻',
        },
        {
          idioms: '人言可畏',
        },
        {
          idioms: '仗势欺人',
        },
        {
          idioms: '以卵击石',
        },
        {
          idioms: '任重道远',
        },
        {
          idioms: '倾国倾城',
        },
        {
          idioms: '全军覆没',
        },
        {
          idioms: '兵临城下',
        },
        {
          idioms: '冰肌玉骨',
        },
        {
          idioms: '出类拔萃',
        },
        {
          idioms: '功高盖主',
        },
        {
          idioms: '劳燕分飞',
        },
        {
          idioms: '势如破竹',
        },
        {
          idioms: '千言万语',
        },
        {
          idioms: '半壁江山',
        },
        {
          idioms: '卧薪尝胆',
        },
        {
          idioms: '卷土重来',
        },
        {
          idioms: '卸磨杀驴',
        },
        {
          idioms: '口说无凭',
        },
        {
          idioms: '唇齿相依',
        },
        {
          idioms: '四脚朝天',
        },
        {
          idioms: '回头是岸',
        },
        {
          idioms: '垂头丧气',
        },
        {
          idioms: '大海捞针',
        },
        {
          idioms: '天方夜谭',
        },
        {
          idioms: '头头是道',
        },
        {
          idioms: '头破血流',
        },
        {
          idioms: '好色之徒',
        },
        {
          idioms: '妖魔鬼怪',
        },
        {
          idioms: '妙笔生花',
        },
        {
          idioms: '始作俑者',
        },
        {
          idioms: '学富五车',
        },
        {
          idioms: '岂有此理',
        },
        {
          idioms: '平分秋色',
        },
        {
          idioms: '度日如年',
        },
        {
          idioms: '异曲同工',
        },
        {
          idioms: '引火上身',
        },
        {
          idioms: '归心似箭',
        },
        {
          idioms: '微不足道',
        },
        {
          idioms: '心心相印',
        },
        {
          idioms: '恨之入骨',
        },
        {
          idioms: '息息相关',
        },
        {
          idioms: '恶贯满盈',
        },
        {
          idioms: '悬崖勒马',
        },
        {
          idioms: '想入非非',
        },
        {
          idioms: '愁眉苦脸',
        },
        {
          idioms: '打家劫舍',
        },
        {
          idioms: '投其所好',
        },
        {
          idioms: '投石问路',
        },
        {
          idioms: '拖泥带水',
        },
        {
          idioms: '按图索骥',
        },
        {
          idioms: '排山倒海',
        },
        {
          idioms: '提心吊胆',
        },
        {
          idioms: '旗开得胜',
        },
        {
          idioms: '无中生有',
        },
        {
          idioms: '日思夜想',
        },
        {
          idioms: '晴天霹雳',
        },
        {
          idioms: '暗箭难防',
        },
        {
          idioms: '本末倒置',
        },
        {
          idioms: '束之高阁',
        },
        {
          idioms: '束手就擒',
        },
        {
          idioms: '枯木逢春',
        },
        {
          idioms: '梁上君子',
        },
        {
          idioms: '气吞山河',
        },
        {
          idioms: '泾渭分明',
        },
        {
          idioms: '浑水摸鱼',
        },
        {
          idioms: '浩浩荡荡',
        },
        {
          idioms: '海纳百川',
        },
        {
          idioms: '渐入佳境',
        },
        {
          idioms: '漏网之鱼',
        },
        {
          idioms: '火上浇油',
        },
        {
          idioms: '火中取栗',
        },
        {
          idioms: '火冒三丈',
        },
        {
          idioms: '照猫画虎',
        },
        {
          idioms: '爱屋及乌',
        },
        {
          idioms: '爱憎分明',
        },
        {
          idioms: '狐假虎威',
        },
        {
          idioms: '狡兔三窟',
        },
        {
          idioms: '病从口入',
        },
        {
          idioms: '百步穿杨',
        },
        {
          idioms: '皮开肉绽',
        },
        {
          idioms: '眼高手低',
        },
        {
          idioms: '石破天惊',
        },
        {
          idioms: '秀色可餐',
        },
        {
          idioms: '积少成多',
        },
        {
          idioms: '粗茶淡饭',
        },
        {
          idioms: '绝处逢生',
        },
        {
          idioms: '罪加一等',
        },
        {
          idioms: '羊入虎口',
        },
        {
          idioms: '老马识途',
        },
        {
          idioms: '老骐伏枥',
        },
        {
          idioms: '耳目一新',
        },
        {
          idioms: '胆小如鼠',
        },
        {
          idioms: '苦中作乐',
        },
        {
          idioms: '苦口婆心',
        },
        {
          idioms: '草船借箭',
        },
        {
          idioms: '蒸蒸日上',
        },
        {
          idioms: '藕断丝连',
        },
        {
          idioms: '蚍蜉撼树',
        },
        {
          idioms: '血流成河',
        },
        {
          idioms: '行云流水',
        },
        {
          idioms: '语重心长',
        },
        {
          idioms: '走马观花',
        },
        {
          idioms: '趁火打劫',
        },
        {
          idioms: '身怀六甲',
        },
        {
          idioms: '车水马龙',
        },
        {
          idioms: '进退两难',
        },
        {
          idioms: '迫在眉睫',
        },
        {
          idioms: '遮天蔽日',
        },
        {
          idioms: '酒囊饭袋',
        },
        {
          idioms: '金貂换酒',
        },
        {
          idioms: '闲云野鹤',
        },
        {
          idioms: '闻鸡起舞',
        },
        {
          idioms: '雌雄难辨',
        },
        {
          idioms: '顺手牵羊',
        },
        {
          idioms: '风烛残年',
        },
        {
          idioms: '马踏飞燕',
        },
        {
          idioms: '鱼贯而出',
        },
        {
          idioms: '鸡毛蒜皮',
        },
        {
          idioms: '鸡飞狗跳',
        },
        {
          idioms: '鸡鸣狗盗',
        }
      ]),
      currentIdiomsIndex: 0,
      idioms: '',
      showError: false,
      focus: false,
      money: 0,
      totalMoney: Number(uni.getStorageSync('totalMoney')) || 0,
      withdrawalMoney: '',
      showNoticeDialog: false
    }
  },

  onLoad() {
    this.generatePuzzle()
  },

  computed: {
    ...mapState('app', ['noticeList'])
  },

  methods: {
    generatePuzzle() {
      if (this.currentIdiomsIndex >= this.idiomsList.length) {
        uni.showToast({
          title: '通关完成',
          icon: 'success'
        })
      }
    },

    generateNextLevel() {
      this.currentIdiomsIndex++
      this.generatePuzzle()
    },

    submit() {
      if (!this.idioms.length) {
        uni.showToast({
          title: '请输入答案',
          icon: 'none'
        })

        return
      }

      if (this.idioms !== this.idiomsList[this.currentIdiomsIndex].idioms) {
        this.idioms = ''

        setTimeout(() => {
          this.showError = true
        }, 0)
      } else {
        let moneyData = uni.getStorageSync('moneyData')[0]
        let money = Number(moneyData.minMoney) + Math.random() * (moneyData.maxMoney - moneyData.minMoney)
        this.money = Number(money.toFixed(2))
        this.totalMoney = Number((this.totalMoney + this.money).toFixed(2))
        this.$refs.hongbaoRef.open()
      }
    },

    withdrawal() {
      if (Number(this.withdrawalMoney) < 1) {
        uni.showToast({
          title: '最低提现1',
          icon: 'error'
        })

        return
      }

      if (Number(this.withdrawalMoney) > this.totalMoney) {
        uni.showToast({
          title: '余额不足',
          icon: 'error'
        })

        return
      }

      uni.showLoading({
        title: '请稍等...',
        mask: true,
      })

      setTimeout(() => {
        uni.showToast({
          title: '提现成功',
          icon: 'success'
        })

        this.$refs.withdrawalDialogRef.close()
        this.totalMoney = Number((this.totalMoney - this.withdrawalMoney).toFixed(2))
        uni.setStorageSync('totalMoney', this.totalMoney)

        setTimeout(() => {
          this.showNoticeDialog = true

          this.noticeList.push({
            time: Date.now(),
            money: Number(this.withdrawalMoney).toFixed(2),
          })

          setTimeout(() => {
            this.showNoticeDialog = false
          }, 4000)
        }, 4000)
      }, 2000)
    },

    jumpUrl(url) {
      uni.navigateTo({
        url: url
      })
    }
  },
}
</script>

<style lang="scss">
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
  background: url("/static/images/bg.png") no-repeat top left/100% 100%;
  padding: 100rpx 0 60rpx;

  .title {
    flex-shrink: 0;
    align-self: stretch;

    .options {
      padding: 0 40rpx;
      display: flex;
      align-items: center;
      justify-content: space-between;

      .left, .right {
        font-size: 32rpx;
        display: flex;
        align-items: center;
        gap: 10rpx;
        background: #ffffff;
        padding: 4rpx 16rpx;
        border-radius: 16rpx;

        &.left {
          color: #00c800;
        }

        &.right {
          color: red;
        }

        image {
          width: 60rpx;
        }
      }

      .grade {
        font-size: 36rpx;
      }
    }

    .sub-title {
      text-align: center;
      font-size: 80rpx;
      color: #D62422;
      margin-top: 20px;
      letter-spacing: 4rpx;
    }
  }

  .sudoku-board {
    flex-grow: 1;
    overflow: auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-end;

    image {
      width: 660rpx;
      margin-bottom: 30rpx;
    }

    .input-box {
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 60rpx;
      position: relative;

      text {
        font-size: 46rpx;
      }

      input {
        background: #ffffff;
        border: 2px solid #3D3832;
        border-radius: 12rpx;
        width: 360rpx;
        height: 90rpx;
        font-size: 46rpx;
        text-align: center;
      }

      .error {
        position: absolute;
        right: 100rpx;
        font-size: 46rpx;
        color: red;
      }
    }

    .submit {
      display: flex;
      align-items: center;
      justify-content: center;

      button {
        color: #FFFFFF;
        background: #32B706;
        width: 200rpx;
      }
    }
  }
}

.hongbao-dialog {
  width: 550rpx;
  padding: 40rpx 50rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .title {
    text-align: center;
    font-size: 36rpx;
  }

  .money1 {
    position: relative;

    image {
      width: 300rpx;
    }

    text {
      position: absolute;
      font-size: 40rpx;
      color: #e0f868;
      bottom: 60rpx;
      left: 0;
      right: 0;
      text-align: center;
    }
  }

  .money2 {
    font-size: 28rpx;
    margin-bottom: 10rpx;
  }

  .btn {
    align-self: stretch;
    display: flex;
    align-items: center;

    button {
      width: 70%;
    }
  }
}

.withdrawal-dialog {
  width: 550rpx;
  padding: 40rpx 50rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .title {
    text-align: center;
    font-size: 40rpx;
    font-weight: bold;
    margin-bottom: 40rpx;
  }

  input {
    height: 86rpx;
    margin-bottom: 40rpx;
    width: 100%;
    border: 1px solid #cccccc;
    border-radius: 16rpx;
    padding: 0 30rpx;
    font-size: 36rpx;
  }

  .btn {
    align-self: stretch;
    display: flex;
    align-items: center;
    justify-content: space-around;

    button {
      width: 180rpx;
      height: 70rpx;
      line-height: 70rpx;
      font-size: 30rpx;
    }
  }
}

.notice-dialog {
  display: flex;
  align-items: center;
  background: #ffffff;
  position: fixed;
  z-index: 999;
  left: 40rpx;
  right: 40rpx;
  border-radius: 8rpx;
  padding: 30rpx 30rpx;
  top: 0;
  opacity: 0;
  transition: all 0.3s ease;
  animation: 4s ease kf1;

  @keyframes kf1 {
    0% {
      top: 0;
      opacity: 0;
    }

    10% {
      top: 100rpx;
      opacity: 1;
    }

    90% {
      top: 100rpx;
      opacity: 1;
    }

    100% {
      top: 0;
      opacity: 0;
    }
  }

  .left {
    background: #00C800;
    width: 90rpx;
    height: 90rpx;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-right: 40rpx;

    image {
      width: 50rpx;
    }
  }

  .right {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 10rpx;

    text {
      &:nth-child(1) {
        font-size: 32rpx;
        font-weight: bold;
      }

      &:nth-child(2) {
        font-size: 28rpx;
      }
    }
  }
}
</style>
