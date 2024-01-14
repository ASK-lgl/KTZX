<template>
	<div>
		<div class="loginform">
			<el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="70px" class="demo-ruleForm">
				<el-form-item prop="account" class="input_box">
					<el-input placeholder="请输入账号" type="text" v-model="ruleForm.account" autocomplete="off"
						class="input"></el-input>
				</el-form-item>
				<el-form-item prop="password" class="input_box">
					<el-input placeholder="请输入密码" type="password" v-model="ruleForm.password" autocomplete="off"
						show-password class="input"></el-input>
				</el-form-item>
			</el-form>

			<div class="submit_box">
				<button class="submit" @click="login('ruleForm')">登录</button>
			</div>

			<div class="forgetPassword">
				<router-link to="/register" class="router-link register"></router-link>
				<router-link to="/forgetPassword" class="router-link forget">忘记密码</router-link>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		var validateAccount = (rule, value, callback) => {
			if (value === "") {
				callback(new Error("请输入账号"));
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
			ruleForm: {
				account: "",
				password: "",
			},
			rules: {
				account: [{ validator: validateAccount, trigger: "blur" }],
				password: [{ validator: validatePassword, trigger: "blur" }],
			},
		};
	},
	methods: {
		login(formName) {
			this.$refs[formName].validate((valid) => {
				if (valid) {
					this.$router.push("/admin");
					// 后期在这里加账户验证代码
				} else {
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
.demo-ruleForm {
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