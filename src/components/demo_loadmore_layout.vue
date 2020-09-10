<template>
    <div class="infinite" v-infinite-scroll="loadMore" infinite-scroll-disabled="noMore" :style="{height: height}">
        <slot></slot>
        <p v-if="loading" class="loading"><span></span></p>
        <p v-if="noMore && !loading" class="noMoreText">没有更多了</p>
    </div>
</template>

<script>


export default {
    name:'InfiniteScoll',
    props:{
        //容器高度
        height: {
            type: String,
            default: '100%'
        },
        //加载中
        loading: {
            type: Boolean,
            default: false
        },
        //无更多
        noMore: {
            type: Boolean,
            default: false
        }
    },
    methods: {
        loadMore() {
          this.$emit('load')
        },
    }
};
</script>

<style scoped>

.infinite{
    overflow-y: auto;
}

.loading{
    margin: 10px 0;
    text-align: center;
}
.loading span {
    display: inline-block;
    width: 20px;
    height: 20px;
    border: 2px solid #409eff;
    border-left: transparent;
    animation: zhuan 0.5s linear infinite;
    border-radius: 50%;
}
.noMoreText{
    margin:10px 0;
    font-size:13px;
    color:#ccc;
    text-align: center;
}
@keyframes zhuan {
    0% {
        transform: rotate(0);
    }
    100% {
        transform: rotate(360deg);
    }
}
</style>
