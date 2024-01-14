<template>
    <div>
    <el-popover
        placement="right-start"
        ref="popoverRef1"
        trigger="click"
        >
        <el-button @click="() => this.$refs.popoverRef1.doClose()">关闭</el-button>
        <span slot="reference">
            <i class="el-icon-caret-right"></i>aaa
        </span>
    </el-popover>
    <el-popover
        placement="right-start"
        ref="popoverRef2"
        trigger="click"
        >
        <el-button @click="() => this.$refs.popoverRef2.doClose()">关闭</el-button>
        <span slot="reference">
            <i class="el-icon-caret-right"></i>aaa
        </span>
    </el-popover>
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