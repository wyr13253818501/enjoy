<template>
	<div id="cartconyent" v-if="flag">
		<div class="top">
			<div class="back_icon" id="back_btn"><img src="http://s17.mogucdn.com/p1/160922/idid_ie3wmnbvgftginzsmizdambqgayde_35x52.png"></div>
			<div class="top-title">
				<a href="javascript:;" class="top-p">购物车</a>
			</div>
			<a class="right_btn" href="javascript:;">删除</a>
		</div>

		<ul class="cartlist">
			<li class="goods-list" v-for="(t,index) in goods">
				<input type="checkbox" class="check" :class="{'check': t.checked}"  @click="selectedProduct(t)" />
				<div class="main">
					<span class="pic-box">
						<img :src="t.src[0]">
					</span>
					<div class="middle">
						<span class="middle-title">{{t.title}}</span>
						<p class="prop">
							<span class="first">颜色：白字母</span>
							<span class="">尺码：均码</span>
						</p>
						<div class="jisuan">
							<span class="jian" @click="changeMoney(t,-1)">-</span>
							<input type="text" value="1" class="ipt" v-model="t.num"/>
							<span class="jia" @click="changeMoney(t,1)">+</span>
							<i class="iconfont" @click="Delete(t.id,t.index)">&#xe60e;</i>
						</div>
					</div>
					<div class="right">
						<p class="goods_price">{{t.nowPrice}}</p>
						<p class="origin_price">{{t.num}}</p>
						<span class="xiaoji">{{((t.nowPrice).substring(1))*t.num}}</span>
					</div>
				</div>
			</li>
		</ul>
		<div class="bottom">
			<div class="bottom-box">
				<input type="checkbox" class="checks " :class="{'check':checkAllFlag}"  @click="checkAll(true)"/>
				<span  id="qx">全选</span>
				<!--//v-show="!checkAllFlag"-->
				<div class="type-box">
					<p class="bottom-p"> 合计</p><span>${{Money}}</span>
				</div>

			</div>

		</div>
	</div>

	<div id="cartcontent" v-else="flag">
		<div class="top1">
			<div class="back_icon1" id="back_btn1"><img src="http://s17.mogucdn.com/p1/160922/idid_ie3wmnbvgftginzsmizdambqgayde_35x52.png"></div>
			<div class="top-title1">
				<a href="javascript:;" class="top-p1">购物车</a>
			</div>
			<a class="right_btn1" href="javascript:;" @click="goShou()">首页</a>
		</div>
		<div class="cart-box">
			<p>您的购物车还空着呢，</p>
			<p>美物这么多，快去看看吧～</p>
			<div class="p-box">
				<p class="btn" @click="goOne(1)">去逛逛</p>
			</div>

		</div>
	</div>
</template>

<script>
    import MyAjax from "./../js/MyAjax.js"
	import "./../scss/cart.scss"

    import routes from "./../routes/routes.js";
    import VueRouter from "vue-router";

    import Vue from 'vue';
    import { Toast } from 'mint-ui';

    Vue.use(VueRouter);
    let router=new VueRouter({
        routes
    })

    export default {
        data() {
            return {
                goods: [],
                flag: false,
                checkAllFlag: false,
                Money: 0,
				isChcked:[]
            }
        },

        watch: {
            $route: function () {

            }
        },

        methods: {
            Delete(id,index){
                console.log(id,index)
				let that=this;
                let arr=JSON.parse(localStorage.getItem("goods"));
                for(let i in arr){
                    if(id==arr[i].id){
                        $(".goods-list").eq(index).css({display:"none"});
                        arr.splice(i,1)
                        localStorage.setItem("goods",JSON.stringify(arr));
                        if(arr.length==0){
                          that.goods=[]
						}
                        localStorage.setItem("goods",JSON.stringify(arr));
					}
				}




			},


            changeMoney: function (t, way) {
                let that = this;
                let arr = JSON.parse(localStorage.getItem("goods"));
                if (way > 0) {
                    for (let i in arr) {
                        let id = t.id;
                        if (id == arr[i].id) {
                            arr[i].num++;
                            t.num++
                        }
                    }
                    localStorage.setItem("goods", JSON.stringify(arr));

                } else {
                    if (t.num <= 1) {
                        t.num = 1;
                        Toast('数量不能小于1')
                        let id = t.id;
                        for (let i in arr) {
                            if (id == arr[i].id) {
                                arr[i].num = 1
                            }
                        }
                        localStorage.setItem("goods", JSON.stringify(arr));
                    } else {
                        let id = t.id;
                        for (let i in arr) {
                            if (id == arr[i].id) {
                                arr[i].num--;
                                t.num--;
                            }
                        }
                        localStorage.setItem("goods", JSON.stringify(arr));
                    }
                }

                that.calcTotalPrice()
            },


            selectedProduct: function (t) {
                let that = this;
                if (typeof t.checked == 'undefined') {
//				    Vue.set(t,"checked",true)
                    that.$set(t, 'checked', true);//局部注册 加$
                } else {
                    t.checked = !t.checked; // 有了之后 点击是false
                }
                that.calcTotalPrice()
            },


            checkAll: function (event) {
//                event.stopPropagation()
//                var that=this;
//					console.log(event.target.parentNode.parentNode.previousSibling.previousSibling.childNodes)
//               var oli=event.target.parentNode.parentNode.previousSibling.previousSibling.childNodes
//				for(var i=0;i<oli.length;i++){
//                    console.log(oli[i].firstChild)
//                    oli[i].firstChild.checked="checked"
//				}
//
				this.checkAllFlag = !this.checkAllFlag;
                let that = this;

//                for(let t in that.goods){
//                    if(that.checkAllFlag){
//                        that.isChcked[t]=true;
//                    }else{
//                        that.isChcked[t]=false;
//                    }
//				}

                that.goods.forEach(function (t, index) {
                    if (typeof t.checked == 'undefined') {
                            Vue.set(t,"checked",that.checkAllFlag)
//                        that.$set(t, 'checked', that.checkAllFlag);//局部注册 加$
                    } else {
                        t.checked = that.checkAllFlag;// 有了之后 点击是false
                    }

                })
                that.calcTotalPrice()

            },

            calcTotalPrice: function () {
                let that = this;
                that.Money = 0;
                that.goods.forEach(function (t, index) {
                    if (t.checked) {
                        that.Money += ((t.nowPrice).substring(1)) * t.num
                    }
                })

            },


            goOne(a) {
                if (localStorage.getItem("islogin") == "1") {
                    router.push({path: '/'})

                } else {
                    Toast('请先登录')
                    router.push({path: 'my', query: {cart: a}})
//					console.log(this.$route.query.cart)
                }
            },


            goShou() {
                router.push({path: '/'})
            },



        },


		mounted () {
		   let that=this;
		   let arr=[];
		   if(localStorage.getItem("goods")){
               let it=JSON.parse(localStorage.getItem("goods"));
               let id=0;
			   for(let i in it){
			       let gt=it[i].id;
                   let url = "http://m.meilishuo.com/jsonp/mls.h5_pintuan/v1?activityId=1bjre8&itemId="+gt
				   MyAjax.vueJsonp(url,function (data) {
//					   console.log(data.data.itemInfo)
					   let obj1=data.data.itemInfo;
                       let godobj={
							src:obj1.topImages,
						   	title:obj1.title,
                            nowPrice:obj1.price,
                            num:it[i].num,
                            id:it[i].id,
						   	index:id,
					   };
                       id++;
					   arr.push(godobj);
                       that.goods=arr;

//                       for(var t in that.goods){
//                           that.isChcked[t]=false;
//                       }

                   },function (err) {

                   })

			   }

		   }else{
				let goods=[];
				this.goods=arr;
		   }


			let arr1=JSON.parse(localStorage.getItem("goods"))
			if(arr1.length == 0){
				this.flag=false;
			}else{
				this.flag=true;
			}


		},

	}
</script>

<style>
</style>