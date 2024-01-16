<template>
    <div>
        <el-form label-width="70px" class="loginForm">
            <el-form-item prop="username" class="input_box">
                <el-input placeholder="请输入用户名" type="text" autocomplete="off"
                    class="input">
                </el-input>
            </el-form-item>
            <el-form-item prop="pwd" class="input_box">
                <el-input placeholder="请输入密码" type="password" autocomplete="off"
                    show-password class="input">
                </el-input>
            </el-form-item>
        </el-form>
</div>
</template>

<script>
export default {
    data() {
        return {
            showRoleClassification: false,
        }
    },
    methods: {
        close(){
            alert(this.showRoleClassification);
        }
    }
}
</script>

<style scope>

.custom-tree-node {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
}

.custom-tree-node .el-checkbox {
    margin-right: 50px;
}
</style>