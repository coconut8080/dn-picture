<template>
    <div>
        <view class="video_play">
            <image :src="videoobj.img"></image>
            <!-- 工具栏 开始 -->
            <view class="video_tool">
                <view
                 @click="handleMuted" 
                 :class="['iconfont',muted?'iconjingyin':'iconshengyin']"
                 >
                 </view>
                <view class="iconfont iconzhuanfa">
                    <button open-type="share"></button>
                </view>
            </view>
            <!-- 工具栏 结束 -->

            <!-- 视频 开始 -->
            <view class="video_wrap">
                <video :muted="muted" :src="videoobj.video" objectFit="fill"></video>
            </view>
            <!-- 视频 结束 -->

            <!-- 下载 开始 -->
            <view class="download">
                <view @click="handleDownload" class="download_btn">
                    下载高清
                </view>
            </view>
            <!-- 下载 结束 -->
        </view>
    </div>
</template>

<script>
export default {
    data() {
        return {
            videoobj: {},
            // 是否静音
            muted: false
        }
    },
    onLoad(){
        // console.log(getApp().globalData);
        this.videoobj = getApp().globalData.video;
    },
    methods: {
        handleMuted(){
            this.muted = !this.muted;
        },
        async handleDownload(){
            await uni.showLoading({ title:"下载中" })

            // 1 将远程文件 下载到小程序内存中
            const { tempFilePath } = (
                await uni.downloadFile({url: this.videoobj.video })
                )[1];
            // 2 将内存中的文件 下载到本地上
            await uni.saveVideoToPhotosAlbum({
                filePath: tempFilePath
            });
            uni.hideLoading();

            await uni.showToast({ title:"下载成功" })
        }
    },
}
</script>

<style lang="scss" scoped>
.video_play {
    position: relative;
  image {
      position: absolute;
      width: 100vw;
      height: 100vh;
      z-index: -1;
    //   css3 滤镜
     filter: blur(20px);
  }

  .video_tool {
      display: flex;
      justify-content: flex-end;
      height: 80rpx;
    .iconfont {
        margin-right: 20rpx;
        width: 80rpx;
        color: #fff;
        border-radius: 50%;
        background: rgba(0,0,0,0.2);
        font-size: 50rpx;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .iconzhuanfa{
        position: relative;
        button{
            width: 100%;
            height: 100%;
            position: absolute;
            opacity: 0;
        }
    }
  }

  .video_wrap {
      display: flex;
      justify-content: center;
    video {
      width: 360rpx;
      height: 600rpx;

    }
  }

  .download {
      display: flex;
      justify-content: center;
      margin-top: 30rpx;
    .download_btn {
        width: 360rpx;
        height: 80rpx;
        border-radius: 40rpx;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #fff;
        background-color: rgba(0,0,0,0.6);
    }
  }
}
</style>