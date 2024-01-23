<template>
    <div>
        <div class="progress_box">
            <el-steps :space="350" :active=active finish-status="success" >
                <el-step title="确认账号"></el-step>
                <el-step title="重置密码"></el-step>
                <el-step title="重置成功"></el-step>
            </el-steps>
        </div>

        <div v-if="active == 1" class="reset_form">
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" class="demo-ruleForm">
                <el-form-item prop="account">
                    <el-input placeholder="请输入账号" type="text" v-model="ruleForm.account" autocomplete="off" class="input"></el-input>
                </el-form-item>
            </el-form>

            <div class="btn_box">
                <button class="btn next_step" @click="GoNext('ruleForm')">下一步</button>
                <button class="btn cancel" @click="GoBackLogin">取消</button>
            </div>
        </div>

        <div v-if="active == 2" class="reset_form">
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" class="demo-ruleForm">
                <el-form-item prop="password">
                    <el-input placeholder="请输入新密码" type="password" v-model="ruleForm.password" autocomplete="off" show-password class="input"></el-input>
                </el-form-item>
                <el-form-item prop="CheckPassword">
                    <el-input placeholder="请再次输入新密码" type="password" v-model="ruleForm.CheckPassword" autocomplete="off" show-password class="input"></el-input>
                </el-form-item>
            </el-form>

            <div class="btn_box">
                <button class="btn next_step" @click="GoNext('ruleForm')">下一步</button>
                <button class="btn cancel" @click="GoLast">上一步</button>
            </div>
        </div>

        <div v-if="active == 3" class="reset_form">
            <div class="btn_box">
                <button class="btn go_login" @click="GoBackLogin()">返回登陆</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        var validateCheckPassword = (rule, value, callback) => {
            if (value === '') {
                callback(new Error('请再次输入密码'));
                }
            else if (value !== this.ruleForm.password) {
                callback(new Error('两次输入密码不一致!'));
            }   
            else {
                callback();
            }
        };
        return {
            active: 1,
            ruleForm:{
                account:'',
                password: '',
                CheckPassword: '',
            },
            rules: {
                account: [
                    { required: true, message:'请输入账号', trigger: 'submit' }
                ],
                password: [
                    { required: true, message: '请输入新密码', trigger: 'submit' }
                ],
                CheckPassword: [
                    { validator: validateCheckPassword, trigger: 'submit' }
                ]
            },
        }
    },
    methods: {
        GoBackLogin() {
            this.$router.push('/login');
        },
        GoNext(formName) {
            if(this.active == 1){
                this.$refs.ruleForm.validateField('account', (valid) => {
                    if (valid) {
                        console.log('error');
                    } else {
                        this.active ++;
                    }
                });
            }
            else if(this.active == 2){
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.active ++;
                    } else {
                        console.log('error');
                        return false;
                    }
                });
            }
        },
        GoLast() {
            this.active --;
        },
    },
}
</script>

<style scoped>

/* 进度条位置 */
.progress_box{
    margin: 23vh 0 0 24vw;
}

/* 输入框 */
.reset_form{
    width: 330px;
    margin: 70px auto 0 auto;
}

/* 输入框宽度 */
.input{
    width: 330px;
}

/* 按钮位置 */
.btn_box{
    text-align: center;
    margin-top: 70px;
}

/* 控制整体按钮样式 */
.btn{
    color: white;
    border: none;
    margin: 0 10px 0 10px;
    width: 140px;
    height: 35px;
    border-radius: 5px;
    cursor: pointer;
}

/* 下一步按钮 */
.btn.next_step, .btn.go_login{
    background-color: dodgerblue;
}

button.btn.next_step:active, button.btn.go_login:active{
    background: rgb(24, 114, 204);
}

/* 取消按钮 */
.btn.cancel{
    background-color: darkgray;
}

button.btn.cancel:active{
    background: rgb(115, 115, 115);
}

.btn.go_login{
    color: white;
    border: none;
    margin-top: 100px;
    width: 140px;
    height: 35px;
    border-radius: 5px;
}
</style>