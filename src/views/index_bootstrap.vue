<template>
	<div style="background: white;">
		<nav class="navbar navbar-default">
		  <div class="container-fluid">
		    <!-- Brand and toggle get grouped for better mobile display -->
		    <div class="navbar-header">
		      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
		        <span class="sr-only">Toggle navigation</span>
		        <span class="icon-bar"></span>
		        <span class="icon-bar"></span>
		        <span class="icon-bar"></span>
		      </button>
		      <a class="navbar-brand"><router-link to="/">首页</router-link></a>
		    </div>
		
		    <!-- Collect the nav links, forms, and other content for toggling -->
		    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
		      <ul class="nav navbar-nav">
		        <li class="active"><router-link to="/register" id="login">注册</router-link></li>
		        <li><router-link to="/login" class="header-title">登录</router-link></li>
		        <li class="dropdown">
		          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">选择背景 <span class="caret"></span></a>
		          <ul class="dropdown-menu">
		            <li><a @click="redbg">红色背景</a></li>
		            <li><a href="#">蓝色背景</a></li>
		            <li><a href="#">蓝色背景</a></li>
		            <li role="separator" class="divider"></li>
		            <li><a href="#">蓝色背景</a></li>
		            <li role="separator" class="divider"></li>
		            <li><a href="#">蓝色背景</a></li>
		          </ul>
		        </li>
		      </ul>
		      <form class="navbar-form navbar-left">
		        <div class="form-group">
		          <input type="text" class="form-control" placeholder="Search">
		        </div>
		        <button type="submit" class="btn btn-default">查询</button>
		      </form>
		      <ul class="nav navbar-nav navbar-right">
		        <li><a><Switch @on-change="change"></Switch></a></li>
		        <li class="dropdown">
		          <a href="#" class="dropdown-toggle" role="button" aria-haspopup="true" aria-expanded="false"><router-link to="/cart">购物车</router-link><span>{{cart_num}}</span></a>
		       
		        </li>
		      </ul>
		    </div><!-- /.navbar-collapse -->
		  </div><!-- /.container-fluid -->
		</nav>
		<div v-show="show_menu">
		    <div class="list-control">
		        <div class="list-control-filter">
		            <span>品牌：</span>
		            <span
		                class="list-control-filter-item"
		                :class="{on: item === filterBrand}"
		                v-for="item in brands"
		                @click="handleFilterBrand(item)" > {{ item }}</span>
		        </div>
		        <div class="list-control-filter">
		            <span>颜色：</span>
		            <span
		                class="list-control-filter-item"
		                :class="{on: item === filterColor}"
		                v-for="item in colors"
		                @click="handleFilterColor(item)">{{ item }}</span>
		        </div>
		        <div class="list-control-order">
		            <span>排序：</span>
		            <span
		                class="list-control-order-item"
		                :class="{on: order === ''}"
		                @click="handleOrderDefault">默认</span>
		            <span
		                class="list-control-order-item"
		                :class="{on: order === 'sales'}"
		                @click="handleOrderSales">
		                销量
		                <template v-if="order === 'sales'">↓</template>
		            </span>
		            <span
		                class="list-control-order-item"
		                :class="{on: order.indexOf('cost') > -1}"
		                @click="handleOrderCost">
		                价格
		                <template v-if="order === 'cost-asc'">↑</template>
		                <template v-if="order === 'cost-desc'">↓</template>
		            </span>
		        </div>
		    
		    <Product v-for="item in filteredAndOrderedList" :info="item" :key="item.id"></Product>
		    <div class="product-not-found" v-show="!filteredAndOrderedList.length">暂无相关商品</div>
		</div>
		</div>
		<h1>filteredAndOrderedList</h1>
		<p v-for="index in 13">
			{{filteredAndOrderedList[index-2]}}
		</p>
			
		
		
	</div>

	    
	  
	
</template>

<script>
	import Product from '../components/product.vue';

	 export default {
		 components: { Product },
	        data () {
	            return {
	                filterBrand: '',
	                filterColor: '',
	                order: '',
					cart_num:5,
					show_menu:true,
					bg :"background:'black'"
	            }
	        },
			computed:{
				list () {
				    return this.$store.state.productList;
				},
				brands () {
				    return this.$store.getters.brands;
				},
				colors () {
				    return this.$store.getters.colors;
				},
				filteredAndOrderedList () {
				    let list = [...this.list];
				    // 按品牌过滤
				    if (this.filterBrand !== '') {
				        list = list.filter(item => item.brand === this.filterBrand);
				    }
				    // 按颜色过滤
				    if (this.filterColor !== '') {
				        list = list.filter(item => item.color === this.filterColor);
				    }
				    // 排序
				    if (this.order !== '') {
				        if (this.order === 'sales') {
				            list = list.sort((a, b) => b.sales - a.sales);
				        } else if (this.order === 'cost-desc') {
				            list = list.sort((a, b) => b.cost - a.cost);
				        } else if (this.order === 'cost-asc') {
				            list = list.sort((a, b) => a.cost - b.cost);
				        }
				    }
				    return list;
				}
			},
			methods:{
				change(){
					this.show_menu = this.show_menu==false?true:false;
				},
				handleFilterBrand (brand) {
				    if (this.filterBrand === brand) {
				        this.filterBrand = '';
				    } else {
				        this.filterBrand = brand;
				    }
				},
				handleFilterColor (color) {
				    if (this.filterColor === color) {
				        this.filterColor = '';
				    } else {
				        this.filterColor = color;
				    }
				},
				handleOrderDefault () {
				    this.order = '';
				},
				handleOrderSales () {
				    this.order = 'sales';
				},
				handleOrderCost () {
				    if (this.order === 'cost-desc') {
				        this.order = 'cost-asc';
				    } else {
				        this.order = 'cost-desc';
				    }
				},
				redbg(){
					var navbar = document.getElementsByClassName('navbar');
					
				}
				
				
			},
			mounted () {
			    this.$store.dispatch('getProductList');
			}
	    }
</script>
<style scoped>
  .list-control{
      background: #fff;
      border-radius: 6px;
      margin: 16px;
      padding: 16px;
      box-shadow: 0 1px 1px rgba(0,0,0,.2);
  }
  .list-control-filter{
      margin-bottom: 16px;
  }
  .list-control-filter-item,
  .list-control-order-item {
      cursor: pointer;
      display: inline-block;
      border: 1px solid #e9eaec;
      border-radius: 4px;
      margin-right: 6px;
      padding: 2px 6px;
  }
  .list-control-filter-item.on,
  .list-control-order-item.on{
      background: #f2352e;
      border: 1px solid #f2352e;
      color: #fff;
  }
  .product-not-found{
      text-align: center;
      padding: 32px;
  }
</style>
