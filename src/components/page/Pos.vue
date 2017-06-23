<template>
  <div class="pos">
  <el-row>
    <el-col :span='7' class="pos-order" id="order-list">
     <el-tabs>
        <el-tab-pane label="点餐">
           <el-table :data="tableData" border style="width:100%">
             <el-table-column prop="goodsName" label="商品名称" width='95'></el-table-column>
              <el-table-column prop="count" label="数量" width='70'></el-table-column>
              <el-table-column prop="price" label="价格" width='70'></el-table-column>
               <el-table-column  label="操作" fixed="right" width='100'>
                 <template scope="scope">
                   <el-button type="text" size="small" @click="delSingleList(scope.row)">删除</el-button>
                   <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                 </template>
               </el-table-column>
           </el-table>
           <div class="totalDiv">
             <small>数量</small> :{{totalCount}}个 <small>金额</small>:{{totalMoney}}元
           </div>
           <div class="div-btn">
             <el-button type="warning">挂单</el-button>
             <el-button type="danger" @click="delAllGoods">删除</el-button>
             <el-button type="success" @click="checkOut">结账</el-button>
           </div>
        </el-tab-pane>
        <el-tab-pane label="挂单">
            挂单  
        </el-tab-pane>
        <el-tab-pane label="外卖">
            外卖
        </el-tab-pane>


        
     </el-tabs>
    </el-col>
     <el-col :span='17'>
      <div class="often-goods">
        <div class="title">常用商品 </div>
        <div class="often-goods-list">
          <ul>
            <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
              <span>{{goods.goodsName}}</span>
              <span class="o-price" >￥{{goods.price}}元</span>
            </li>
          </ul>
        </div>
     </div>
     <div class="goods-type">
        <el-tabs>
          <el-tab-pane label="汉堡">
            <div>
              <ul class="cookList">
                <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </el-tab-pane>
          <el-tab-pane label="小吃">
            <div>
              <ul class="cookList">
                <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </el-tab-pane>
          <el-tab-pane label="饮料">
            <div>
              <ul class="cookList">
                <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </el-tab-pane>
          <el-tab-pane label="套餐">
           <div>
              <ul class="cookList">
                <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </el-tab-pane>
        </el-tabs>
     </div>
    </el-col>
  </el-row>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  data(){
    return{
        tableData: [],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney:0,
        totalCount:0
    }
  },
  created:function(){
      axios.get('http://jspang.com/DemoApi/oftenGoods.php')
           .then(response => {
             //console.log(response)
             this.oftenGoods = response.data;
           })
           .catch(error => {
            //console.log(error)
            alert('网络错误')
           }) 
      axios.get('http://jspang.com/DemoApi/typeGoods.php')  
           .then(response => {
             //console.log(response);
             this.type0Goods =response.data[0];
             this.type1Goods =response.data[1];
             this.type2Goods =response.data[2];
             this.type3Goods =response.data[3];
           })
           .catch(error => {
             alert('网络错误')
           })   
  },
  mounted:function(){
    var orderHeight =document.body.clientHeight;
    document.getElementById('order-list').style.height =orderHeight+'px';

  },
  methods:{
     
    //添加商品到订单列表的方法
    addOrderList(ingoods){
      //汇总数量清零
       this.totalMoney=0;
      this.totalCount=0;
      let isHave =false;
      //判断添加的商品是否在订单列表中
      for(let i=0;i<this.tableData.length;i++){
        console.log(this.tableData[i].goodsId)
        if(this.tableData[i].goodsId==ingoods.goodsId){
              isHave=true;//存在
        }
      }
      //根据isHave的值判断订单列表中是否有此商品
      if(isHave){
        //如果存在，数量进行增加
        let arr = this.tableData.filter(o => o.goodsId == ingoods.goodsId)
         console.log(arr);
        arr[0].count++;
       
      }
        else{
        //不存在就放到数组中
        let newGoods = {goodsId:ingoods.goodsId,goodsName:ingoods.goodsName,price:ingoods.price,count:1}
        this.tableData.push(newGoods)
        
    }
    //进行价格和数量的汇总
        this.getAllMoney();
     
    },
    //结账
    checkOut(){
      if(this.totalCount!=0){
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        this.$message({
          message : '结账成功!',
          type:'success'
        })
      }
      else{
        this.$message.error('订单里没有商品哦!')
      }
    },
    //删除单个商品
    delSingleList:function(mgoods){
        this.tableData = this.tableData.filter(o => o.goodsId!=mgoods.goodsId)
        this.getAllMoney();
    },
    //删除全部商品
    delAllGoods:function(){
      this.tableData = [];
      this.totalMoney =0;
      this.totalCount =0;
    },
    //数量金额汇总
    getAllMoney :function(){
      this.totalCount=0;
      this.totalMoney=0;
      if(this.tableData){
         //进行价格和数量的汇总
      this.tableData.forEach(element=>{
        this.totalCount+=element.count;
        this.totalMoney = this.totalMoney+(element.price*element.count);
       
      })
      }
    }
  }
}
</script>


<style scoped>
.pos-order{
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
  
}
.div-btn{
  margin-top: 10px;
}
.title{
  height: 20px;
  background-color: #F9FAFC;
  border-bottom: solid 1px #D3DCE6;
  padding:10px;
  text-align: left; 
}
.often-goods-list ul li{
  list-style: none;
  float: left;
  border-bottom: 1px solid #E5E9F2;
  padding: 10px;
  background-color: #fff;

}
.o-price{
  color:#58B7FF; 
}
.goods-type{
  clear: both;
}
.cookList li{
  list-style:none;
  width: 24%;
  border-bottom: 1px solid #E5E9F2;
  height: auto;overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}

   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv{
     background-color: #fff;
     padding: 10px;
     border-bottom: 1px solid gray;       
   }
</style>
