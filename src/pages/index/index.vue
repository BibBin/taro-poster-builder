<template>
  <view class="index">
    <view class="action-bar">
      <button @tap="start">生成海报</button>
      <button @tap="savePoster">下载海报</button>
    </view>
    
    <view class="preview-area">
      <image v-if="posterPath" :src="posterPath" mode="widthFix" />
      <view v-else class="text">预览区域</view>
    </view>
    <PosterBuilder 
      v-if="startDraw"
      custom-style="position: fixed; left: 200%;"
      :config="base"
      @success="drawSuccess"
      @fail="drawFail" />
  </view>
</template>

<script lang="ts">
import Taro from '@tarojs/taro'
import { defineComponent, reactive, toRefs } from 'vue';
import PosterBuilder from '../../components/PosterBuilder/index.vue';

export default defineComponent({
  name: 'Index',
  components: {
    PosterBuilder,
  },
  setup(){
    const state = reactive({
      startDraw:false,
      posterPath:''
    })
    const base = {
      width: 750,
      height: 1334,
      backgroundColor: '#232422',
      debug: false,
      blocks: [
        // 头部底色
        {
          x: 32,
          y: 80,
          width: 686,
          height: 160,
          paddingLeft: 0,
          paddingRight: 0,
          backgroundColor: '#FFFFFF',
          borderRadius: 32,
          zIndex: 10
        },
        //底部背景
        {
          x: 32,
          y: 950,
          width: 686,
          height: 302,
          paddingLeft: 0,
          paddingRight: 0,
          borderRadiusGroup: [0, 0, 16, 16],
          backgroundColor: '#FFFFFF',
          zIndex: 11
        }
      ],
      texts: [
        {
          x: 216,
          y: 108,
          text: 'BiBin',
          width: 380,
          lineNum: 2, // 最多几行
          fontSize: 36,
          fontWeight: 'bold',
          color: '#1A171B',
          zIndex: 11
        },
        {
          x: 216,
          y: 174,
          text: '为你挑选了一个好物',
          width: 380,
          fontSize: 28,
          color: '#7C7D7A',
          zIndex: 11
        },
        {
          x: 64,
          y: 994,
          text: `￥6799`,
          fontSize: 48,
          color: '#ED2D2B',
          fontWeight: 'bold',
          zIndex: 12
        },
        {
          x: 64,
          y: 1092,
          text: 'Apple iPhone 13 (A2634) 256GB 蓝色 支持移动联通电信5G 双卡双待手机',
          fontSize: 32,
          width: 380,
          color: '#282925',
          lineNum: 2, // 最多几行
          zIndex: 12
        }
      ],
      images: [
        {
          x: 64,
          y: 100,
          width: 120,
          height: 120,
          borderRadius: 60,
          url: 'https://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTJP05RJ5icJkUkBjVtSb3DHib4pGRVEqjw3qNic53kd1tJmibPpzR1etJnWJFiaIJDLK6TDzD3d5SyPsXQ/132',
          zIndex: 11
        },
        {
          x: 32,
          y: 272,
          width: 686,
          height: 686,
          url: 'https://m.360buyimg.com/mobilecms/s750x750_jfs/t1/119360/32/20820/239818/618be3c6E1dbf188d/4070880e024273bb.jpg!q80.dpg',
          borderRadiusGroup: [16, 16, 0, 0],
          zIndex: 11
        }
      ]
    };
    const start = () => {
      state.startDraw = true;
      Taro.showLoading();
    }
    const drawSuccess = (result) => {
      console.log('绘制好了', result);
      const { tempFilePath, errMsg } = result;
      if (errMsg === 'canvasToTempFilePath:ok') {
        state.posterPath = tempFilePath;
        Taro.hideLoading();
      } else {
        Taro.hideLoading();
        Taro.showToast({
          title: '失败，请稍后重试',
          icon: 'none',
          duration: 2500
        });
      }
    }; 
    const drawFail = (result) => {
      console.log('绘制失败', result);
      Taro.hideLoading();
    }
    const savePoster = () => {
      Taro.saveImageToPhotosAlbum({
      filePath: state.posterPath,
      success() {
        Taro.showToast({
          title: '已保存到相册',
          icon: 'success',
          duration: 2000
        });
      }
    });
    }
    return {
      ...toRefs(state),
      base,
      start,
      drawSuccess,
      drawFail,
      savePoster
    }
  }
});
</script>

<style>
.index {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
 
}
.action-bar{
  display: flex;
}
.preview-area{
  width: 80%;
  min-height: 800px;
  margin:20px auto;
  text-align: center;
  border:1px solid #cccccc;
}
.text{
  line-height: 800px;
}
</style>
