<template>
    <infiniteScroll
        :height="height"
        :loading="loading"
        :noMore="noMore"
        @load="load"
    >
        <ul class="list" >
            <li v-for="(i,index) in list" class="list-item" :key="index">{{ i }}</li>
        </ul>
    </infiniteScroll>
</template>
<script>

//内容组件
//加载更多，通过总页数据来判断

import infiniteScroll from '../components/demo_loadmore_layout'
import cate from '../mock/cate'

export default {
    components:{
        infiniteScroll
    },
    data() {
        return {
            page: 1,//起始页数值为
            totalPages: "",//取后端返回内容的总页数
            list: [], //后端返回的数组
            loading:true,
            height:'648px'
        };
    },
    computed: {
        //当
        noMore() {
            //当起始页数大于总页数时停止加载
            return this.page >= this.totalPages*1;
        }
    },
    created() {
        this.getMessage();
    },
    methods: {
        load() {
            console.log('加载更多2');
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

.list-item {
    width: 100%;
    padding: 0 10px;
    line-height: 250px;
    border-bottom: 1px solid #e7e7e7;
    box-sizing: border-box;
}
</style>
