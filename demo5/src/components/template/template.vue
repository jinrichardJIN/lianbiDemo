<template>
	<div class='downloadContent outHide '>
		<!--弹窗-->
		<div class="positionF width100 height100 alertBOX">
			<div class="alertGoAPP">
				<p>是否下载<br>多点金融APP</p>
				<div>
					<span class="colorBlack borderRightGray" @click="cancel()">取消</span>
					<span>
						<a href="http://a.app.qq.com/o/simple.jsp?pkgname=com.duodianjr.finance" class="color2d982e">前往</a>
					</span>
				</div>
			</div>
		</div>

		<!--第一个图片栏-->
		<div class="column">
			<img src="./template1.jpg" v-bind:src='bannerImg'>
		</div>
		<!--第二个图片栏-->
		<div class='bannerWrapper ML-1 column SecondConlumn' style="background-size:cover">
			<div class='bannerContent' id="bannerContent">
				<div class='contentRight  font14 bannerContentDiv' id="bannerContentDiv">
					<div class="whiteBkg radius5 MB12 phoneNum bannerContentDiv clearfix positionR outHide">
						<img src="./phone.png" class="positionA active" style="height:28px;width:28px;top:8px;left:8px" >
						<input type="text" id="phoneNum" maxlength="11" placeholder="请输入手机号" class="fl ML45" v-model="phoneNum" isKey="1">
					</div>

					<div class="whiteBkg radius5 MB12 outHide captcha positionR bannerContentDiv clearfix">
					<img src="./code.png" class="positionA active" style="height:28px;width:28px;top:8px;left:8px" >
						<input type="text" maxlength="4" placeholder="请输入验证码" class="positionA " style="left:45px" v-model="captcha" id="captcha" isKey="2">
						<span id="Countdown" class=" verification positionA textC radius5R blackTex2 buttonColor" style="" @click="getJson()" v-text="timerCodeMsg">
                  		</span>
					</div>
					<div class="whiteBkg radius5 MB12 bannerContentDiv clearfix positionR outHide">
					<img src="./password.png" class="positionA active" style="height:28px;width:28px;top:8px;left:8px" >
						<input type="password" id="password" maxlength="16" placeholder="请设置登录密码" class="fl ML45 password" style="" v-model="password" isKey="3" >
					</div>
					<div class="whiteBkg radius5 MB12 outHide verifycode positionR bannerContentDiv outHide">
					     <img src="./password2.png" class="positionA active" style="height:28px;width:28px;top:8px;left:8px" >
	               		 <input  type="password" maxlength="6" placeholder="请输入6位数字交易密码" class="fl ML45 " style="" v-model="verifycode" id="verifycode" isKey="4">

	                </div>

					<a class="radius5 textC block font16  bannerContentDiv buttonColor blackTex2" @click="checking()">注册</a>
					<div class="agreenment ">
						<span class="font14">
							我同意
							<a   id="register" class="pointer" @click="registration()">《用户注册服务协议》
							</a>
						</span>

					</div>
				</div>
			</div>
		</div>
		<!--第三个图片栏-->
		<div class="column activeRule" style="background-size:cover">
			<div></div>
			<p class="width90 blockC font14  whiteTex height300 lineHeight25px positionR" v-html='ruleContent' style="top:40px">
			</p>

		</div>
		<!--第四个图片栏-->
		<div class="column">
			<img src="./template4.jpg" v-bind:src='footer'>
		</div>

		<!--底部-->
		<div class="DownLoadApp">
			<div class="overHidden">
				<img class="fl" src="./duodianLogo.png">
				<div class="fl height90P ML5P MT2point5 positionR">
					<p class="textWhite font16">多点金融APP</p>
					<p class="textWhite font12 opacity7 positionA bottom0">安全.便捷.精彩</p>
				</div>
				<a href="http://a.app.qq.com/o/simple.jsp?pkgname=com.duodianjr.finance" class="RightNowDownLoad">立即下载</a>
			</div>
		</div>
	</div>
</template>

<script>
	import Advantage from '../advantage/advantage';
	import { getRealPath, regexp, alertBox2, ajaxRequest, errorMessage } from 'assets/js/merge.js';
	let Base64 = require('js-base64').Base64;
	export default {
		data() {
			return {
				webUrl: getRealPath(),
				phoneNum: "", //手机号
				captcha: "", //验证码
				password: "", //登录密码
				imgUrl: {},
				verifycode:"",
				bannerImg: require('./template1.jpg'),
				codeUrl: getRealPath() + 'imgcode?' + Math.random(),
				footer:require('./template4.jpg'),
				timerCodeMsg: "获取",
				fetchCodeMsg: false,
				fetchCodeMsgN: true,
				detail: "",
				sessionStorage: "",
				mobile: this.GetRequest().mobile,
				channel: this.GetRequest().channel||'TEMPLATE1', //渠道
				ruleContent: '1、本活动仅限通过本页面注册多点金融的新用户；2、注册多点金融并成功首次投资定期产品1000元及以上，即送电影票1张，首投定期2000元及以上，即送电影票2张；3、有任何问题可以咨询多点金融客服热线 400-998-9966，关注官方微信多点金融。', //活动规则文案
				token: '', //注册token
				bannerColour:''
			}
		},
		watch: {
			phoneNum(val) {
				let reg = /^[0-9]{0,11}$/;
				this.phoneNum = regexp(reg, val)
			},
			captcha(val) {
				let reg = /^[a-zA-Z0-9]{0,4}$/;
				this.captcha = regexp(reg, val)
			},
			password(val) {
				let reg = /^[a-zA-Z0-9\!\@\#\$\%\^\&\*]{0,16}$/;
				this.password = regexp(reg, val)
			},
			 verifycode(val){
		        let reg=/^[0-9]{0,6}$/;
		        this.verifycode=regexp(reg,val)
		      }
		},
		computed: {

		},
		methods: {
			getJson: function() {
				this.checkingT();
				if(this.fetchCodeMsg && this.fetchCodeMsgN) { //验证码
					$.post(this.webUrl + 'javaservice/webClient?functionId=captcha', { data: JSON.stringify({ 'mobile': this.phoneNum, 'type': 'REGISTER', 'blackBox': this.sessionStorage }) }).then((res) => {
						res = JSON.parse(res);
						this.replaceFlag();
						this.detail = res.resMsg;

						if(res.resCode == "0000") {
							this.SetRemainTime();
						} else {
							errorMessage(this.detail)
						}
					}, (error) => {
						this.fetchCodeMsgN = true;

					})
				}
			},
			//获取url中的参数 GetRequest().name   GetRequest().mobile
			GetRequest: function() {
				var strs = '';
				var url = window.location.href; //获取url中"?"符后的字串
				url = '?' + url.split('?')[1];
				var theRequest = new Object();
				if(url.indexOf("?") != -1) {
					var str = url.substr(1);
					strs = str.split("&");
					for(var i = 0; i < strs.length; i++) {
						theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
					}
				}
				return theRequest;
			},
		checking: function() {
				if (this.phoneNum=="") {
          $("#phoneNum").attr({"placeholder":"请输入手机号"}).addClass("input");
         return false;
       }
        if (! (/^((13|15|18|14|17)[0-9]{9})$/.test(this.phoneNum))) {
          $("#phoneNum").attr({"placeholder":"请输入正确的手机号码"}).addClass("input");
          this.phoneNum="";
          return false;
      }

         this.fetchCodeMsg=true;
        if (!this.captcha) {
           $("#captcha").attr({"placeholder":"请输入短信验证码"}).addClass("input");
           return false;
        };
        if (this.password == null || this.password == '') {
        $("#password").attr({"placeholder":"请设置登录密码"}).addClass("input");
        return false;
        };
        if(this.password.length<8){
          errorMessage('登录密码为8~16位字母数字或符号的组合');
            return false;
        }
          if(/^[\d]+$/.test(password)||/^[a-zA-Z]+$/.test(this.password)) {
          errorMessage('登录密码为8~16位字母数字或符号的组合');
          return false;
        };
        if(!(/(?!^[0-9]*$)(?!^[a-zA-Z]*$)^([a-zA-Z0-9\!\@\#\$\%\^\&\*]{8,16})$/.test(this.password))){
            $("#password").attr({"placeholder":"登录密码为8~16位字母数字或符号的组合"}).addClass("input");
            this.password="";
            return false;
        }
         if (this.verifycode == "" || this.verifycode  == null) {
            this.verifycode="";
           $("#verifycode").attr({"placeholder":"请设置交易密码"}).addClass("input");
          return false;
        }
       if (! (/^[0-9]{6}$/.test(this.verifycode))) {
            this.verifycode="";
           $("#verifycode").attr({"placeholder":"请设置交易密码"}).addClass("input");
          return false;
        }
        this.register()
			},
		checkingT: function() {

				 if (this.phoneNum=="") {
          $("#phoneNum").attr({"placeholder":"请输入手机号"}).addClass("input");
         return false;
       }
        if (! (/^((13|15|18|14|17)[0-9]{9})$/.test(this.phoneNum))) {
          $("#phoneNum").attr({"placeholder":"请输入正确的手机号码"}).addClass("input");
          this.phoneNum="";
          return false;
      }

         this.fetchCodeMsg=true;
			},
			replaceFlag: function() {

				$("#reminder").css({ "top": "0" });
				setTimeout(function() {
					$("#reminder").css({ "top": "-76px" })
				}, 3000)
			},
			changeUrl: function() {
				this.codeUrl = this.codeUrl.replace(/\?.*/ig, "") + "?" + Math.random();
			},
			SetRemainTime: function() {
				let sec = 60;
				var _this = this;
				_this.fetchCodeMsgN = false;
				$("#Countdown").removeClass("blackTex2 ").addClass(" whiteTex");
				$("#Countdown").css('background','#616161' )
				for(let i = 0; i <= 60; i++) {
					window.setTimeout(function() {
						if(sec != 0) {
							_this.timerCodeMsg = sec + "秒";
							sec--;
						} else {
							sec = 60; //如果倒计时结束就让  获取验证码显示出来
							$("#Countdown").removeClass("whiteTex").addClass(" blackTex2 ");
							$("#Countdown").css('background',_this.bannerColour );
							console.log(_this.bannerColour)
							_this.timerCodeMsg = "再次获取"
							_this.fetchCodeMsgN = true;
						}
					}, i * 1000);

				}
			},
			//跳转用户服务注册协议
			registration: function() {
				var u = navigator.userAgent,
					app = navigator.appVersion;
				var isAndroid = u.indexOf('Android') > -1 || u.indexOf('Linux') > -1; //android终端或者uc浏览器
				var isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
				var os = 'android';
				if(isiOS) {
					os = 'ios';
				}
				$.post(this.webUrl + 'javaservice/webClient?functionId=getUrl&os=' + os, { data: JSON.stringify({ 'name': 'register' }) }).then((res) => {
					res = JSON.parse(res);
					var serAgreementUrl = res.record.url
					if(res.resCode != "0000") {} else {
						window.location.href = this.webUrl + serAgreementUrl;
					}
				}, (error) => {

				})
			},
			//注册请求
			register: function() {

				var _this = this;
				$.post(this.webUrl + 'javaservice/webClient?functionId=register', { data: JSON.stringify({ 'mobile': this.phoneNum, 'password': this.password,'cashPassword':this.verifycode, 'valicode': this.captcha, 'sourceType': 'web', 'channel': this.channel, 'blackBox': this.sessionStorage, "invitationCode": this.mobile }) }).then((res) => {
					res = JSON.parse(res);
					this.replaceFlag();
					this.detail = res.resMsg;
					if(res.resCode == "0000") {
						$('.alertBOX').css('display', 'block');
					} else {
						errorMessage(this.detail);
						this.captcha = '';
					}
				}, (error) => {
					res = JSON.parse(res);
						_this.detail = res.resMsg;
						errorMessage(_this.detail);

				})

			},


			//取消
			cancel: function() {
				$('.alertBOX').css('display', 'none');
			}
		},

		mounted() {
			$('.alertBOX').height($(window).height());

			//活动页面不需要头尾
			$('#nav').remove();
			$('footer').remove();


			this.$nextTick(() => {
			//加载图片
			$.post(this.webUrl + 'javaservice/webClient?functionId=channelInfo', { data: JSON.stringify({ 'channel': this.channel }) }).then((res) => {
				res = JSON.parse(res);
				this.replaceFlag();
				this.detail = res.resMsg;
				if(res.resCode == "0000") {
				    if(res.record.content && res.record.content !=''){  //背景图是否存在
              this.imgUrl = JSON.parse(res.record.content);
              this.bannerColour=this.imgUrl['bannerColour'];
              $('.activeRule').css({
                'background':'url(' + this.imgUrl['pic2'] + ')',
                'background-size':'cover'
              });
              $('.SecondConlumn').css({
                'background':'url(' + this.imgUrl['pic1'] + ')',
                'background-size':'cover'
              });
              $('.active').css('background',this.imgUrl['iconColour'] );
              $(".buttonColor").css('background',this.imgUrl['bannerColour'] );
              $(".downloadContent").css('background',this.imgUrl['backColour']);
              this.footer=this.imgUrl['pic3'];
            }
					this.bannerImg = res.record.wxPicLink;
					this.ruleContent = res.record.channelDesc;

				} else {
					errorMessage(this.detail);
				}

			}, (error) => {
				this.fetchCodeMsgN = true;
			})

				var input = "placeholder" in document.createElement('input');
				if(!input) {
					var placeholderArr = ["请输入手机号", "请输入验证码", "8~16位字母数字或符号的密码组合", "请输入6位数字交易密码"],
						oDiv = document.getElementById("bannerContentDiv"),
						oInput = oDiv.getElementsByTagName("input");
					$("#bannerContentDiv input").focus(function() {
						$(this).val("");
					}).blur(function() {
						var placeholderVal = $(this).attr("placeholder");
						$(this).val(placeholderVal);
					})
					for(i = 0; i < oInput.length; i++) {
						$("#bannerContentDiv input").eq(i).val(placeholderArr[i])
					}
				}

				var bodyWidth = document.body.clientWidth;
				if(bodyWidth < 798) {
					var liWidth = $("#advantageUl").width();
					$(".bannerContentDiv").width(liWidth)

				}

				var _this = this;
				(function() {
					window.sessionStorage.setItem("key", Math.random());
					var tokenId = window.sessionStorage.getItem("key");
					window._fmOpt = {
						partner: 'lianbi',
						appName: 'lianbi_web',
						token: tokenId
					};
					var cimg = new Image(1, 1);
					cimg.onload = function() {
						window._fmOpt.imgLoaded = true;
					};
					cimg.src = "https://fp.fraudmetrix.cn/fp/clear.png?partnerCode=lianbi&appName=lianbi_ddjr_web&tokenId=" + window._fmOpt.token;
					var fm = document.createElement('script');
					fm.type = 'text/javascript';
					fm.async = true;
					fm.src = ('https:' == document.location.protocol ? 'https://' : 'http://') + 'static.fraudmetrix.cn/fm.js?ver=0.1&t=' + (new Date().getTime() / 3600000).toFixed(0);
					var s = document.getElementsByTagName('script')[0];
					s.parentNode.insertBefore(fm, s);
				})();
				this.sessionStorage = window.sessionStorage.getItem("key");
			})
		}
	}
</script>

<style lang="scss" rel="stylesheet/scss">
a:hover {
    color: #333;
}
.active{
	background:#ff053b ;
}
.buttonColor{
	background:#ffe538
}
.SecondConlumn{
	background: url('./template2.jpg');
	background-size:cover;
}
	$height:44px;
	$width:100%;
	.downloadContent .bannerWrapper .bannerContent .contentRight .act03Tex a {
		color: #3997f4;
	}



	.input::-webkit-input-placeholder {
		/* WebKit browsers */
		color: #fb4148;
	}

	.input:-moz-placeholder {
		/* Mozilla Firefox 4 to 18 */
		color: #fb4148;
	}

	.input::-moz-placeholder {
		/* Mozilla Firefox 19+ */
		color: #fb4148;
	}

	.input:-ms-input-placeholder {
		/* Internet Explorer 10+ */
		color: #fb4148;
	}

	.colorG {
		color: #ffebee;
	}

	.downloadContent .bannerWrapper .bannerContent .contentRight div input {
		width: 244px
	}

	.downloadContent .bannerWrapper .bannerContent .contentRight .agreenment {
		text-align: left;
		position: relative;
		left: 0;
		top: -6px;
		color: #fff;
	}

	.downloadContent {
		width: 100%;
		text-align: left;
		.reminder {
			top: -76px;
			left: 0;
			width: 100%;
			height: 76px;
			line-height: 76px;
			background: rgba(83, 65, 123, 0.9);
			color: #fff;
			text-align: center;
			font-size: 24px;
			z-index: 1000;
			transition: all 0.4s cubic-bezier(0.445, 0.05, 0.55, 0.95);
		}
		.bannerWrapper {
			.bannerPic {
				position: relative;
				height: 800px;
				left: 50%;
				transform: translateX(-50%);
			}
			.bannerContent {
				width: 80%;
				margin: 0 auto;
				height: initial;
				.contentLeft {
					.leftText {
						padding-top: 133px;
					}
					.special {
						background: #2e679c;
					}
				}
				.contentRight {
					height: 100%;
					text-align: right;
					.register {
						display: block;
					}
					div {
						width: $width;
						height: $height;
						input {
							height: $height;
							line-height: $height;
							.verification {
								width: 50px;
								height: 44px;
								background-color: #ff063c;
							}
						}
					}
					.password {
						input {
							width: 244px;
						}
					}
					.phoneNum {
						input {
							width: 244px;
						}
					}
					.verifycode {
						input {
							width: 244px;
						}
						img {}
					}
					.captcha {
						overflow: visible;
						input {
							width: 180px;
							left: 25px;
						}
						span {
							width: 86px;
							right: -1px;
						}
					}
					a {
						width: $width;
						height: $height;
						line-height: $height;
						letter-spacing: 1px;
					}
					span {
						width: $width;
						height: $height;
						line-height: $height;
					}
				}
			}
		}
	}

	.column img {
		width: 100%;
		display: block;
		height: 100%;
	}

	.DownLoadApp {
		background: rgba(0, 0, 0, 0.5);
		position: fixed;
		bottom: 0px;
		height: 80px;
		width: 100%;
		&>div {
			width: 95%;
			margin: 0 auto;
			height: 50px;
			margin-top: 15px;
			img {
				height: 80%;
				margin-top: 5px;
			}
		}
	}
	/*基础类*/



	.overHidden {
		overflow: hidden;
	}

	.padding10P {
		padding: 10px;
	}



	.colore9f842 {
		color: #e9f842;
	}

	.textWhite {
		color: #FFFFFF;
	}

	.ML5P {
		margin-left: 5px;
	}

	.MT10P {
		margin-top: 10px;
	}

	.height40P {
		height: 40px;
	}

	.radius5 {
		border-radius: 5px;
	}

	.lineHeight40 {
		line-height: 40px;
	}

	.border1pxWhite {
		border: 1px solid #FFFFFF;
	}

	.bottom0 {
		bottom: 0;
	}

	.opacity7 {
		opacity: 0.7;
	}

	.colorWhiteImportant {
		color: #FFFFFF !important;
	}

	.height300 {
		height: 170px;
		overflow-y: scroll;
	}

	.lineHeight25px {
		line-height: 25px;
	}

	.height90P {
		height: 90%;
	}

	.MT2point5 {
		margin-top: 2.5px;
	}

	.colorBlack {
		color: #000000;
	}

	.color2d982e {
		color: #2d982e;
	}

	.borderRightGray {
		border-right: 1px solid gainsboro;
	}
	/*立即下载*/

	.RightNowDownLoad {
		height: 30px;
		line-height: 30px;
		margin-top: 10px;
		font-size: 14px;
		padding: 0 20px;
		border-radius: 12px;
		cursor: pointer;
		display: block;
		float: right;
		color: #FFFFFF;
		border: 1px solid rgba(255, 255, 255, 0.8);
	}
	/*活动规则*/

	.activeRule {
		background: url('./template3.jpg');//
		background-size:cover;
	}
	/*弹窗*/

	.alertBOX {
		z-index: 1000;
		background: rgba(0, 0, 0, 0.5);
		display: none;
	}

	.alertGoAPP {
		width: 70%;
		position: absolute;
		left: 15%;
		background: white;
		top: 50%;
		margin-top: -50px;
		p {
			width: 40%;
			color: #000000;
			text-align: center;
			margin: 0 auto;
			padding-top: 10px;
			line-height: 30px;
			padding-bottom: 10px;
			font-weight: bold;
			font-size: 16px;
		}
		div {
			overflow: hidden;
			border-top: 1px solid gainsboro;
			span {
				display: block;
				width: 50%;
				text-align: center;
				font-size: 16px;
				float: left;
				height: 40px;
				line-height: 40px;
				box-sizing: border-box;
				a {
					display: block;
					width: 100%;
					height: 100%;
				}
			}
		}
	}

	@media only screen and (max-width: 1280px) {
		#nav .navWrap .logo[data-v-088a0d5a] {
			margin-left: 50px;
		}
		#nav .navWrap .navContent[data-v-088a0d5a] {
			margin-right: 60px;
		}
		.downloadContent .bannerWrapper .bannerPic {
			height: 620px;
		}
		.downloadContent .bannerWrapper .bannerContent {
			width: 910px;
			height: 500px;
		}
	}

	@media only screen and (max-width: 798px) {
		/*基础类*/

		/*基础类*/
		.overHidden {
			overflow: hidden;
		}
		.padding10P {
			padding: 10px;
		}

		.colore9f842 {
			color: #e9f842;
		}
		.textWhite {
			color: #FFFFFF;
		}
		.ML5P {
			margin-left: 5px;
		}
		.MT10P {
			margin-top: 10px;
		}
		.height40P {
			height: 40px;
		}
		.radius5 {
			border-radius: 5px;
		}
		.lineHeight40 {
			line-height: 40px;
		}
		.border1pxWhite {
			border: 1px solid #FFFFFF;
		}
		.bottom0 {
			bottom: 0;
		}
		.opacity7 {
			opacity: 0.7;
		}
		.colorWhiteImportant {
			color: #FFFFFF !important;
		}
		.height300 {
			height: 170px;
			overflow-y: scroll;
		}
		.lineHeight25px {
			line-height: 25px;
		}
		.height90P {
			height: 90%;
		}
		.MT2point5 {
			margin-top: 2.5px;
		}
		.colorBlack {
			color: #000000;
		}
		/*立即下载*/
		.RightNowDownLoad {
			height: 30px;
			line-height: 30px;
			margin-top: 10px;
			font-size: 14px;
			padding: 0 20px;
			border-radius: 12px;
			cursor: pointer;
			display: block;
			float: right;
			color: #FFFFFF;
			border: 1px solid rgba(255, 255, 255, 0.8);
		}
		.borderRightGray {
			border-right: 1px solid gainsboro;
		}
		.color2d982e {
			color: #2d982e;
		}
		/*活动规则*/
		.activeRule {
			background: url('./template3.jpg');
			background-size:cover;
		}
		/*弹窗*/
		.alertBOX {
			z-index: 1000;
			background: rgba(0, 0, 0, 0.5);
			display: none;
		}
		.alertGoAPP {
			width: 70%;
			position: absolute;
			left: 15%;
			background: white;
			top: 50%;
			margin-top: -50px;
			p {
				width: 40%;
				color: #000000;
				text-align: center;
				margin: 0 auto;
				padding-top: 10px;
				padding-bottom: 10px;
				line-height: 30px;
			}
			div {
				overflow: hidden;
				border-top: 1px solid gainsboro;
				span {
					display: block;
					width: 50%;
					text-align: center;
					font-size: 16px;
					float: left;
					height: 40px;
					line-height: 40px;
					box-sizing: border-box;
					a {
						display: block;
						width: 100%;
						height: 100%;
					}
				}
			}
		}
		.downloadContent {
			display: block;
			.column {
				img {
					width: 100%;
					display: block;
				}
				#bannerContentDiv {
					width: 90%;
					margin: 0 auto;
					padding-top: 0px;
					height: initial;
					position: static;
					-webkit-transform: translateX(0);
					-moz-transform: translateX(0);
					-ms-transform: translateX(0);
					-o-transform: translateX(0);
					transform: translateX(0);
					padding-bottom: 50px;
				}
			}
		}
		.DownLoadApp {
			background: rgba(0, 0, 0, 0.5);
			position: fixed;
			bottom: 0px;
			height: 80px;
			width: 100%;
			&>div {
				width: 95%;
				margin: 0 auto;
				height: 50px;
				margin-top: 15px;
				img {
					height: 80%;
					margin-top: 5px;
				}
			}
		}
		.downloadContent .bannerWrapper .bannerContent .contentRight div input {
			height: 20px;
			margin-top: 12px;
			line-height: initial;
		}
		.downloadContent {
			padding-top: 0;
			background-size: 100%;
			height: 100%;
			width: 100%;
			// background: #f622bf;
			.reminder {
				height: 70px;
				line-height: 70px;
				font-size: 18px;
			}
			.bannerWrapper {
				.bannerPic {
					display: none;
				}
				.bannerContent {
					width: 90%;
					height: initial;
					position: static;
					-webkit-transform: translateX(0);
					-moz-transform: translateX(0);
					-ms-transform: translateX(0);
					-o-transform: translateX(0);
					transform: translateX(0);
					margin: 0 auto;
					.contentLeft {
						float: none;
						text-align: center;
						left: 50%;
						position: relative;
						-webkit-transform: translateX(-51%);
						-moz-transform: translateX(-51%);
						-ms-transform: translateX(-51%);
						-o-transform: translateX(-51%);
						transform: translateX(-51%);
						.highest {
							margin-top: 0;
							margin-bottom: 10px;
						}
						.leftText {
							margin-top: -100px;
							padding-top: 0;
							font-size: 18px;
							margin-bottom: 0;
						}
						.special {
							padding-top: 3px;
							padding-bottom: 3px;
							font-size: 12px;
						}
					}
					.contentRight {
						width: 90%;
						padding-top: 50px;
						float: none;
						text-align: center;
						margin: 0 auto;
						position: absolute;
						left: 50%;
						transform: translateX(-50%);
						.register {
							display: none;
						}
					}
				}
			}
		}
	}

	@media only screen and (max-width:320px) {

		.DownLoadApp {
			height:74px;
			&>div {
				margin-top:12px;
				div {
					p:nth-child(1) {
						font-size: 16px;
					}
				}
			}
			.RightNowDownLoad {
				padding: 0 15px;
				font-size: 12px;
			}
		}
	}
</style>
