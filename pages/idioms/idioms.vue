<template>
  <view class="container">
    <view class="title">
      <view class="grade">
        第{{ grade }}关
      </view>

      <view class="options">
        <view class="left" @click="$refs.withdrawalDialogRef.open()">
          <image mode="widthFix" src="/static/images/weixin_pay_icon.png"/>
          <text>提现</text>
        </view>

        <view class="right" @click="toggleRain">
          <image mode="widthFix" src="/static/images/hongbao.png"/>
          <text>{{ totalMoney }}</text>
        </view>
      </view>

      <view class="sub-title">看图猜成语拿红包</view>
    </view>

    <view class="sudoku-board">
      <image mode="widthFix" :src="idiomsList[currentIdiomsIndex].pic"/>

      <view class="input-box">
        <text>答案：</text>
        <input v-model="idioms" @focus="showError = false; focus = true" :focus="focus"/>
        <view class="error" v-show="showError" @click="showError = false; focus = true">错误！</view>
      </view>

      <view class="submit">
        <button @click="submit" style="margin-right: 20rpx">确认</button>
        <button @click="idioms = ''; generateNextLevel()">下一关</button>
      </view>
    </view>

    <div class="packet-container" v-if="isRaining">
      <!-- 红包容器 -->
      <div class="rain-container" @click="toggleRain">
        <div
            v-for="(redpacket, index) in redpackets"
            :key="redpacket.id"
            class="redpacket"
            @animationend="animationend(index)"
            :style="{
          left: redpacket.left + 'rpx',
          animationDuration: redpacket.duration + 's'
        }"
            @click="openRedpacket(index)">
          <image style="width: 80rpx" mode="widthFix" src="/static/images/hongbao.png"/>
        </div>
      </div>
    </div>

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
    [array[i], array[j]] = [array[j], array[i]] // 使用解构赋值交换元素
  }
  return array
}

export default {
  data() {
    return {
      idiomsList: shuffleArray([
        {
          idioms: '一厢情愿',
          id: 'yxqy',
          pic: '/static/images/idiomsPics/yxqy.jpg'
        },
        {
          idioms: '一叶知秋',
          id: 'yyzq',
          pic: '/static/images/idiomsPics/yyzq.jpg'
        },
        {
          idioms: '一字千金',
          id: 'yzqj',
          pic: '/static/images/idiomsPics/yzqj.jpg'
        },
        {
          idioms: '一手遮天',
          id: 'yszt',
          pic: '/static/images/idiomsPics/yszt.jpg'
        },
        {
          idioms: '一枕黄粱',
          id: 'yzhl',
          pic: '/static/images/idiomsPics/yzhl.jpg'
        },
        {
          idioms: '一见钟情',
          id: 'yjzq',
          pic: '/static/images/idiomsPics/yjzq.jpg'
        },
        {
          idioms: '一若千金',
          id: 'ynqj',
          pic: '/static/images/idiomsPics/ynqj.jpg'
        },
        {
          idioms: '一路顺风',
          id: 'ylsf',
          pic: '/static/images/idiomsPics/ylsf.jpg'
        },
        {
          idioms: '七情六欲',
          id: 'qqly',
          pic: '/static/images/idiomsPics/qqly.jpg'
        },
        {
          idioms: '七窍生烟',
          id: 'qqsy',
          pic: '/static/images/idiomsPics/qqsy.jpg'
        },
        {
          idioms: '七零八落',
          id: 'qlbl',
          pic: '/static/images/idiomsPics/qlbl.jpg'
        },
        {
          idioms: '三五成群',
          id: 'swcq',
          pic: '/static/images/idiomsPics/swcq.jpg'
        },
        {
          idioms: '三令五申',
          id: 'slws',
          pic: '/static/images/idiomsPics/slws.jpg'
        },
        {
          idioms: '三言两语',
          id: 'syly',
          pic: '/static/images/idiomsPics/syly.jpg'
        },
        {
          idioms: '三顾茅庐',
          id: 'sgml',
          pic: '/static/images/idiomsPics/sgml.jpg'
        },
        {
          idioms: '不堪入目',
          id: 'bkrm',
          pic: '/static/images/idiomsPics/bkrm.jpg'
        },
        {
          idioms: '东张西望',
          id: 'dzxw',
          pic: '/static/images/idiomsPics/dzxw.jpg'
        },
        {
          idioms: '举一反三',
          id: 'jyfs',
          pic: '/static/images/idiomsPics/jyfs.jpg'
        },
        {
          idioms: '乘人之危',
          id: 'crzw',
          pic: '/static/images/idiomsPics/crzw.jpg'
        },
        {
          idioms: '九死一生',
          id: 'jsys',
          pic: '/static/images/idiomsPics/jsys.jpg'
        },
        {
          idioms: '书香门第',
          id: 'sxmd',
          pic: '/static/images/idiomsPics/sxmd.jpg'
        },
        {
          idioms: '了如指掌',
          id: 'lrzz',
          pic: '/static/images/idiomsPics/lrzz.jpg'
        },
        {
          idioms: '事半功倍',
          id: 'sbgb',
          pic: '/static/images/idiomsPics/sbgb.jpg'
        },
        {
          idioms: '云开日出',
          id: 'ykrc',
          pic: '/static/images/idiomsPics/ykrc.jpg'
        },
        {
          idioms: '井底之蛙',
          id: 'jdzw',
          pic: '/static/images/idiomsPics/jdzw.jpg'
        },
        {
          idioms: '亡羊补牢',
          id: 'wybl',
          pic: '/static/images/idiomsPics/wybl.jpg'
        },
        {
          idioms: '亭亭玉立',
          id: 'ttyl',
          pic: '/static/images/idiomsPics/ttyl.jpg'
        },
        {
          idioms: '人仰马翻',
          id: 'rymf',
          pic: '/static/images/idiomsPics/rymf.jpg'
        },
        {
          idioms: '人言可畏',
          id: 'rykw',
          pic: '/static/images/idiomsPics/rykw.jpg'
        },
        {
          idioms: '仗势欺人',
          id: 'zsqr',
          pic: '/static/images/idiomsPics/zsqr.jpg'
        },
        {
          idioms: '以卵击石',
          id: 'yljs',
          pic: '/static/images/idiomsPics/yljs.jpg'
        },
        {
          idioms: '任重道远',
          id: 'rzdy',
          pic: '/static/images/idiomsPics/rzdy.jpg'
        },
        {
          idioms: '倾国倾城',
          id: 'qgqc',
          pic: '/static/images/idiomsPics/qgqc.jpg'
        },
        {
          idioms: '全军覆没',
          id: 'qjfm',
          pic: '/static/images/idiomsPics/qjfm.jpg'
        },
        {
          idioms: '兵临城下',
          id: 'blcx',
          pic: '/static/images/idiomsPics/blcx.jpg'
        },
        {
          idioms: '冰肌玉骨',
          id: 'bjyg',
          pic: '/static/images/idiomsPics/bjyg.jpg'
        },
        {
          idioms: '出类拔萃',
          id: 'clbc',
          pic: '/static/images/idiomsPics/clbc.jpg'
        },
        {
          idioms: '功高盖主',
          id: 'gggz',
          pic: '/static/images/idiomsPics/gggz.jpg'
        },
        {
          idioms: '劳燕分飞',
          id: 'lyff',
          pic: '/static/images/idiomsPics/lyff.jpg'
        },
        {
          idioms: '势如破竹',
          id: 'srpz',
          pic: '/static/images/idiomsPics/srpz.jpg'
        },
        {
          idioms: '千言万语',
          id: 'qywy',
          pic: '/static/images/idiomsPics/qywy.jpg'
        },
        {
          idioms: '半壁江山',
          id: 'bbjs',
          pic: '/static/images/idiomsPics/bbjs.jpg'
        },
        {
          idioms: '卧薪尝胆',
          id: 'wxcd',
          pic: '/static/images/idiomsPics/wxcd.jpg'
        },
        {
          idioms: '卷土重来',
          id: 'jtcl',
          pic: '/static/images/idiomsPics/jtcl.jpg'
        },
        {
          idioms: '卸磨杀驴',
          id: 'xmsl',
          pic: '/static/images/idiomsPics/xmsl.jpg'
        },
        {
          idioms: '口说无凭',
          id: 'kswp',
          pic: '/static/images/idiomsPics/kswp.jpg'
        },
        {
          idioms: '唇齿相依',
          id: 'ccxy',
          pic: '/static/images/idiomsPics/ccxy.jpg'
        },
        {
          idioms: '四脚朝天',
          id: 'sjct',
          pic: '/static/images/idiomsPics/sjct.jpg'
        },
        {
          idioms: '回头是岸',
          id: 'htsa',
          pic: '/static/images/idiomsPics/htsa.jpg'
        },
        {
          idioms: '垂头丧气',
          id: 'ctsq',
          pic: '/static/images/idiomsPics/ctsq.jpg'
        },
        {
          idioms: '大海捞针',
          id: 'dhlz',
          pic: '/static/images/idiomsPics/dhlz.jpg'
        },
        {
          idioms: '天方夜谭',
          id: 'tfyt',
          pic: '/static/images/idiomsPics/tfyt.jpg'
        },
        {
          idioms: '头头是道',
          id: 'ttsd',
          pic: '/static/images/idiomsPics/ttsd.jpg'
        },
        {
          idioms: '头破血流',
          id: 'tpxl',
          pic: '/static/images/idiomsPics/tpxl.jpg'
        },
        {
          idioms: '好色之徒',
          id: 'hszt',
          pic: '/static/images/idiomsPics/hszt.jpg'
        },
        {
          idioms: '妖魔鬼怪',
          id: 'ymgg',
          pic: '/static/images/idiomsPics/ymgg.jpg'
        },
        {
          idioms: '妙笔生花',
          id: 'mbsh',
          pic: '/static/images/idiomsPics/mbsh.jpg'
        },
        {
          idioms: '始作俑者',
          id: 'szyz',
          pic: '/static/images/idiomsPics/szyz.jpg'
        },
        {
          idioms: '学富五车',
          id: 'xfwc',
          pic: '/static/images/idiomsPics/xfwc.jpg'
        },
        {
          idioms: '岂有此理',
          id: 'qycl',
          pic: '/static/images/idiomsPics/qycl.jpg'
        },
        {
          idioms: '平分秋色',
          id: 'pfqs',
          pic: '/static/images/idiomsPics/pfqs.jpg'
        },
        {
          idioms: '度日如年',
          id: 'crrn',
          pic: '/static/images/idiomsPics/crrn.jpg'
        },
        {
          idioms: '异曲同工',
          id: 'yqtg',
          pic: '/static/images/idiomsPics/yqtg.jpg'
        },
        {
          idioms: '引火上身',
          id: 'yhss',
          pic: '/static/images/idiomsPics/yhss.jpg'
        },
        {
          idioms: '归心似箭',
          id: 'gxsj',
          pic: '/static/images/idiomsPics/gxsj.jpg'
        },
        {
          idioms: '微不足道',
          id: 'wbzd',
          pic: '/static/images/idiomsPics/wbzd.jpg'
        },
        {
          idioms: '心心相印',
          id: 'xxxy',
          pic: '/static/images/idiomsPics/xxxy.jpg'
        },
        {
          idioms: '恨之入骨',
          id: 'hzrg',
          pic: '/static/images/idiomsPics/hzrg.jpg'
        },
        {
          idioms: '息息相关',
          id: 'xxxg',
          pic: '/static/images/idiomsPics/xxxg.jpg'
        },
        {
          idioms: '恶贯满盈',
          id: 'wymg',
          pic: '/static/images/idiomsPics/wymg.jpg'
        },
        {
          idioms: '悬崖勒马',
          id: 'xylm',
          pic: '/static/images/idiomsPics/xylm.jpg'
        },
        {
          idioms: '想入非非',
          id: 'xrff',
          pic: '/static/images/idiomsPics/xrff.jpg'
        },
        {
          idioms: '愁眉苦脸',
          id: 'cmkl',
          pic: '/static/images/idiomsPics/cmkl.jpg'
        },
        {
          idioms: '打家劫舍',
          id: 'djqs',
          pic: '/static/images/idiomsPics/djqs.jpg'
        },
        {
          idioms: '投其所好',
          id: 'tqsh',
          pic: '/static/images/idiomsPics/tqsh.jpg'
        },
        {
          idioms: '投石问路',
          id: 'tswl',
          pic: '/static/images/idiomsPics/tswl.jpg'
        },
        {
          idioms: '拖泥带水',
          id: 'tnds',
          pic: '/static/images/idiomsPics/tnds.jpg'
        },
        {
          idioms: '按图索骥',
          id: 'atsj',
          pic: '/static/images/idiomsPics/atsj.jpg'
        },
        {
          idioms: '排山倒海',
          id: 'psdh',
          pic: '/static/images/idiomsPics/psdh.jpg'
        },
        {
          idioms: '提心吊胆',
          id: 'txdd',
          pic: '/static/images/idiomsPics/txdd.jpg'
        },
        {
          idioms: '旗开得胜',
          id: 'qkds',
          pic: '/static/images/idiomsPics/qkds.jpg'
        },
        {
          idioms: '无中生有',
          id: 'wzsy',
          pic: '/static/images/idiomsPics/wzsy.jpg'
        },
        {
          idioms: '日思夜想',
          id: 'ysyx',
          pic: '/static/images/idiomsPics/ysyx.jpg'
        },
        {
          idioms: '晴天霹雳',
          id: 'qtpl',
          pic: '/static/images/idiomsPics/qtpl.jpg'
        },
        {
          idioms: '暗箭难防',
          id: 'ajnf',
          pic: '/static/images/idiomsPics/ajnf.jpg'
        },
        {
          idioms: '本末倒置',
          id: 'bmdz',
          pic: '/static/images/idiomsPics/bmdz.jpg'
        },
        {
          idioms: '束之高阁',
          id: 'szgg',
          pic: '/static/images/idiomsPics/szgg.jpg'
        },
        {
          idioms: '束手就擒',
          id: 'ssjq',
          pic: '/static/images/idiomsPics/ssjq.jpg'
        },
        {
          idioms: '枯木逢春',
          id: 'kmfc',
          pic: '/static/images/idiomsPics/kmfc.jpg'
        },
        {
          idioms: '梁上君子',
          id: 'lsjz',
          pic: '/static/images/idiomsPics/lsjz.jpg'
        },
        {
          idioms: '气吞山河',
          id: 'qtsh',
          pic: '/static/images/idiomsPics/qtsh.jpg'
        },
        {
          idioms: '泾渭分明',
          id: 'jwfm',
          pic: '/static/images/idiomsPics/jwfm.jpg'
        },
        {
          idioms: '浑水摸鱼',
          id: 'hsmy',
          pic: '/static/images/idiomsPics/hsmy.jpg'
        },
        {
          idioms: '浩浩荡荡',
          id: 'hhdd',
          pic: '/static/images/idiomsPics/hhdd.jpg'
        },
        {
          idioms: '海纳百川',
          id: 'hnbc',
          pic: '/static/images/idiomsPics/hnbc.jpg'
        },
        {
          idioms: '渐入佳境',
          id: 'jrjj',
          pic: '/static/images/idiomsPics/jrjj.jpg'
        },
        {
          idioms: '漏网之鱼',
          id: 'lwzy',
          pic: '/static/images/idiomsPics/lwzy.jpg'
        },
        {
          idioms: '火上浇油',
          id: 'hjjy',
          pic: '/static/images/idiomsPics/hjjy.jpg'
        },
        {
          idioms: '火中取栗',
          id: 'hzql',
          pic: '/static/images/idiomsPics/hzql.jpg'
        },
        {
          idioms: '火冒三丈',
          id: 'hmsz',
          pic: '/static/images/idiomsPics/hmsz.jpg'
        },
        {
          idioms: '照猫画虎',
          id: 'zmhh',
          pic: '/static/images/idiomsPics/zmhh.jpg'
        },
        {
          idioms: '爱屋及乌',
          id: 'awjw',
          pic: '/static/images/idiomsPics/awjw.jpg'
        },
        {
          idioms: '爱憎分明',
          id: 'azfm',
          pic: '/static/images/idiomsPics/azfm.jpg'
        },
        {
          idioms: '狐假虎威',
          id: 'hjhw',
          pic: '/static/images/idiomsPics/hjhw.jpg'
        },
        {
          idioms: '狡兔三窟',
          id: 'jtsk',
          pic: '/static/images/idiomsPics/jtsk.jpg'
        },
        {
          idioms: '病从口入',
          id: 'bckr',
          pic: '/static/images/idiomsPics/bckr.jpg'
        },
        {
          idioms: '百步穿杨',
          id: 'bbcy',
          pic: '/static/images/idiomsPics/bbcy.jpg'
        },
        {
          idioms: '皮开肉绽',
          id: 'pkrz',
          pic: '/static/images/idiomsPics/pkrz.jpg'
        },
        {
          idioms: '眼高手低',
          id: 'ygsd',
          pic: '/static/images/idiomsPics/ygsd.jpg'
        },
        {
          idioms: '石破天惊',
          id: 'sptj',
          pic: '/static/images/idiomsPics/sptj.jpg'
        },
        {
          idioms: '秀色可餐',
          id: 'xskc',
          pic: '/static/images/idiomsPics/xskc.jpg'
        },
        {
          idioms: '积少成多',
          id: 'jscd',
          pic: '/static/images/idiomsPics/jscd.jpg'
        },
        {
          idioms: '粗茶淡饭',
          id: 'ccdf',
          pic: '/static/images/idiomsPics/ccdf.jpg'
        },
        {
          idioms: '绝处逢生',
          id: 'jcfs',
          pic: '/static/images/idiomsPics/jcfs.jpg'
        },
        {
          idioms: '罪加一等',
          id: 'zjyd',
          pic: '/static/images/idiomsPics/zjyd.jpg'
        },
        {
          idioms: '羊入虎口',
          id: 'yrhk',
          pic: '/static/images/idiomsPics/yrhk.jpg'
        },
        {
          idioms: '老马识途',
          id: 'lmst',
          pic: '/static/images/idiomsPics/lmst.jpg'
        },
        {
          idioms: '老骐伏枥',
          id: 'ljfl',
          pic: '/static/images/idiomsPics/ljfl.jpg'
        },
        {
          idioms: '耳目一新',
          id: 'emyx',
          pic: '/static/images/idiomsPics/emyx.jpg'
        },
        {
          idioms: '胆小如鼠',
          id: 'dxrs',
          pic: '/static/images/idiomsPics/dxrs.jpg'
        },
        {
          idioms: '苦中作乐',
          id: 'kzzl',
          pic: '/static/images/idiomsPics/kzzl.jpg'
        },
        {
          idioms: '苦口婆心',
          id: 'kkpx',
          pic: '/static/images/idiomsPics/kkpx.jpg'
        },
        {
          idioms: '草船借箭',
          id: 'ccjj',
          pic: '/static/images/idiomsPics/ccjj.jpg'
        },
        {
          idioms: '蒸蒸日上',
          id: 'zzrs',
          pic: '/static/images/idiomsPics/zzrs.jpg'
        },
        {
          idioms: '藕断丝连',
          id: 'odsl',
          pic: '/static/images/idiomsPics/odsl.jpg'
        },
        {
          idioms: '蚍蜉撼树',
          id: 'pfhs',
          pic: '/static/images/idiomsPics/pfhs.jpg'
        },
        {
          idioms: '血流成河',
          id: 'xlch',
          pic: '/static/images/idiomsPics/xlch.jpg'
        },
        {
          idioms: '行云流水',
          id: 'xyls',
          pic: '/static/images/idiomsPics/xyls.jpg'
        },
        {
          idioms: '语重心长',
          id: 'yzxc',
          pic: '/static/images/idiomsPics/yzxc.jpg'
        },
        {
          idioms: '走马观花',
          id: 'zmgh',
          pic: '/static/images/idiomsPics/zmgh.jpg'
        },
        {
          idioms: '趁火打劫',
          id: 'chdj',
          pic: '/static/images/idiomsPics/chdj.jpg'
        },
        {
          idioms: '身怀六甲',
          id: 'shlj',
          pic: '/static/images/idiomsPics/shlj.jpg'
        },
        {
          idioms: '车水马龙',
          id: 'csml',
          pic: '/static/images/idiomsPics/csml.jpg'
        },
        {
          idioms: '进退两难',
          id: 'jtln',
          pic: '/static/images/idiomsPics/jtln.jpg'
        },
        {
          idioms: '迫在眉睫',
          id: 'pzmj',
          pic: '/static/images/idiomsPics/pzmj.jpg'
        },
        {
          idioms: '遮天蔽日',
          id: 'ztbr',
          pic: '/static/images/idiomsPics/ztbr.jpg'
        },
        {
          idioms: '酒囊饭袋',
          id: 'jnfd',
          pic: '/static/images/idiomsPics/jnfd.jpg'
        },
        {
          idioms: '金貂换酒',
          id: 'jdhj',
          pic: '/static/images/idiomsPics/jdhj.jpg'
        },
        {
          idioms: '闲云野鹤',
          id: 'xyyh',
          pic: '/static/images/idiomsPics/xyyh.jpg'
        },
        {
          idioms: '闻鸡起舞',
          id: 'wjqw',
          pic: '/static/images/idiomsPics/wjqw.jpg'
        },
        {
          idioms: '雌雄难辨',
          id: 'cxnb',
          pic: '/static/images/idiomsPics/cxnb.jpg'
        },
        {
          idioms: '顺手牵羊',
          id: 'ssqy',
          pic: '/static/images/idiomsPics/ssqy.jpg'
        },
        {
          idioms: '风烛残年',
          id: 'fzcn',
          pic: '/static/images/idiomsPics/fzcn.jpg'
        },
        {
          idioms: '马踏飞燕',
          id: 'mtfy',
          pic: '/static/images/idiomsPics/mtfy.jpg'
        },
        {
          idioms: '鱼贯而出',
          id: 'ygec',
          pic: '/static/images/idiomsPics/ygec.jpg'
        },
        {
          idioms: '鸡毛蒜皮',
          id: 'jmsp',
          pic: '/static/images/idiomsPics/jmsp.jpg'
        },
        {
          idioms: '鸡飞狗跳',
          id: 'jfgt',
          pic: '/static/images/idiomsPics/jfgt.jpg'
        },
        {
          idioms: '鸡鸣狗盗',
          id: 'jmgd',
          pic: '/static/images/idiomsPics/jmgd.jpg'
        }
      ]),
      currentIdiomsIndex: 0,
      grade: 1,
      idioms: '',
      showError: false,
      focus: false,
      money: 0,
      totalMoney: Number(uni.getStorageSync('totalMoney')) || 0,
      withdrawalMoney: '',
      showNoticeDialog: false,
      isRaining: false,
      redpackets: [],
      timer: null,
      maxCount: 3 // 同时存在的最大红包数
    }
  },

  onLoad() {
    setTimeout(() => {
      this.toggleRain()
    }, 100)
  },

  computed: {
    ...mapState('app', ['noticeList'])
  },

  beforeDestroy() {
    clearInterval(this.timer)
  },

  methods: {
    generateNextLevel() {
      this.grade++
      if (this.currentIdiomsIndex >= (this.idiomsList.length - 1)) {
        this.currentIdiomsIndex = 0
      } else {
        this.currentIdiomsIndex++
      }
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
        mask: true
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
            money: Number(this.withdrawalMoney).toFixed(2)
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
    },

    toggleRain() {
      this.isRaining = !this.isRaining
      if (this.isRaining) {
        this.startRain()
      } else {
        this.stopRain()
      }
    },

    startRain() {
      this.createRedpacket()

      this.timer = setInterval(() => {
        if (this.redpackets.length >= this.maxCount) return
        this.createRedpacket()
      }, 2100)
    },

    stopRain() {
      clearInterval(this.timer)
      this.redpackets = []
    },

    createRedpacket() {
      let duration = Math.random() * 2 + 6 // 下落时间6-8秒
      let id = Date.now()

      const newPacket = {
        left: (750 - (this.redpackets.length + 1) * 150),
        duration: duration,
        id: id
      }
      this.redpackets.push(newPacket)
    },

    openRedpacket(index) {
      console.log('红包被点击', this.redpackets[index].id)
      // this.redpackets.splice(index, 1)
      // 这里可以添加打开红包的逻辑
    },

    animationend(index) {
      // console.log('index', index)
      // this.redpackets.splice(index, 1)
    }
  }
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
    margin-bottom: 30rpx;

    .grade {
      text-align: center;
      margin-bottom: 20rpx;
    }

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
    }

    .sub-title {
      text-align: center;
      font-size: 80rpx;
      color: #d62422;
      margin-top: 20px;
      letter-spacing: 4rpx;
    }
  }

  .sudoku-board {
    // flex-grow: 1;
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
        border: 2px solid #3d3832;
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
        color: #ffffff;
        background: #32b706;
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
    background: #00c800;
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

.packet-container {
  background: #00000030;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
  z-index: 999;
  overflow: hidden;

  .switch-btn {
    position: fixed;
    z-index: 100;
    padding: 10px 20px;
    background: #cc0000;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .rain-container {
    position: relative;
    height: 100%;
  }

  .redpacket {
    position: absolute;
    top: -50px;
    font-size: 50px;
    cursor: pointer;
    animation: drop linear infinite;
    user-select: none;
  }

  @keyframes drop {
    from {
      transform: translateY(-50px);
    }
    to {
      transform: translateY(105vh);
    }
  }
}
</style>
