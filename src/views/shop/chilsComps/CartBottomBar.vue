<template>
  <div class="bottom-menu">
    <CheckButton :value='isActiveAll' @checkBtnClick="checkBtnClick" class="select-all" ></CheckButton>
    <span>全选</span>
    <span class="total-price">合计: ¥{{totalPrice}}</span>
    <span class="buy-product">去计算({{checkLength}})</span>
  </div>
</template>

<script>
  import CheckButton from './CheckButton'
  import { mapGetters } from 'vuex'

 export default { 
  name: "BottomBar",
    data(){
      return{
        cartList:this.$store.state.cartList
      }
    },
    components: {
     CheckButton
    },
    computed: {
      ...mapGetters({
        // length:'cartlength'
      }),
      totalPrice(){
        return this.cartList.filter(item =>{
          return item.checked
        }).reduce((preValue,item)=>{
          return preValue + item.price * item.count
        },0)
      },
      checkLength(){
        return this.cartList.filter(item =>item.checked).length
      },
      isActiveAll(){
        // return !(this.cartList.filter(item => !item.checked).length)
        // if(this.cartList.length==0) return false
        //如果找到不选中的为true 然后再取反
        // return !(this.cartList.find(item => !item.checked))
        for(let item of this.cartList){
          if(!item.checked){
            return false
          }
        }
        return true
      },
      
      
    },
    methods: {
      checkBtnClick(){
        if(this.isActiveAll){
          this.cartList.forEach(item => item.checked=false);
        }else{
          this.cartList.forEach(item => item.checked=true);
        }
        // this.isActiveAll = !this.isActiveAll
      }
    }
  }
</script>

<style scoped>
  .bottom-menu {
    width: 100%;
    height: 44px;
    background-color: #eee;
    position: fixed;
    bottom: 49px;
    left: 0;
    box-shadow: 0 -2px 3px rgba(0, 0, 0, .2);
    font-size: 14px;
    color: #888;
    line-height: 44px;
    padding-left: 35px;
    box-sizing: border-box;
  }

  .bottom-menu .select-all {
    position: absolute;
    line-height: 0;
    left: 12px;
    top: 13px;
  }

  .bottom-menu .total-price {
    margin-left: 15px;
    font-size: 16px;
    color: #666;
  }

  .bottom-menu .buy-product {
    background-color: orangered;
    color: #fff;
    width: 100px;
    height: 44px;
    text-align: center;
    line-height: 44px;
    float: right;
  }
</style>
