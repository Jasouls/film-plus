<template>
    <div class="city">
        <Topbar>
            <template v-slot:left>
                <div class="back" @click="backone">
                    &lt;
                </div>
            </template>
            <template v-slot:center>
                <div>请选择城市</div>
            </template>
            <template v-slot:right>
                <div>
                    <i class="iconfont icon-search"></i>
                </div>
            </template>
        </Topbar>
        <div class="list" ref="setheight">
            <mt-index-list>
                <mt-index-section :index="data.index" v-for="data in citylist" :key="data.index">
                    <div v-for="item in data.list" :key="item.cityId" @click="handleClick(item.cityId)">
                        <mt-cell :title="item.name"></mt-cell>
                    </div>
                </mt-index-section>
            </mt-index-list>
        </div>
    </div>
</template>
<script>
import {request6} from '@/network/request'
import Topbar from '@/components/common/topbar/Topbar'
export default {
    data () {
        return {
            citylist:[]    
        }
    },
    components: {
        Topbar  
    },
    mounted () {
        this.$refs.setheight.style.height = document.documentElement.clientHeight - 44 + 'px'
        request6({
            url:'/gateway?k=8585724'
        }).then(res => {
            this.citylist = this.selectCity(res)
        })
    },
    methods: {
        //将请求到的城市数据进行二次处理
        selectCity(cities){
            let azArr = []
            //将65-90的数字遍历，转换成字母后放入azArr
            for(let i = 65;i < 91;i++){
                azArr.push(String.fromCharCode(i))
            }
            let newArr = []
            //过滤城市数据，将拼音首字母与azArr的每一项进行匹配，放入新数组返回
            for(let j = 0;j < azArr.length;j++){
                let arr = cities.filter(item => item.pinyin.substring(0,1)===azArr[j].toLowerCase())
                if(arr.length > 0){
                    newArr.push({
                        index:azArr[j],
                        list:arr
                    })
                }
            }
            return newArr
        },
        backone(){
            //返回上一页
            history.back()
        },
        handleClick(id){
            this.$store.commit("changeCity",id)
            this.$router.push(`/cinema`)
        }
    },
    beforeCreate () {
        this.$store.commit("changeTab",false)
    },
    beforeDestroy () {
        this.$store.commit("changeTab",true)
    }
}
</script>
<style lang="less" scoped>
    .back{
        font-size: 24px;
    }
    i{
        font-size: 24px;
    }
    .list{
        position: relative;
        overflow: hidden;
        margin-top: 44px;
    }
</style>