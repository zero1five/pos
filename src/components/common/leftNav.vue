<template>
  <div class="left-nav">
      <ul>
          <li>
              <i class="icon iconfont icon-shouyin"></i>
              <div>收银</div>
          </li>
          <li>
              <i class="icon iconfont icon-jinrudianpu"></i>
              <div>店铺</div>
          </li>
          <li>
              <i class="icon iconfont icon-shangpin"></i>
              <div>商品</div>
          </li>          
          <li>
              <i class="icon iconfont icon-huiyuantianjiakehu"></i>
              <div>会员</div>
          </li>
          <li>
              <i class="icon iconfont icon-tongji"></i>
              <div>统计</div>
          </li>                              
      </ul>
  </div>
</template>

<script>
    export default {
        
    }
</script>

<style>
    .left-nav {
        color: #fff;
        font-size: 10px;
        height: 100%;
        background: #1d8ce0;
        float: left;
        width: 5%;
    }
    .iconfont {
        font-size: 24px;
    }
    .left-nav ul {
        padding: 0;
        margin: 0;
    }
    .left-nav li {
        list-style: none;
        text-align: center;
        border-bottom: 1px solid #20a0ff;
        padding: 10px;
        cursor: pointer;
        transition: all .4s;
        background: #60a8df;
    }
    .left-nav li:hover{
        background: #f58c36;
    }
</style>

