<template>
  <div class="pos">
      <el-row>
      <el-col :span='7' class="pos-order" id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            
          </el-tab-pane>
          <el-table :data="tableData" border style="width:100%">
            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
            <el-table-column prop="count" label="数量" width="85"></el-table-column>
            <el-table-column prop="price" label="金额" width="100"></el-table-column>
            <el-table-column label="操作" width="100" fixed="right">
              <template scope="scope">
                <el-button type="text" size="small"  @click="delSingleGoods(scope.row)">删除</el-button>
                <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
              </template>
            </el-table-column>
              
          </el-table >
          <div class="totalDiv">
            <small>数量：</small>{{totalCount}}  &nbsp;&nbsp;<small>金额： </small>{{totalMoney}}元
          </div>
          
          <el-tab-pane label="挂单"></el-tab-pane>
          挂单
          <el-tab-pane label="外卖"></el-tab-pane>
          
        </el-tabs>
        <div class="div-btn">
          <el-button type="warning">挂单</el-button>
          <el-button type="danger" @click="delAllGoods()">删除</el-button>
          <el-button type="success" @click="checkout()">结账</el-button>
        </div>
      </el-col>
      <el-col :span="17">
        <div class="oftenGoods">
          <div class="title">热销爆款</div>
          <div class="oftenGoodsList">
            <ul>
              <li v-for="goods in oftenGoods" :key="goods" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <div>
                <ul class="cookList">
                  <li v-for="goods in type0Goods" :key="goods"  @click="addOrderList(goods)">
                    <span class="foodImg"><img src="http://lorempixel.com/400/200" width="100%"></span>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
              </div>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class="cookList">
                  <li v-for="goods in type1Goods" :key="goods"  @click="addOrderList(goods)">
                    <span class="foodImg"><img src="http://lorempixel.com/400/200" width="100%"></span><br>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class="cookList">
                  <li v-for="goods in type2Goods" :key="goods"  @click="addOrderList(goods)">
                    <span class="foodImg"><img src="http://lorempixel.com/400/200" width="100%"></span><br>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class="cookList">
                  <li v-for="goods in type3Goods" :key="goods"  @click="addOrderList(goods)">
                    <span class="foodImg"><img src="http://lorempixel.com/400/200" width="100%"></span><br>
                    <span class="foodName">{{goods.goodsName}}</span>
                    <span class="foodPrice">￥{{goods.price}}元</span>
                  </li>
                </ul>
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
      tableData:[],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalMoney:0,
      totalCount:0,
    }
  },
  created:function(){
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
    .then(response=>{
      console.log(response);
      this.oftenGoods=response.data;
    })
    .catch(error=>{
      alert('网络错误！')
    })

    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
    .then(response=>{
      console.log(response);
      this.type0Goods=response.data[0];
      this.type1Goods=response.data[1];
      this.type2Goods=response.data[2];
      this.type3Goods=response.data[3];

    })
    .catch(error=>{
      alert('网络错误！')
    })
  },
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    document.getElementById('order-list').style.height=orderHeight+'px';

  },
  methods: {
    addOrderList(goods){
      this.totalMoney=0;
      this.totalCount=0;

      //判断商品是否已经存在于订单列表中
      let isHave=false;
      for(let i=0; i<this.tableData.length; i++){
        if(this.tableData[i].goodsId==goods.goodsId){
          isHave=true;
        }
      }
      //根据判断的值编写业务逻辑
      if(isHave){
        //改变列表中商品的数量
        let arr=this.tableData.filter(o=>o.goodsId == goods.goodsId);
        arr[0].count++;
      }else{
        let newGoods={ goodsId: goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1}
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
      
    },

    //删除单个商品
    delSingleGoods(goods){
      this.tableData=this.tableData.filter(o=>o.goodsId!=goods.goodsId);
      this.getAllMoney();
    },

    //删除所有商品
    delAllGoods(){
      this.totalMoney=0;
      this.totalCount=0;
      this.tableData=[]
    },

    //模拟结账
    checkout(){
      if(this.totalCount!=0){
        this.tableData=[];
        this.totalMoney=0;
        this.totalCount=0;
        this.$message({
          message:'结账成功，欢迎下次光临',
          type:'success'
        })
      }else {
        this.$message.error('不能空结，老板迷糊啦！')
      }
    },
    //汇总数量和金额
    getAllMoney(){
      this.totalMoney=0;
      this.totalCount=0;
      if(this.tableData){
        //计算汇总
        this.tableData.forEach((element)=>{
          this.totalCount+=element.count;
          this.totalMoney=this.totalMoney + (element.price*element.count);
        })
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.pos-order{
  background-color: #f9fafc;
  border-right: 1px solid #C0CCDA;
}
.div-btn{
  margin-top: 10px;
}
.title{
  height:20px;
  border-bottom:1px solid #d3dce6;
  background-color: #F9FAFC;
  padding: 10px;
  text-align: left;
}
.oftenGoodsList ul li{
  list-style: none;
  float: left;
  border: 1px solid rgb(110, 148, 224);
  padding: 10px;
  margin: 10px;
  background-color: rgb(239, 242, 247)
}
.o-price{
  color: rgb(134, 149, 233);
}
.goods-type{
  clear: both;
}
.cookList li{
       list-style: none;
       width:15%;
       border:1px solid #E5E9F2;
       height: 15%;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 10px;
       cursor: pointer;
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
      padding-left: 25%;
       width: 50%;
       height: 60%;
   }
   .foodName{
       width: 100%;
       font-size: 18px;
       padding-left: 1%;
       color:#085f63;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 35%;
       padding-top:10px;
   }
   .totalDiv{
     background-color: #fff;
     padding: 10px;
     border-bottom: 1px solid #D3dce6;
   }
</style>
