<template>
	<div>
		<div class="loginform">
			<el-form :model="loginForm" :rules="rules" ref="loginForm" label-width="70px" class="demo-loginForm">
				<el-form-item prop="loginname" class="input_box">
					<el-input placeholder="请输入用户名" type="text" v-model="loginForm.loginname" autocomplete="off"
						class="input"></el-input>
				</el-form-item>
				<el-form-item prop="pwd" class="input_box">
					<el-input placeholder="请输入密码" type="password" v-model="loginForm.pwd" autocomplete="off"
						show-password class="input"></el-input>
				</el-form-item>
			</el-form>

			<div class="submit_box">
				<button class="submit" @click="login('loginForm')">登录</button>
			</div>

			<div class="forgetPassword">
				<router-link to="/register" class="router-link register"></router-link>
				<router-link to="/forgetPassword" class="router-link forget">忘记密码</router-link>
			</div>
		</div>
	</div>
</template>

<script>
// import axois from 'axios'
export default {
	data() {
		var validateloginname = (rule, value, callback) => {
			if (value === "") {
				callback(new Error("请输入用户名"));
			} else {
				callback();
			}
		};
		var validatePassword = (rule, value, callback) => {
			if (value === "") {
				callback(new Error("请输入密码"));
			} else {
				callback();
			}
		};
		return {
			loginForm: {
				loginname: '',
				pwd: '',
				catelog: -1,
				loginway: -1
			},
			rules: {
				loginname: [{ validator: validateloginname, trigger: "blur" }],
				pwd: [{ validator: validatePassword, trigger: "blur" }],
			},
			user: {		// 接收后端数据
				userid: 1,
				rolename: '超级管理员',
				roleid: '1',
				homeurl: 'homes/adminhome'
			},
			userData: {
				id: 1,
				obsId: 1,
				username: '超级管理员',
				loginname: '超级管理员',
				mobile: '',
				pwd: '1',
				status: 1,
				remark: '',
				catelog: 2,
				headImage: ''
			},
		};
	},
	methods: {
		clearLoginForm() {
			this.loginForm.loginname = this.loginForm.pwd = '';
			this.loginForm.catelog = this.loginForm.loginway = -1;
		},
		login(formName) {
			this.$refs[formName].validate((valid) => {
				if (valid) {
					this.$router.push(this.user.homeurl);
					// 这里暂时先不启用登录用户名检验
					// axois.post(`http://127.0.0.1:4523/m1/3914762-0-default/login`, this.loginForm).then((result) => {
					// 	if(result.data.code === 400){
					// 		this.$message({
					// 			message: result.data.msg,
					// 			type: 'error',
					// 			duration: 800,
					// 		});
					// 	}
					// 	else if(result.data.code === 404){
					// 		this.$message({
					// 			message: result.data.msg,
					// 			type: 'error',
					// 			duration: 800,
					// 		});
					// 	}
					// 	else if(result.data.code === 200){
					// 		if(result.data.data.length > 1){
					// 			this.choseRoleFormVisible = true;
					// 		}
					// 		else if(result.data.data.length == 1){
					// 			this.user = result.data.data[0];
					// 			this.$router.push(this.user.homeurl);
					// 		}
					// 	}
					// });

					return true;
				} 
				else {
					console.log("error submit!!");
					return false;
				}
			});
		},
	},
};
</script>

<style scoped>
/* 登录框 */
.loginform {
	width: 350px;
	height: 420px;
	border: 1px solid black;
	box-sizing: border-box;
	margin: 18vh auto 0 auto;
	background: white;
	border-radius: 7px;
}

/* 登录表单 */
.demo-loginForm {
	/* padding-right: 90px; */
	margin-top: 150px;
}

.submit_box {
	text-align: center;
}

/* 登录按钮 */
.submit_box .submit {
	background-color: dodgerblue;
	color: white;
	border: none;
	margin: 0 auto 0 auto;
	width: 140px;
	height: 35px;
	border-radius: 5px;
}

/* 按钮点击变化 */
button.submit:active {
	background: rgb(24, 114, 204);
}

/* 每一个输入框的位置调整 */
.input_box {
	padding-bottom: 20px;
}

/* 设置输入框宽度 */
.input {
	width: 207px;
}

/* 忘记密码 */
.forgetPassword {
	font-size: 13px;
	margin-top: 30px;
	padding: 0 50px 0 50px;
	display: flex;
	justify-content: space-between;
}

.router-link.forget:link,
.router-link.forget:visited {
	color: darkgray;
	text-decoration: none;
}

.router-link.forget:hover {
	color: red;
}

.router-link.register:link,
.router-link.register:visited {
	color: limegreen;
	text-decoration: none;
}

.router-link.register:hover {
	color: red;
}
</style>