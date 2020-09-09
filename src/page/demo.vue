<template>
        <div class="box" v-infinite-scroll="load" infinite-scroll-disabled="disabled" >
            <ul class="list" >
                <li v-for="(i,index) in list" class="list-item" :key="index">{{ i }}</li>
            </ul>
            <p v-if="loading" class="loading">
                <span></span>
            </p>
            <p v-if="noMore && !loading" class="noMoreText">没有更多了</p>
        </div>
</template>

<script>
//加载更多，通过总页数据来判断

import cate from '../mock/cate'

export default {
    data() {
        return {
            page: 1,//起始页数值为
            loading: true,  //初始也在加载中
            totalPages: "",//取后端返回内容的总页数
            list: [] //后端返回的数组
        };
    },
    computed: {
        //当
        noMore() {
            //当起始页数大于总页数时停止加载
            return this.page >= this.totalPages*1;
        },
        //没有loading,没有更多就停止加载
        disabled() {
            // return this.loading || this.noMore;
            return this.noMore;
        }
    },
    created() {
        this.getMessage();
    },
    methods: {
        load() {
            console.log('到底了');
            //这里要加锁
            if(this.loading){
                return;
            }
            //滑到底部时进行加载
            this.loading = true;
            this.page += 1; //页数+1
            this.getMessage(); //调用接口，此时页数+1，查询下一页数据
        },
        //数据请求
        getMessage() {
            let params = {
                pageNumber: this.page,
                pageSize: 10 //每页查询条数
            };

            const paramsData = Object.keys(params).map(key => `${encodeURIComponent(key)}=${encodeURIComponent(params[key])}`).join('&')
            fetch(`http://testapi.ydl.com/api/information/cate?${paramsData}`).then(res=>{
                return res.json();
            }).then(res=>{
                res=cate;
                const { list, totalPages }=res.data;
                this.list = this.list.concat(list); //因为每次后端返回的都是数组，所以这边把数组拼接到一起
                this.totalPages = totalPages;
                this.loading = false;
            })

        }
    }
};
</script>

<style scoped>

.box {
    width: 400px;
    height: 500px;
    overflow-y: auto;
    border: 2px solid cadetblue;
}
.list {
    padding: 0;
    font-size: 14px;
}
.list-item {
    width: 100%;
    display: block;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    list-style: none;
    padding: 0 1rem;
    box-sizing: border-box;
    height: 250px;
    line-height: 250px;
    border-bottom: 1px solid #e7e7e7;
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
