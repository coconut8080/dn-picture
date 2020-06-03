<template>
    <div>
        <view
        @touchstart = "handleTouchstart"
        @touchend = "handleTouchend"
        >学习触屏事件</view>
    </div>
</template>

<script>
/**
 * 1 给容器绑定两个触屏事件 touchstart 和 touchend
 * 2 用户按下屏幕事件
 *   1 记录用户按下屏幕的时间(Date.now() )和坐标 x 和 y 
 * 3 用户离开屏幕的事件
 *   1 记录用户离开屏幕的时间(Date.now() )和坐标 x 和 y 
 *   2 根据两个时间 运算 判断 用户按下屏幕时长是否合法
 *   3 根据两对坐标 判断距离是否合法 判断 滑动的方向
 */
export default {
    data() {
        return {
            // 按下的时间
            startTime:0,
            // 按下的坐标
            startX:0,
            startY:0
        }
    },
    methods: {
        handleTouchstart(e){
            console.log(e.changedTouches[0].clientX);
            console.log(e.changedTouches[0].clientY);

            this.startTime = Date.now();
            this.startX = e.changedTouches[0].clientX;
            this.startY = e.changedTouches[0].clientY;
            
            
        },
        handleTouchend(e){
            console.log(e.changedTouches[0].clientX);
            console.log(e.changedTouches[0].clientY);

            const endTime = Date.now();
            const endX = e.changedTouches[0].clientX;
            const endY = e.changedTouches[0].clientY;

            // 判断按下的时长
            if(endTime - this.startTime >2000){
                return;
            }

            // 滑动的方向
            let direction = "";

            //  先判断用户滑动的距离 是否合法 合法：判断滑动的方向  注意 距离要加上绝对值
            if(Math.abs(endX - this.startX) > 10 ){
                // 滑动方向
                direction = endX-this.startX > 0 ? "right":"left";
            }else{
                return;
            }
            // 用户做了合法的滑动操作
             console.log(direction);
        }
    },
}
</script>

<style lang="scss">
view {
  width: 100%;
  height: 500rpx;
  background-color: aqua;
}
</style>