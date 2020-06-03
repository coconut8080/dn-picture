<template>
    <div>
        <scroll-view 
            @scrolltolower = "handleToLower"
            class="recommend_view"
            scroll-y 
            v-if="recommends.length>0">
            <!-- 推荐开始 -->
            <view class="recommend_warp">
                <navigator 
                class="recommend_item"
                v-for="item in recommends"
                :key="item.id"
                :url="`/pages/album/index?id=${item.target}`"
                >
                    <image mode="widthFix" :src="item.thumb"></image>
                </navigator>
            </view>
            <!-- 推荐结束 -->

            <!-- 月份开始 -->
            <view class="monthes_warp">
                <view class="monthes_title">
                    <view class="monthes_title_info">
                        <view class="monthes_info">
                            <text>{{monthes.MM}} /</text>
                            {{monthes.DD}} 月
                        </view>
                        <view class="monthes_title_text">{{ monthes.title }}</view>
                    </view>
                    <view class="monthes_title_more">更多 ></view>
                </view>
                <view class="monthes_content">
                    <view 
                        class="monthes_item"
                        v-for="(item,index) in monthes.items"
                        :key="item.id"
                    >
                    <go-detail :list="monthes.items" :index="index">
                        <image 
                            mode="aspectFill"
                            :src="item.thumb+item.rule.replace('$<Height>',360)" 
                            >
                        </image>
                    </go-detail>
                    </view>
                </view>
            </view>
            <!-- 月份结束 -->

            <!-- 热门开始 -->
            <view class="hots_warp">
                <view class="hots_title">
                    <text>热门</text>
                </view>
                <view class="hots_content">
                    <view 
                    class="hots_item"
                    v-for="(item,index) in hots"
                    :key="item.id">
                        <go-detail :list="hots" :index="index">
                            <image
                             mode="widthFix" 
                             :src="item.thumb"
                             ></image>
                        </go-detail>
                    </view>
                </view>
            </view>
            <!-- 热门结束 -->
        </scroll-view>
    </div>
</template>

<script>
import moment from "moment";
import goDetail from "@/components/goDetail";
export default {
    components:{
        goDetail
    },
    data(){
        return{
            // 推荐列表
            recommends: [],
            // 月份
            monthes:{},
            // 热门
            hots:[],
            // 请求的参数
            params:{
                // 要获取几条
                limit: 30,
                // 关键字
                order:"hot",
                // 要跳过几条
                skip:0
            },
            // 是否还有下一页
            hasMore: true
        }
    },
    mounted(){
        uni.setNavigationBarTitle({
            title: '首页'
        });
        this.getList();
    },
    methods:{
        // 获取接口的数据
        getList(){
            this.request({
            url:"http://157.122.54.189:9088/image/v3/homepage/vertical",
            data:this.params,
            })
        .then(result =>{
            // console.log(result);

            // 判断有没有下一页数据
            if(result.res.vertical.length ===0){
                this.hasMore = false;
                return;
            }

            if(this.recommends.length === 0){
                // 第一次发送的请求
                this.recommends = result.res.homepage[1].items;
                this.monthes = result.res.homepage[2];
                // 将事件戳 改为 xx号/月  moment.js
                this.monthes.MM = moment(this.monthes.stime).format('MM');
                this.monthes.DD = moment(this.monthes.stime).format('DD');
            }
            
            // 获取热门数据的列表
            // 数组拼接 es6
            this.hots = [...this.hots,...result.res.vertical];
            })
        },

        // 滚动条触底事件
        handleToLower(){
            /**
             * 1 修改参数 skip+=limit
             * 2 重新发送请求
             * 3 请求回来成功 hots 数据的叠加
             */
            if(this.hasMore){
                this.params.skip += this.params.limit;
                this.getList();
            }else{
                uni.showToast({
                    title: "没有数据了",
                    icon: "none"
                })
            }

        }
    }
}
</script>

<style lang="scss">
.recommend_view{
    // height: 屏幕的高度 - 头部标题的高度
    height: calc(100vh - 36px);
}

.recommend_warp{
    display: flex;
    flex-wrap: wrap;
    .recommend_item{
        width: 50%;
        border: 5rpx solid #fff;
    }
}
.monthes_warp {
  .monthes_title {
      display: flex;
      justify-content: space-between;
      padding: 20rpx;
    .monthes_title_info {
        color:$color;
        font-size: 30rpx;
        font-weight: 600;
        display: flex;
      .monthes_info {
        text {
            font-size: 36rpx;
        }
      }

      .monthes_title_text {
          font-size: 34rpx;
          color: #666;
          margin-left: 30rpx;
      }
    }

    .monthes_title_more {
        color:$color;
        font-size: 24rpx;

    }
  }

  .monthes_content {
        display: flex;
        flex-wrap: wrap;
      .monthes_item{  
            width: 33.33%;
            border: 5rpx solid #fff;
    }
  }
}

.hots_warp {
    
    .hots_title {
    padding: 20rpx;
    text {
        border-left: 20rpx solid $color;
        font-size: 32rpx;
        padding-left: 30rpx;
        font-size: 34rpx;
       font-weight: 600;

    }
  }

  .hots_content {
      display: flex;
      flex-wrap: wrap;
    .hots_item {
        width: 33.33%;
        border: 5rpx solid #fff;
      image {

      }
    }
  }
}
</style>