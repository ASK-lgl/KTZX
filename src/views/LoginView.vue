<template>
	<div style="height: 100vh;">
		<div class="loginBox">
			<div style="width: 100%; height: 10vh; display: flex; flex-direction: row; justify-content: center;">
				<button class="catelog left" id="1" @click="changeCatelog">学生登陆</button>
				<button class="catelog right" id="2" @click="changeCatelog">教师登录</button>
			</div>
			<div style="margin: 30px 0 30px 0; text-align: center;">
				<el-radio v-model="loginway" label='1' @change="changeLoginway">密码登录</el-radio>
				<el-radio v-model="loginway" label='2' @change="changeLoginway">短信登陆</el-radio>
			</div>
			<div v-show="loginFormType == 1">
				<el-form :model="loginForm" :rules="pwdRules" ref="loginForm" class="loginForm">
					<el-form-item prop="username" class="input_box">
						<el-input placeholder="请输入用户名" type="text" v-model="loginForm.username" autocomplete="off"
							class="input">
						</el-input>
					</el-form-item>
					<el-form-item prop="pwd" class="input_box">
						<el-input placeholder="请输入密码" type="password" v-model="loginForm.pwd" autocomplete="off"
							show-password class="input">
						</el-input>
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

			<div v-show="loginFormType == 2">
				<el-form :model="loginFormByPhone" :rules="phoneRules" ref="loginFormByPhone" class="loginForm">
					<el-form-item prop="phone" class="input_box">
						<el-input placeholder="请输入手机号" type="text" v-model="loginFormByPhone.phone" autocomplete="off"
							class="input">
						</el-input>
					</el-form-item>
					<el-form-item prop="code" class="input_box">
						<el-input placeholder="请输入验证码" type="text" v-model="loginFormByPhone.code" autocomplete="off"
							class="input">
						</el-input>
					</el-form-item>
				</el-form>
				<div class="submit_box">
					<button class="submit" @click="login('loginFormByPhone')">登录</button>
				</div>
				<div class="forgetPassword">
					<router-link to="/register" class="router-link register"></router-link>
					<router-link to="/forgetPassword" class="router-link forget">忘记密码</router-link>
				</div>
			</div>
		</div>

		<el-dialog title="选择角色" :visible.sync="chooseRoleFormVisible" width="500px">
			<el-radio style="display: block; margin-bottom: 30px;" v-model="roleNum" v-for="(item, index) in rolesList.simpleRoleList"
			:key="item.roleid" :label="index" @change="chooseRoles">
				{{ item.rolename }}
			</el-radio>
			<div slot="footer" class="dialog-footer">
				<el-button @click="close_chooseRoleForm()">取 消</el-button>
				<el-button type="primary" @click="confirmChooseRole()">确 定</el-button>
			</div>
		</el-dialog>
	</div>
</template>

<script>
// import axois from 'axios'
export default {
	data() {
		return {
			loginForm: {
				username: '',
				pwd: '',
				catelog: null,
				loginway: 1
			},
			loginFormByPhone: {
				phone: '',
				code: '',
				catelog: null,
				loginway: 2
			},
			pwdRules: {
				username: [{ required: true, message: "请输入用户名", trigger: "submit" },],
				pwd: [{ required: true, message: "请输入密码", trigger: "submit" },],
			},
			phoneRules: {
				phone: [
					{ required: true, message: "请输入手机号", trigger: "submit" },
					{ pattern: /\d{11}$/, message: "请输入正确的手机号", trigger: "blur" }
				],
				code: [
					{ required: true, message: "请输入验证码", trigger: "submit" },
				],
			},
			user: {		// 接收后端数据
				userid: '',
				catelog: '',
				rolename: '',
				roleid: '',
				homeurl: ''
			},
			rolesList: {
				userid: '',
				catelog: '',
				simpleRoleList: [
					//测试数据
					{
						rolename: "超级管理员",
						roleid: "1",
						homeurl: "homes/superadminhome"
					},
					{
						rolename: "教务处",
						roleid: "3",
						homeurl: "homes/eduhome"
					}
					/************************** */
				]
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

			loginway: '1',
			loginFormType: 1,

			chooseRoleFormVisible: false,
			roleNum: '',
		};
	},
	methods: {
		clearLoginForm() {
			this.loginForm.username = this.loginForm.pwd = '';
			this.loginForm.catelog = this.loginForm.loginway = -1;
		},
		login(formName) {
			this.$refs[formName].validate((valid) => {
				if (valid) {
					//仅供测试
					if(this.loginForm.username === '超级管理员' && this.loginForm.pwd === '1'){
						if(this.loginForm.catelog === '2'){
							this.chooseRoleFormVisible = true;
							this.roleNum = '';
						}
						else if(this.loginForm.catelog !== '2'){
							this.$message({
								message: '无可用角色',
								type: 'error',
								duration: 800
							});
						}
					}
					else{
						this.$message({
							message: '账号或密码错误',
							type: 'error',
							duration: 800
						});
					}
					/*************************************** */

					// 这里暂时先不启用异步请求
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
					// 		if(result.data.data.rolescount > 0){
					// 			this.roleNum = '';
					// 			this.rolesList = result.data.data;
					// 			this.chooseRoleFormVisible = true;
					// 		}
					// 		else if(result.data.data.rolescount == 0){
					// 			this.user = result.data.data;
					// 			this.$router.push('homes/studenthome');
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
		changeCatelog(event){
			let id = event.target.id;
			this.loginForm.catelog = this.loginFormByPhone.catelog = id;
			let buttons = document.getElementsByClassName('catelog');
			for(let button of buttons){
				button.style.borderBottom = 'none';
			}
			document.getElementById(id).style.borderBottom = '3px solid blue'; 	
		},
		changeLoginway(value){
			this.loginForm.loginway = this.loginFormByPhone.loginway = this.loginFormType = value;
		},

		chooseRoles(value){
			this.user.userid = this.rolesList.userid;
			this.user.catelog = this.rolesList.catelog;
			this.user = this.rolesList.simpleRoleList[value];
		},
		close_chooseRoleForm(){
			this.chooseRoleFormVisible = false;
		},
		confirmChooseRole(){
			this.$router.push(this.user.homeurl);
		},

		prompt() {
			this.$notify({
				title: '目前可用的账号',
				message: '教师登录√<br>密码登录√<br>用户名: 超级管理员<br>密码: 1',
				position: 'top-left',
				duration: 0,
				showClose: false,
				dangerouslyUseHTMLString: true,
			});
		},
	},
	mounted() {
		this.prompt();
	},
	beforeDestroy() {
		this.$notify.closeAll();
	}
};
</script>

<style scoped>
/* 登录框 */
.loginBox {
	width: 350px;
	height: 420px;
	border: 1px solid black;
	box-sizing: border-box;
	background: white;
	border-radius: 7px;
	position: absolute;
	top: 50%;
	transform: translateY(-50%);
	margin-right: 100px;
    right: 0;
}

/* 登录表单 */
.loginForm {
	margin-top: 0px;
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
	text-align: center;
	margin-bottom: 40px;
}

/* 设置输入框宽度 */
.input {
	width: 270px;
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

.catelog{
	height: 100%;
	line-height: 12vh;
	text-align: center;
	margin: 0 20px 0 20px;
	background-color: transparent;
	border: none;
	cursor: pointer;
}

.catelog.left{
	width: 30%;
	border-bottom: 3px solid blue;
}
/* .catelog.left:active{
	border-bottom: 3px solid blue;
} */

.catelog.right{
	width: 30%;
}
/* .catelog.right:focus{
	border-bottom: 3px solid blue;
} */
</style>