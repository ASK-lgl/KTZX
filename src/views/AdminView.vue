<template>
    <div style="height: 100vh;">
        <el-container style="height: 100%;">

            <el-header height="80px">
                <el-dropdown trigger="click" style="float: right;" @command="handleCommand">
                    <span class="el-dropdown-link">
                        <el-avatar :src="require('../../image/ask.jpg')" :size="56"
                            class="avatar top_avatar"></el-avatar>
                    </span>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item icon="el-icon-user" command="GoUserInfo">账户详情</el-dropdown-item>
                        <el-dropdown-item icon="el-icon-circle-close" command="logout">退出登录</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
            </el-header>

            <el-container>
                <el-aside width="260px">
                    <div class="user_panel">
                        <div class="column left">
                            <el-avatar :src="require('../../image/ask.jpg')" :size="70"
                                class="avatar left_avatar"></el-avatar>
                        </div>
                        <div class="column right">
                            <div class="user_info">
                                <span style="font-size: 18px; color: blue; font-family: '黑体';"><b>超级管理员</b></span>
                                <span id="current_time"></span>
                            </div>
                        </div>
                    </div>

                    <div class="user_function">
                        <!--动态绑定了那一栏菜单处于active状态，与main中的标签的编号是对应的-->
                        <el-menu :default-active=activeTabsValue class="el-menu-vertical_demo"
                            background-color="rgb(240, 248, 255)" text-color="#000000" active-text-color="rgb(0, 0, 255)">
                            <el-submenu index="submenu1" class="vertical_navi">
                                <template slot="title">
                                    <i class="el-icon-menu"></i>
                                    <span class="function_text">类型与角色管理</span>
                                </template>
                                <el-menu-item index="1" @click="checkTab('1', '类型管理')"
                                    class="vertical_navi">
                                    <i class="el-icon-s-operation"></i>
                                    类型管理
                                </el-menu-item>
                                <el-menu-item index="2" @click="checkTab('2', '角色管理')"
                                    class="vertical_navi">
                                    <i class="el-icon-s-custom"></i>
                                    角色管理
                                </el-menu-item>
                            </el-submenu>
                            <el-menu-item index="3" @click="checkTab('3', '类型授权')"
                                class="vertical_navi">
                                <i class="el-icon-s-tools"></i>
                                <span class="function_text">类型授权</span>
                            </el-menu-item>
                        </el-menu>
                    </div>
                </el-aside>

                <el-main>
                    <el-dialog title="增加类型" :visible.sync="add_typeFormVisible" width="500px">
                        <el-form :model="add_typeFormData">
                            <el-form-item label="角色类型" :label-width="formLabelWidth">
                                <el-input v-model="add_typeFormData.typeName"></el-input>
                            </el-form-item>
                            <el-form-item label="备注" :label-width="formLabelWidth">
                                <el-input v-model="add_typeFormData.notes"></el-input>
                            </el-form-item>
                        </el-form>
                        <div slot="footer" class="dialog-footer">
                            <el-button @click="close_addRoleTypeForm()">取 消</el-button>
                            <el-button type="primary" @click="confirmAddRoleType()">确 定</el-button>
                        </div>
                    </el-dialog>

                    <el-dialog title="设置权限" :visible.sync="changeTypeAuthorityFormVisible" width="500px">
                        <el-tree :data="treeData" node-key="id" ref="tree" highlight-current
                            :props="defaultProps" class="tree">
                            <span class="custom-tree-node" slot-scope="{ node }">
                                <span>
                                    <i v-if="node.data.children.length > 0" class="el-icon-folder-opened"></i>
                                    <i v-else-if="node.data.children.length == 0" class="el-icon-collection-tag"></i>
                                    {{ node.data.label }}
                                </span>
                                <el-checkbox v-if="node.checked !== undefined" v-model="node.checked"
                                    @change="handleCheckChange(node)"></el-checkbox>
                            </span>
                        </el-tree>
                        <div slot="footer" class="dialog-footer">
                            <el-button @click="close_changeRoleAuthorityForm()">取 消</el-button>
                            <el-button type="primary" @click="confirmChangeRoleAuthorityRow()">确 定</el-button>
                        </div>
                    </el-dialog>

                    <el-dialog title="增加角色" :visible.sync="add_roleFormVisible" width="500px">
                        <el-form :model="add_roleFormData">
                            <el-form-item label="角色名称" :label-width="formLabelWidth">
                                <el-input v-model="add_roleFormData.roleName"></el-input>
                            </el-form-item>
                            <el-form-item label="备注" :label-width="formLabelWidth">
                                <el-input v-model="add_roleFormData.notes"></el-input>
                            </el-form-item>
                        </el-form>
                        <div slot="footer" class="dialog-footer">
                            <el-button @click="close_addRoleForm()">取 消</el-button>
                            <el-button type="primary" @click="confirmAddRole(roleListIndex - '0')">确 定</el-button>
                        </div>
                    </el-dialog>

                    <el-dialog title="添加用户" :visible.sync="add_userFormVisible" width="800px">
                        <el-table :data="userList" style="width: 100%;"
                            :row-class-name="tableRowClassName" size="mini" border>
                            <el-table-column prop="name" label="姓名" width="100">
                            </el-table-column>
                            <el-table-column prop="notes" label="备注" width="">
                            </el-table-column>
                        </el-table>
                        <div slot="footer" class="dialog-footer">
                            <el-button @click="close_addUserForm()">取 消</el-button>
                            <el-button type="primary" @click="confirmAddUser()">确 定</el-button>
                        </div>
                    </el-dialog>
                    <!-- 把所有表单放在这是为了防止当前标签页后存在其他标签页时弹出表单后会蒙上一层蒙板的情况
                         给予了表单最高的堆叠层级 -->

                    <div v-if="activeTabsValue === '0'"
                        style="width: 100%; height: 100%; text-align: center; line-height: 80vh;">主页
                    </div>

                    <div v-if="activeTabsValue > 0" style="height: 100%;">
                        <div style="height: 40px;">
                            <el-tabs v-model=activeTabsValue type="card" closable @tab-remove="removeTab">
                                <!--这里的removeTab会把标签编号传进去，从1开始-->
                                <el-tab-pane :key="item.index" v-for="item in editableTabs" :label="item.name"
                                    :name="item.index">
                                </el-tab-pane>
                            </el-tabs>
                        </div>

                        <div v-if="activeTabsValue === '1'">
                                    <el-button type="primary" size="mini" @click="open_addRoleTypeForm()"
                                        style="margin: 10px 0 10px 10px;">
                                        增加类型
                                    </el-button>

                                    <el-table :data="roleMap" style="width: 100vw;"
                                        :row-class-name="tableRowClassName" size="mini" border>
                                        <el-table-column prop="name" label="类型" width="260">
                                        </el-table-column>
                                        <el-table-column prop="" label="角色数量" width="200">
                                            <template slot-scope="scope">
                                                {{ roleArrayByTypeNum[scope.$index].length }}
                                            </template>
                                        </el-table-column>
                                        <el-table-column prop="" label="首页" width="400">
                                            <template slot-scope="scope">
                                                {{ roleMap[scope.$index].homeName }}
                                            </template>
                                        </el-table-column>
                                        <el-table-column label="操作" width="">
                                            <template slot-scope="row">
                                                <div class="table_operate_column">
                                                    <el-button type="primary" size="small" @click="changeType(row.$index)"
                                                        style="margin-right: 10px;" icon="el-icon-edit" round>
                                                    </el-button>
                                                    <el-button type="danger" icon="el-icon-delete" round size="small" 
                                                        @click="confirmDeleteMessage()">
                                                    </el-button>
                                                </div>
                                            </template>
                                        </el-table-column>
                                    </el-table>
                        </div>

                        <div v-else-if="activeTabsValue === '2'" style="height: calc(100% - 40px);">
                            <div class="user_column role_list">
                                <div style="height: 50px; text-align: center; line-height: 50px; color: dodgerblue; user-select: none;
                                            background: rgb(240, 240, 240);">
                                    选择角色
                                </div>
                                <el-menu
                                    :default-active=roleListIndex
                                    class="role-list-el-menu"
                                    text-color="#000"
                                    active-text-color="dodgerblue">
                                    <el-menu-item v-for="(role, index) in roleMap"
                                        :key="role.typeNum"
                                        :index="index.toString()" 
                                        class="vertical_navi roleType">
                                        <el-popover
                                            ref="popoverRef"
                                            placement="right-start"
                                            width="700"
                                            trigger="click">
                                            <el-button type="primary" style="margin-bottom: 10px;" size="mini"
                                                @click="open_addRoleForm(index)">
                                                增加角色
                                            </el-button>
                                            <el-table
                                                :data="roleArrayByTypeNum[role.typeNum - 1]" 
                                                style="width: 100%"
                                                :row-class-name="tableRowClassName" 
                                                size="mini"
                                                border>
                                                <el-table-column prop="name" label="角色名称" width="200">
                                                </el-table-column>
                                                <el-table-column label="用户数量" width="70">
                                                    <template slot-scope="scope">
                                                        {{ scope.row.users.length }}
                                                    </template>
                                                </el-table-column>
                                                <el-table-column prop="notes" label="备注" width="200">
                                                </el-table-column>
                                                <el-table-column label="操作" width="">
                                                    <template slot-scope="scope">
                                                        <div class="table_operate_column">
                                                            <el-button type="primary" size="mini"
                                                                @click="showRoleUser(index, scope.$index)">
                                                                查看当前角色的所有用户
                                                            </el-button>
                                                        </div>
                                                    </template>
                                                </el-table-column>
                                            </el-table>
                                            <span slot="reference" 
                                                style="display: block; height: 100%; width: 100%;">
                                                <i class="el-icon-caret-right"></i>{{ role.name }}
                                            </span>
                                        </el-popover>
                                    </el-menu-item>
                                </el-menu>
                            </div>
                            <div class="user_column role_user" v-if="roleIndex >= 0">
                                <el-button type="primary" size="mini"
                                    style="margin: 10px 0 10px 10px;"
                                    @click="open_addUserForm()">
                                    添加用户
                                </el-button>
                                <el-table
                                    :data="roleArrayByTypeNum[roleListIndex - '0'][roleIndex].users" 
                                    style="width: 100%"
                                    :row-class-name="tableRowClassName" size="mini" border>
                                    <el-table-column label="姓名" width="260">
                                        <template slot-scope="scope">
                                            {{ userList[scope.row - 1].name }}
                                        </template>
                                    </el-table-column>
                                    <el-table-column label="备注" width="">
                                        <template slot-scope="scope">
                                            {{ userList[scope.row - 1].notes }}
                                        </template>
                                    </el-table-column>
                                </el-table>
                            </div>
                        </div>

                        <div v-if="activeTabsValue === '3'">
                            <el-table :data="roleMap" style="width: 100vw"
                                :row-class-name="tableRowClassName" size="mini" border>
                                <el-table-column prop="name" label="类型" width="330">
                                </el-table-column>
                                <el-table-column prop="notes" label="备注" width="470">
                                </el-table-column>
                                <el-table-column>
                                    <div class="table_operate_column">
                                        <el-button type="primary" size="small" @click="open_changeTypeAuthorityForm()">
                                            修改类型权限
                                        </el-button>
                                    </div>
                                </el-table-column>
                            </el-table>
                        </div>

                        <div v-if="activeTabsValue === '1'" class="pagination_box">
                            <el-pagination class="pagination" 
                                @size-change="typeManagement_handleSizeChange"
                                @current-change="typeManagement_handleCurrentChange" 
                                :page-sizes="[7, 10, 15, 20]" 
                                :page-size="typeManagementPaginationdata.rowSize"
                                layout="total, sizes, prev, pager, next, jumper" 
                                :total="typeManagementPaginationdata.total"
                                :current-page.sync="typeManagementPaginationdata.currentPage" 
                                :key="typeManagementPaginationdata.total" 
                                background>
                                <!-- 加一个key是为了点击返回键后刷新这个分页组件的显示当前是第几个分页，值是随便赋的，但千万不要赋成currentPage!!!
                                    虽然不知道为什么，可能为了强制刷新组件 -->
                            </el-pagination>
                        </div>

                        <div v-if="activeTabsValue === '3'" class="pagination_box">
                            <el-pagination class="pagination" 
                                @size-change="typeAuthorize_handleSizeChange"
                                @current-change="typeAuthorize_handleCurrentChange" 
                                :page-sizes="[7, 10, 15, 20]" 
                                :page-size="typeAuthorizePaginationdata.rowSize"
                                layout="total, sizes, prev, pager, next, jumper" 
                                :total="typeAuthorizePaginationdata.total"
                                :current-page.sync="typeAuthorizePaginationdata.currentPage" 
                                :key="typeAuthorizePaginationdata.total" 
                                background>
                            </el-pagination>
                        </div>
                    </div>
                </el-main>
            </el-container>
        </el-container>
    </div>
</template>

<script>
let timer = null;
export default {
    data() {
        return {
            activeTabsValue: '0',   // 当前哪个标签页处于active状态，默认是0
            editableTabs: [],   // 存每个标签页的数据
            tabSumIndex: 1,     // 记录有多少个标签页，编号从1开始

            // 接收后端传来的数据
            AdminPagesData: [
                {
                    name: '教务处主任',
                    typeNum: 3,
                    users: [1, 2],
                    notes: '教务处',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '教务处首页',
                },
                {
                    name: '信息学院负责人',
                    typeNum: 4,
                    users: [],
                    notes: '',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '学院负责人首页',
                },
                { 
                    name: '计算机专业负责人',
                    typeNum: 6, 
                    users: [3, 4, 5],
                    notes: '',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '',
                },
                {
                    name: '电控学院负责人',
                    typeNum: 4,
                    users: [],
                    notes: '',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '',
                },
                {
                    name: '自动化专业负责人',
                    typeNum: 6,
                    users: [6],
                    notes: '',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '专业负责人首页',
                },
                {
                    name: '经管学院负责人',
                    typeNum: 4,
                    users: [],
                    notes: '',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '',
                },
                {
                    name: '计算机专业本科生',
                    typeNum: 10,
                    users: [8],
                    notes: '',
                    isAdmin: false,
                    homeUrl: '',
                    homeName: '学生首页',
                },
            ],
            roleArrayByTypeNum: [],    //存储同一角色类型的不同角色名称

            // 不同角色类型的映射
            roleMap: [
                {
                    typeNum: 1,
                    name: '超级管理员',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 2,
                    name: '教学秘书',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 3,
                    name: '教务处',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 4,
                    name: '学院负责人',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 5,
                    name: '系主任',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 6,
                    name: '专业负责人',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 7,
                    name: '课程负责人',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 8,
                    name: '任课教师',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 9,
                    name: '助教',
                    notes: '',
                    homeName: '',
                },
                {
                    typeNum: 10,
                    name: '学生',
                    notes: '',
                    homeName: '',
                },
            ],

            userList: [
                {
                    id: 1,
                    userName: '吕德华',
                    name: '吕德华',
                    notes: '',
                },
                {
                    id: 2,
                    userName: '徐主任',
                    name: '徐主任',
                    notes: '',
                },
                {
                    id: 3,
                    userName: '李源',
                    name: '李源',
                    notes: '',
                },
                {
                    id: 4,
                    userName: '孙晶',
                    name: '孙晶',
                    notes: '',
                },
                {
                    id: 5,
                    userName: '方英兰',
                    name: '方英兰',
                    notes: '',
                },
                {
                    id: 6,
                    userName: '闫昊',
                    name: '闫昊',
                    notes: '',
                },
                {
                    id: 7,
                    userName: '张顺心',
                    name: '张顺心',
                    notes: '',
                },
                {
                    id: 8,
                    userName: '张心玮',
                    name: '张心玮',
                    notes: '',
                },
                {
                    id: 9,
                    userName: '张心瑞',
                    name: '张心瑞',
                    notes: '',
                },
            ],

            formLabelWidth: '80px',// 表单中输入框前提示字的长度
            
            add_typeFormVisible: false,
            add_typeFormData: {     // 存储添加类型表单的数据
                typeName: '',
                notes: '',
            },

            //分类管理的分页组件数据
            typeManagementPaginationdata: {
                total: 100,
                currentPage: 1,
                rowSize: 7,
            },
            
            changeTypeAuthorityFormVisible: false,
            // 存储树形控件的数据
            treeData: [
                {
                    label: '一级 1',
                    children: [
                        {
                            label: '二级 1-1',
                            children: [
                                {
                                    label: '三级 1-1-1',
                                    children: []
                                }, 
                                {
                                    label: '三级 1-1-2',
                                    children: []
                                },
                            ],
                        },
                    ],
                }, 
                {
                    label: '一级 2',
                    children: [
                            {
                            label: '二级 2-1',
                            children: [],
                        }, 
                        {
                            label: '二级 2-2',
                            children: [],
                        },
                    ],
                }, 
                {
                    label: '一级 3',
                    children: [
                        {
                            label: '二级 3-1',
                            children: [],
                        }, 
                        {
                            label: '二级 3-2',
                            children: [],
                        },
                    ],
                },
                {
                    label: '一级 1',
                    children: [
                        {
                            label: '二级 1-1',
                            children: [
                                {
                                    label: '三级 1-1-1',
                                    children: [],
                                }, 
                                {
                                    label: '三级 1-1-2',
                                    children: [],
                                },
                            ],
                        },
                    ],
                }, 
                {
                    label: '一级 2',
                    children: [
                        {
                            label: '二级 2-1',
                            children: [],
                        }, 
                        {
                            label: '二级 2-2',
                            children: [],
                        },
                    ],
                }, 
                {
                    label: '一级 3',
                    children: [
                        {
                            label: '二级 3-1',
                            children: [],
                        }, 
                        {
                            label: '二级 3-2',
                            children: [],
                        },
                    ],
                }
            ],

            defaultProps: {
                children: 'children',
                label: 'label'
            },

            //分类授权的分页组件数据
            typeAuthorizePaginationdata: {
                total: 80,
                currentPage: 1,
                rowSize: 7,
            },

            roleListIndex: '', // 在角色用户管理中显示哪一类角色的用户
            roleIndex: -1, // 当前角色的数组下标，对应第几个角色名称，从0开始，-1是默认不显示用户表格

            add_roleFormVisible: false,
            add_roleFormData: {
                roleName: '',
                notes: '',
            },

            add_userFormVisible: false,
        }
    },
    methods: {
        // 检查当前标签页是不是被打开，若没有则创建
        checkTab(tabIndex, tabName) {
            let flag = false;
            for (let i = 0; i < this.editableTabs.length; i++) {
                if (this.editableTabs[i].index === tabIndex) {
                    flag = true;
                    this.activeTabsValue = tabIndex;
                    break;
                }
            }
            if (!flag) {
                let newTabIndex = tabIndex;
                this.editableTabs.push({
                    name: tabName,
                    index: newTabIndex,
                });
                this.activeTabsValue = newTabIndex;
                this.tabSumIndex++;
            }
        },
        removeTab(tabIndex) {
            let tabs = this.editableTabs;
            let activeIndex = this.activeTabsValue;
            if (activeIndex === tabIndex) {
                tabs.forEach((tab, index) => {
                    if (tab.index === tabIndex) {
                        let nextTab = tabs[index + 1] || tabs[index - 1];
                        if (nextTab) {
                            activeIndex = nextTab.index;
                        }
                        else activeIndex = '0';
                    }
                });
            }

            this.activeTabsValue = activeIndex;
            this.editableTabs = tabs.filter(tab => tab.index !== tabIndex);
            this.tabSumIndex--;
        },

        // 账户管理
        handleCommand(command) {
            if (command == 'GoUserInfo') {
                this.$router.push('/userInfo');
            }
            else if (command == 'logout') {
                this.$router.push('/login');
            }
        },

        // 显示系统时间
        updateTime() {
            var currentTime = new Date();
            document.getElementById('current_time').innerHTML = currentTime.toLocaleTimeString();
        },

        // 改变指定表格指定行的背景色
        tableRowClassName({ rowIndex }) {
            if (rowIndex % 2 == 0) {
                return 'changeColor-row';
            } else if (rowIndex % 2 == 1) {
                return '';
            }
            return '';
        },
        
        // 清除添加类型表单的数据
        clearAddRoleTypeForm(){
            this.add_typeFormData.typeName = this.add_typeFormData.notes = '';
        },
        open_addRoleTypeForm(){
            this.clearAddRoleTypeForm();
            this.add_typeFormVisible = true;
        },
        confirmAddRoleType(){
            this.add_typeFormVisible = false;
            let RM_formerLength = this.roleMap.length;  //存未添加新类型时的roleMap数组长度

                this.roleMap.push({
                    typeNum: RM_formerLength + 1,    //类型编号从1开始
                    name: this.add_typeFormData.typeName,
                    notes: this.add_typeFormData.notes,
                });
                //这里用RM_formerLength做索引是因为类型的对应编号从0开始
                this.roleArrayByTypeNum[RM_formerLength] = [];

            this.roleArrayByTypeNum.splice(this.roleArrayByTypeNum.length, 0);    //这里是为了告诉vue该刷新table组件了

            this.clearAddRoleTypeForm();
            this.$message({
                type: 'success',
                message: '添加成功!'
            });
        },
        close_addRoleTypeForm() {
            this.add_typeFormVisible = false;
            this.clearAddRoleTypeForm();
        },

        confirmDeleteMessage() {
            this.$confirm('此操作将永久删除该类型及其下所有角色, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$message({
                    type: 'success',
                    message: '删除成功'
                });
            }).catch(() => {});
        },

        // 修改类型
        changeType(rowIndex) {
            this.$prompt('请输入新的类型', '修改类型', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
            }).then(({ value }) => {
                this.roleMap[rowIndex].name = value;
                this.$message({
                    type: 'success',
                    message: '修改成功',
                });
            }).catch(() => {});
        },

        
        //类型管理的分页组件函数
        typeManagement_handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
            this.typeManagementPaginationdata.rowSize = val;
        },
        typeManagement_handleCurrentChange(val) {
            console.log(`当前页: ${val}`);
            this.typeManagementPaginationdata.currentPage = val;
        },


        //分类授权的分页组件函数
        typeAuthorize_handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
            this.typeAuthorizePaginationdata.rowSize = val;
        },
        typeAuthorize_handleCurrentChange(val) {
            console.log(`当前页: ${val}`);
            this.typeAuthorizePaginationdata.currentPage = val;
        },

        // 修改分类权限
        open_changeTypeAuthorityForm() {
            this.changeTypeAuthorityFormVisible = true;
        },

        // 树形组件的勾选检查函数
        handleCheckChange(node) {
            let status = node.checked;
            this.checkAllChildren(node, status);
            this.checkAllParents(node, status);
        },
        checkAllChildren(node, status) {
            node.setChecked(status);    //设置当前节点为选中状态
            node.childNodes.forEach((child) => {    //如果当前节点有子节点，那么就递归调用当前函数把所有子节点都勾选上
                this.checkAllChildren(child, status);
            });
        },
        checkAllParents(node, status) {
            if (node.parent) {  //如果当前节点存在父节点
                let parentStatus = node.parent.childNodes.every((child) => {    //遍历此节点的所有兄弟节点，查看是否都为选中状态
                    return child.checked === status;
                });
                if (parentStatus) {     //如果兄弟节点都被选中，那么此节点的父节点也设置为被选中
                    node.parent.setChecked(status);
                    this.checkAllParents(node.parent, status);  //递归调用当前函数，判断当前节点的其他祖先节点是否可以被设为选中
                }
            }
        },

        // 将后端传来的数据按角色类型分类存放
        classifyRolesBytypeNum() {
            for(let type of this.roleMap){
                this.roleArrayByTypeNum[type.typeNum - 1] = [];
            }
            for(let role of this.AdminPagesData){
                this.roleArrayByTypeNum[role.typeNum - 1].push(role);
                if(this.roleMap[role.typeNum - 1].homeName === '') this.roleMap[role.typeNum - 1].homeName = role.homeName;
            }
        },

        showRoleUser(index, roleIndex){
            this.roleListIndex = index + '';    //指向当前是哪个角色类型，对应roleMap的索引
            
            //指向当前要展示哪个角色类型分类的数据，对应roleArrayByTypeNum数组的某一类角色的第roleIndex个角色名称
            this.roleIndex = roleIndex;
            this.$refs.popoverRef[index].doClose();
        },

        clearAddRoleForm(){
            this.add_roleFormData.roleName = this.add_roleFormData.notes = '';
        },
        open_addRoleForm(roleListIndex){
            this.clearAddRoleForm();

            //这是为了防止用户点击添加角色的类型不是当前的显示的类型，防止roleArrayByTypeNum数组第二维的下标越界
            if(this.roleListIndex != roleListIndex) this.roleIndex = -1;
            this.roleListIndex = roleListIndex + '';
            this.add_roleFormVisible = true;
        },
        confirmAddRole(typeNum){
            this.roleArrayByTypeNum[typeNum].push({
                name: this.add_roleFormData.roleName,
                typeNum: typeNum + 1,
                users: [],
                notes: this.add_roleFormData.notes,
            })

            this.roleArrayByTypeNum[typeNum].splice(this.roleArrayByTypeNum[typeNum].length, 0);
            this.add_roleFormVisible = false;
            this.clearAddRoleForm();
        },
        close_addRoleForm(){
            this.add_roleFormVisible = false;
            this.clearAddRoleForm();
        },

        // 添加角色用户表单
        open_addUserForm(){
            this.add_userFormVisible = true;
        },
        close_addUserForm(){
            this.add_userFormVisible = false;
        },
        confirmAddUser(){
            this.add_userFormVisible = false;
        },
    },
    mounted() {
        timer = setInterval(this.updateTime, 1000); // 在组件挂载后开始定时器
        this.classifyRolesBytypeNum();
    },
    beforeDestroy() {
        clearInterval(timer); // 在组件销毁前清除定时器
    },
}
</script>

<style scoped>

.el-header {
    background-color: cornflowerblue;
    color: #333;
    text-align: center;
    padding: 12px 60px 0 60px;
}

.el-aside {
    background-color: aliceblue;
    color: #333;
}

.el-main {
    color: #666;
    padding: 0;
}

.avatar.top_avatar {
    cursor: pointer;
    background-color: cornflowerblue;
}

/* 用户信息仪表盘 */
.user_panel {
    border-bottom: 1px solid gainsboro;
    height: 150px;
}

/* 用户仪表盘内部结构 */
.column {
    float: left;
    height: 100%;
}

.column.left {
    width: 45%;
    text-align: center;
}

.column.right {
    width: 55%;
}

.avatar.left_avatar {
    margin-top: 40px;
    background-color: aliceblue;
}

/* 用户仪表盘内用户信息 */
.column.right .user_info {
    height: 100px;
    margin-top: 25px;
    line-height: 50px;
}

/* 角色功能字体 */
.function_text {
    font-size: 16px;
}

.el-table{
    color: black;
}

/* 改变表格行颜色 */
.el-table >>> .changeColor-row {
    background-color: aliceblue;
}

.table_operate_column{
    display: flex; 
    justify-content: left;
}

/* 分页组件位置 */
.pagination_box{
    position: absolute; 
    bottom: 15px; 
    right: 100px;
}

.custom-tree-node {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
}

.custom-tree-node .el-checkbox {
    margin-right: 50px;
}


/* 树形控件样式 */
.tree{
    min-height: 50vh;
    max-height: 50vh;
    overflow-y: scroll;
}

/* 导航栏样式 */
.vertical_navi{
    border-top: 1px solid rgb(225, 225, 225);
}

/* 重置角色用户管理的菜单栏样式，去掉内边距并减少高度 */
.vertical_navi.roleType{
    padding: 0 !important;
    height: 40px !important;
    line-height: 40px !important;
}

.user_column{
    height: 100%;
    float: left;
}

.user_column.role_list{
    width: 20%;
    border-right: 1px solid rgb(225, 225, 225);
    height: calc(100vh - 120px);
    overflow-y: auto;
}

/* 角色用户管理菜单的高度和超出部分控制 */
.role-list-el-menu{
    height: calc(100vh - 170px);
    overflow-y: auto;
}

.user_column.role_user{
    width: 79.8%;
}
</style>