<template>
	<header class="header">
		<div class="commonHeader">
			<div class="header-one" @click="goHome()">
				<p>首页</p>
			</div>
			<div class="header-two">
				<p>ENJOY</p>
				<b class="iconfont" @click="homeCity()">&#xe728;</b>
			</div>
			<div class="header-three" @click="goLogin()">
				<p v-if="!people">登录</p>
				<p v-if="people" class="iconfont">&#xe635;</p>
			</div>
			<div class="header-four">
				<p class="iconfont">&#xe6ae;</p>
			</div>
		</div>
	</header>
</template>

<script type="text/javascript">
	import "./../scss/list.scss"
	import VueRouter from "vue-router"
	import Vue from "vue"
	Vue.use(VueRouter);
	var router=new VueRouter({

	})
	export default{
		data(){
			return{
				people:true
			}
		},
		methods:{
			goHome(){
				router.push({path:'home'})
			},
			goLogin(){
				router.push({ path: 'login' })
			},
			homeCity(){
				router.push({path:'home'})
			}
		},
		mounted(){
			var that=this;
			if(localStorage.getItem("isLogin")){
				that.people=true;
			}else{
				that.people=false
			}
		}
	}
</script>