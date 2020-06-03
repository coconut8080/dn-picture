<template>
  <div>
    <scroll-view 
     class="video_view"
     scroll-y
     enable-flex
     @scrolltolower="handleScrolltolower"
    >
      <view 
      class="video_item"
      v-for="item in videowp"
      :key="item.id"
      @click="handleGovideo(item)"
      >
        <image mode="widthFix" :src="item.img"></image>
      </view>
    </scroll-view >
  </div>
</template>

<script>
export default {
  data() {
    return {
      videowp:[],
      hasMore:true
    }
  },
  props: {
    urlobj: Object
  },
  watch: {
    urlobj() {
      //  console.log("变化了");
      // console.log(this.urlobj);
      this.videowp = [];
      this.getList();
    }
  },
  mounted() {
    // console.log(this.urlobj);
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: this.urlobj.url,
        data: this.urlobj.params
      }).then(result => {
        // console.log(result);
        if(result.res.videowp.length === 0){
          this.hasMore = false;
          uni.showToast({
            title:"没有更多的数据了",
            icon:"none"
          });
          return;
        }
        this.videowp = [...this.videowp,...result.res.videowp];
      });
    },
    // 分页事件
    handleScrolltolower(){
      if(this.hasMore){
        this.urlobj.params.skip += this.urlobj.params.limit;
        this.getList();
      }else{
        uni.showToast({
          title:"没有更多的数据了",
          icon:"none"
        })
      }
    },
    // 图片点击事件
    handleGovideo(item){
      // 将数据存到全局共享中
      getApp().globalData.video = item;
      uni.navigateTo({
        url:"/pages/videoPlay/index"
      })
    }
  }
};
</script>

<style lang="scss" scoped>
.video_view{
  display: flex;
  flex-wrap: wrap;
  height: calc(100vh - 36px);
  .video_item{
    width: 33.33%;
    border: 5rpx solid #fff;
    image{

    }
  }
}
</style>