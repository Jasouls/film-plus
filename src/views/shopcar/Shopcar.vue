<template>
    <div class="shop">
        <Topbar>
            <template v-slot:left>
                <div class="back" @click="backpage">&lt;</div>
            </template>
            <template v-slot:center>
                <div>购物车</div>
            </template>
        </Topbar> 
        <ul class="list" v-if="datalist.length != 0">
            <li v-for="(data,index) in datalist" :key="data.filmId" class="item">
                <img :src="data.poster" alt="">
                <div class="main">
                    <h2>{{data.name}}</h2>
                    <p>单价：{{price}}</p>
                </div>
                <div class="num">
                    <button @click="addOne(index)">+</button>
                    <span>{{data.current}}</span>
                    <button @click="oddOne(index)">-</button>
                </div>
            </li>
        </ul>
        <p class="zero" v-else>购物车空空如也！</p>
        <bottombar @click.native="toPay">
            <div>总计：{{addNum}}</div>
        </bottombar>
    </div>    
</template>
<script>
import { MessageBox } from 'mint-ui'
import Topbar from '@/components/common/topbar/Topbar'
import Bottombar from '@/components/base/bottombar/Bottombar'
export default {
    data () {
        return {
            datalist:[],
            start:1,
            price:0,
            current:1
        }
    },
    computed: {
      addNum(){
          let all = 0
          for(let i = 0;i < this.datalist.length;i++){
              all += this.datalist[i].current * this.price
          }
          return this.datalist.length !== 0 ? all : 0
      }  
    },
    components: {
        Topbar,
        Bottombar
    },
    methods: {
        backpage(){
            this.$router.push("/user")
        },
        addOne(i){
            this.datalist[i].current++
        },
        oddOne(i){
            if(this.datalist[i].current === 1){
                MessageBox({
                    title: '提示',
                    message: '确认删除该订单？',
                    showCancelButton: true
                }).then(res => {
                    this.isDelete(res,i)
                })
            }else{
                this.datalist[i].current--
            }
        },
        isDelete(data,i){
            if(data === 'confirm'){
                this.datalist.splice(i,1)
                this.$store.commit("removeShop",i)
            }
        },
        toPay(){
            MessageBox({
                title: '提示',
                message: '确认前往结算吗？',
                showCancelButton: true
            })
        }
    },
    mounted () {
        this.$store.commit('changeTab',false)
        this.datalist = this.$store.state.shopcar.map(item => {
            this.$set(item, "current", 1)
            return item
        })
        if(this.datalist.length > 0){
            this.price = 20
        }
    },
    beforeDestroy () {
        this.$store.commit('changeTab',true)
    }
}
</script>
<style lang="less" scoped>
.back{
    font-size: 20px;
}
.shop{
    .zero{
       margin-top: 44px; 
       text-align: center;
       line-height: 100px;
       font-size: 24px;
       font-weight: bold;
    }
    .list{
        margin-top: 44px;
        box-sizing: border-box;
        .item{
            display: flex;
            box-sizing: border-box;
            padding: 20px 10px;
            border: 1px solid lightgray;
            justify-content: space-between;
            img{
                height: 100px;
                width: 72px;
            }
            .main{
                flex: 1;
                box-sizing: border-box;
                padding-left: 20px;
                h2{
                    text-align: left;
                    font-size: 18px;
                    font-weight: bold;
                }
                p{
                    margin-top: 60px;
                }
            }
            
            .num{
                width: 24%;
                margin-top: 40px;
            }
        }
    }
}
</style>