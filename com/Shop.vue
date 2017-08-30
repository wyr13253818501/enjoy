<template>
	<div class="scontent" v-if="si">
		<!--购物车有数据时-->
		<div v-if="zai">
			<ul class="pList">
				<li v-for="(item,index) in dataList">
					<div class="left">
						<img :src="item.img"/>
					</div>
					<div class="right">
						<p>{{item.name}}</p>
						<p>单价：<span>{{item.price/100}}</span>元</p>
						<p><span @click="decase(index)">-</span><span>{{item.num}}</span><span @click="add(index)">+</span></p>
						<h6 class="iconfont" @click="deletePro(index)">&#xe621;</h6>
					</div>
				</li>
			</ul>			
			<div class="like">
				<p class="like">猜你喜欢</p>
				<ul class="likeList">
					<li v-for="lis in titleList.content" @click="goDetail(lis.enjoy_url)">
						<img :src="lis.product_image"/>
						<p class="likePrice">{{lis.name}}</p>
						<p class="likePrice"><a>{{lis.price/100}}元{{lis.show_entity_name}}</a><a>￥{{lis.original_price/100}}</a></p>
					</li>
				</ul>
			</div>
			<div class="account">
				<div class="account-one">
					<p><a>合计:</a><a>{{sum}}元</a></p>
				</div>
				<div class="account-two" @click="goApply()">
					<p>结算</p>
				</div>
				
			</div>
		</div>
		
		
		
		<!--购物车为空的情况-->
		<div v-if="!zai">
			<div class="cart-empty">
				<img src="https://s1.ricebook.com/feck/web-cart/37cea7f9c3bd1d34c0eca258281ff871.png"/>
				<p>购物车是空的哦~</p>
			</div>
			<div class="like">
				<p class="like">猜你喜欢</p>
				<ul class="likeList">
					<li v-for="lis in titleList.content" @click="goDetail(lis.enjoy_url)">
						<img :src="lis.product_image"/>
						<p class="likePrice">{{lis.name}}</p>
						<p class="likePrice"><a>{{lis.price/100}}元{{lis.show_entity_name}}</a><a>￥{{lis.original_price/100}}</a></p>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>
<script type="text/javascript">
	import "./../scss/shop.scss"
	import MyAjax from "./../md/MyAjax.js";
	import VueRouter from "vue-router"
	import Vue from "vue"
	Vue.use(VueRouter);
	var router=new VueRouter({

	})
	export default{
		data(){
			return{
				dataList:[],
				sum:0,
				zai:false,
				titleList:[],
				si:false,
				heji:''
			}
		},
		methods:{
			goDetail(id){
				router.push({path:"detail",query:{url:id}})
			},
			decase(index){
				var that=this;
				let arr=JSON.parse(localStorage.getItem("goods"));
				arr[index].num--;
				localStorage.setItem("goods",JSON.stringify(arr))
				that.dataList=JSON.parse(localStorage.getItem("goods"))
				var arr1=JSON.parse(localStorage.getItem("goods"))
				that.sum=0
				for(var i=0;i<arr1.length;i++){
					that.sum+=arr1[i].price/100*arr1[i].num
				}
				localStorage.setItem("money",that.sum)
			},
			add(index){
				var that=this;
				let arr=JSON.parse(localStorage.getItem("goods"));
				arr[index].num++;
				localStorage.setItem("goods",JSON.stringify(arr))
				that.dataList=JSON.parse(localStorage.getItem("goods"))
				var arr1=JSON.parse(localStorage.getItem("goods"))
				that.sum=0
				for(var i=0;i<arr1.length;i++){
					that.sum+=arr1[i].price/100*arr1[i].num
				}
				localStorage.setItem("money",that.sum)
			},
			deletePro(index){
//				console.log(index);
				var that=this;
				let arr=JSON.parse(localStorage.getItem("goods"));				
				arr.splice(index,1)
				localStorage.setItem("goods",JSON.stringify(arr))
				$(".pList li").eq(index).css("display","none")
				if(!localStorage.getItem("goods")||JSON.parse(localStorage.getItem("goods"))==""){
					that.zai=false;
				}else{
					that.zai=true;
				}
				
				var arr1=JSON.parse(localStorage.getItem("goods"))
				that.sum=0
				for(var i=0;i<arr1.length;i++){
					that.sum+=arr1[i].price/100*arr1[i].num
				}
				localStorage.setItem("money",that.sum)
				
			},
			goApply(){
				router.push({path:"apply"})
			}
		},
		mounted(){
			var that=this;
			that.dataList=JSON.parse(localStorage.getItem("goods"))
			var arr=JSON.parse(localStorage.getItem("goods"))
			if(!localStorage.getItem("goods")||JSON.parse(localStorage.getItem("goods"))==""){
				that.zai=false;
			}else{
				that.zai=true;
			}			
			var url="https://api.ricebook.com/3/enjoy_product/cart_recommend_product.json?city_id=1";
			MyAjax.vueJson(url,function(data){
//				console.log(data)
				that.titleList=data;
				that.si=true
			},function(err){
				console.log(err)
			});
			
			for(var i=0;i<arr.length;i++){
				that.sum+=arr[i].price/100*arr[i].num
			}
			localStorage.setItem("money",that.sum)
		}
	}
</script>