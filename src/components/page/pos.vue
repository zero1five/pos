<template>
  <div class="pos">
      <el-row>
          <el-col :span="6" class="pos-order" id="orderList">
              <el-tabs>
                  <el-tab-pane label="点餐">
                      <el-table :data="tabData" border style="width:100%">
                          <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                          <el-table-column prop="count" label="数量" width="50"></el-table-column>
                          <el-table-column prop="price" label="金额" width="70"></el-table-column>
                          <el-table-column label="操作" width="100" fixed="right">
                            <template scope="scope">
                                <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                                <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                            </template>
                          </el-table-column>
                      </el-table>
                      <div class="totalDiv">
                          数量: {{totalCount}}  |  金额: {{totalMoney}}元
                      </div>
                      <div class="div-btn">
                          <el-button type="warning" @click="hang()">挂单</el-button>
                          <el-button type="danger" @click="delAllGoods()">删除</el-button>
                          <el-button type="success" @click="checkout()">结账</el-button>
                      </div>
                  </el-tab-pane>

                  <el-tab-pane label="挂单">
                     <el-table border  :data="tabData" style="width:100%">
                          <el-table-column label="挂单量" prop="goodsName"></el-table-column>
                          <el-table-column label="单数"  prop="count" width="70"></el-table-column>
                      </el-table>
                      <div class="div-btn">
                          <el-button type="success" @click="checkout()">结账</el-button>
                      </div>
                  </el-tab-pane>
                  <el-tab-pane label="外卖">
                      外卖
                  </el-tab-pane>
              </el-tabs>
          </el-col>
          <el-col :span="18"> 
              <div class="often-goods">
                  <div class="title">常用商品</div>
                  <div class="often-goods-list">
                      <ul>
                          <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
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
                            <ul class='cookList'>
                                <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                    <span class="foodName">{{goods.goodsName}}</span>
                                    <span class="foodPrice">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                          </div>
                      </el-tab-pane>
                      <el-tab-pane label="小食">
                          <ul class='cookList'>
                                <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                    <span class="foodName">{{goods.goodsName}}</span>
                                    <span class="foodPrice">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                      </el-tab-pane>
                      <el-tab-pane label="饮料">
                          <ul class='cookList'>
                                <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                                    <span class="foodName">{{goods.goodsName}}</span>
                                    <span class="foodPrice">￥{{goods.price}}元</span>
                                </li>
                            </ul>
                      </el-tab-pane>  
                      <el-tab-pane label="套餐">
                          <ul class='cookList'>
                                <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                                    <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
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
        data () {
            return {
        tabData: [],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney: 0,
        totalCount: 0,
            }
        },
        created() {
            axios.get('http://jspang.com/DemoApi/oftenGoods.php')
                .then(response=>{
                    this.oftenGoods = response.data;
                })
                .catch(error=>{
                    alert('网络错误，不能访问-0-')
                })

            axios.get('http://jspang.com/DemoApi/typeGoods.php')
                .then((response)=>{
                    this.type0Goods = response.data[0];
                    this.type1Goods = response.data[1];
                    this.type2Goods = response.data[2];
                    this.type3Goods = response.data[3];
                })
                .catch((error)=>{
                    alert('网络错误，不能访问-0-')
                })

        },
        mounted () {
            var orderHeight = document.body.clientHeight;
            document.getElementById('orderList').style.height = orderHeight + 'px';
        },
        methods: {
            addOrderList(goods){

                this.totalMoney = 0;
                this.totalCount = 0;
                
                //商品是否已经存在于订单列表中
                let isHave = false;
                for(let i=0; i<this.tabData.length; i++){
                    if(this.tabData[i].goodsId == goods.goodsId){
                        isHave = true;
                    }
                }
                //根据判断的值编写业务逻辑
                if(isHave){
                    //改变列表中商品数量
                    let arr = this.tabData.filter(a=>a.goodsId == goods.goodsId);
                    arr[0].count++;
                    this.getAllMoney();
                }else {
                    let newGoods = {goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1}
                    this.tabData.push(newGoods)
                }   
                this.getAllMoney();
                //计算金额以及数量
               /*  this.tabData.forEach((element)=>{
                    this.totalCount += element.count;
                    this.totalMoney = this.totalMoney+(element.price*element.count);
                }) */
            },
            //删除选中列表内的单个商品
            delSingleGoods(goods){
                this.tabData = this.tabData.filter(a=>a.goodsId != goods.goodsId);
                this.getAllMoney();

            },
            //计算金额和数量的方法
            getAllMoney(){
                this.totalMoney = 0;
                this.totalCount = 0;
                if(this.tabData){
                    this.tabData.forEach((element)=>{
                        this.totalCount += element.count;
                        this.totalMoney = this.totalMoney+(element.price*element.count);
                    })
                }
            },
            //模拟结账
            checkout(){
                if(this.totalCount !=0){
                    this.tabData = [];
                    this.totalMoney = 0;
                    this.totalCount = 0;
                    this.$message({
                        message: '结账成功，感谢你又为店里出了一份力!',
                        type: 'success'
                    })
                }else {
                    this.$message.error('当前无账单哦~老板了解你急切的心情！')
                }
            },
            hang(){
                if(this.totalCount !=0){
                    this.$message({
                            message: '当前账单已成功挂单 请到挂单界面查看！',
                            type: 'warning'
                    })
                }else {
                    this.$message({
                            message: '请确定点餐完成后再点击挂单 -',
                            type: 'error'
                    })
                }
            },

            //删除全部的商品
            delAllGoods(){
                if(this.totalCount !=0){
                    this.tabData = [];
                    this.totalMoney = 0;
                    this.totalCount = 0;
                    this.$message({
                            message: '当前餐品成功删除!',
                            type: 'success'
                    })
                }else {
                    this.$message({
                            message: '当前并未添加餐品',
                            type: 'error'
                    })
                }
            }
        }

    }
</script>

<style>
    .pos-order {
        background: #f9fafc;
        border-right: 1px solid #c0ccda;
    }
    .div-btn {
        margin-top: 10px;
    }
    .title {
        height: 20px;
        border-bottom: 1px solid #d3dce6;
        background: #f9fafc;
        padding: 10px;
        text-align: left;
    }
    .often-goods-list ul li{
        list-style: none;
        float: left;
        border: 1px solid #e5e9f2;
        padding: 10px;
        margin: 10px;
        background: #fff;
        cursor: pointer;
        transition: all .4s;
    }
    .often-goods-list ul li:hover{
        background: #f0d2b1;
    }
    .o-price {
        color: #58b7ff;
    }
    .goods-type {
        clear: both;
    }
    .cookList li{
        list-style: none;
        width:23%;
        border:1px solid #E5E9F2;
        height: auot;
        overflow: hidden;
        background-color:#fff;
        padding: 2px;
        float:left;
        margin: 2px;
        cursor: pointer;
        transition: all .4s;
        box-shadow: 0 3px 5px rgba(170, 168, 168, 0.7);
    }
    .cookList li:hover{
        background: #f0d2b1;
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
    .totalDiv {
        background: #fff;
        padding: 10px;
        border-bottom: 1px solid #f0d2b1;
    }
    .el-tabs__nav-scroll {
        margin-left: 10px;
    }
</style>

