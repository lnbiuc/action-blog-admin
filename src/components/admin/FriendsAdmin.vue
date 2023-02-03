<template>
    <div class="messageMainContent">
        <div class="tableContent">
            <el-table :data="friends" border class="table" max-height="100vh - 100px">
                <el-table-column align="center" header-align="center" label="ID" prop="id" sortable/>
                <el-table-column align="center" header-align="center" label="名称" prop="title" sortable/>
                <el-table-column align="center" header-align="center" label="描述" prop="description" sortable />
                <el-table-column align="center" header-align="center" label="首页地址" prop="indexUrl" sortable />
                <el-table-column align="center" header-align="center" label="头像地址" prop="avatarUrl" sortable/>
                <el-table-column align="center" header-align="center" label="状态" prop="status" sortable/>
                <el-table-column align="center" header-align="center" label="添加时间" prop="addTime" sortable/>
                <el-table-column  align="center" header-align="center" width="150px">
                    <template #header>
                        <el-button size="small" @click="dialog.addDialog = true;this.flushFrom()">ADD NEW</el-button>
                    </template>
                    <template #default="scope">
                        <el-button size="small" type="primary"  @click="handleEdit(scope.$index, scope.row);dialog.editDialog = true">Edit</el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">Delete
                        </el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
    </div>
    <el-dialog
                v-model="dialog.addDialog"
                title="添加友链"
                width="40%">
            <el-form>
                <el-form-item label="标题">
                    <el-input v-model="form.title"/>
                </el-form-item>
                <el-form-item label="描述">
                    <el-input v-model="form.description"/>
                </el-form-item>
                <el-form-item label="首页">
                    <el-input v-model="form.indexUrl"/>
                </el-form-item>
                <el-form-item label="头像">
                    <el-input v-model="form.avatarUrl"/>
                </el-form-item>
            </el-form>
            <template #footer>
                <span class="dialog-footer">
                    <el-button @click="dialog.addDialog = false">取消</el-button>
                    <el-button type="primary" @click="dialog.addDialog = false;submitAdd()">确定</el-button>
                </span>
            </template>
        </el-dialog>
        <el-dialog
                v-model="dialog.editDialog"
                title="修改友链"
                width="40%">
            <el-form>
                <el-form-item label="标题">
                    <el-input v-model="form.title"/>
                </el-form-item>
                <el-form-item label="描述">
                    <el-input v-model="form.description"/>
                </el-form-item>
                <el-form-item label="首页">
                    <el-input v-model="form.indexUrl"/>
                </el-form-item>
                <el-form-item label="头像">
                    <el-input v-model="form.avatarUrl"/>
                </el-form-item>
                <el-form-item label="状态">
                    <el-input v-model="form.status"/>
                </el-form-item>
            </el-form>
            <template #footer>
                <span class="dialog-footer">
                    <el-button @click="dialog.editDialog = false">取消</el-button>
                    <el-button type="primary" @click="dialog.editDialog = false;submitEdit()">确定</el-button>
                </span>
            </template>
        </el-dialog>
</template>

<script>
import { ElMessage } from 'element-plus'

import { addFriend, getFriends,editFriends, deleteFriend } from '../../axios'
export default {
    name: 'FriendsAdmin',
    data() {
        return {
            friends: [],
            dialog: {
                addDialog: false,
                editDialog: false,
            },
            form: {
                id: 0,
			    title: '',
			    description: '',
			    indexUrl: '',
                avatarUrl: '',
			    status: '',
            }
        }
    },
    methods: {
        getInfo() {
            getFriends().then((res) => {
                this.friends = res.data.data
            })
        },
        handleEdit(index, row) {
            this.form.id = row.id
            this.form.title = row.title
            this.form.description = row.description
            this.form.indexUrl = row.indexUrl
            this.form.avatarUrl = row.avatarUrl
            this.form.status = row.status

        },
        handleDelete (index, row) {
            deleteFriend(row.id).then(res => {
                if (res.data.code === 200) {
                    ElMessage({
                        message: '删除成功',
                        type: 'success'
                    })
                    this.getInfo()
                }else {
                        ElMessage({
                            message: '删除失败',
                            type: 'error'
                        })
                    }
            })
        },
        submitEdit () {
            if(this.form.id !== '' && this.form.title !== '' && this.form.description !== '' && this.form.indexUrl !== '' && this.form.avatarUrl !== '' && this.form.status !== '') {
                editFriends(this.form.id,this.form.title,this.form.description,this.form.indexUrl,this.form.avatarUrl,this.status).then((res) => {
                    if(res.data.code === 200) {
                        ElMessage({
                            message: '修改成功',
                            type: 'success'
                        })
                        this.flushFrom()
                        this.getInfo()
                    } else {
                        ElMessage({
                            message: '修改失败',
                            type: 'error'
                        })
                        this.flushFrom()
                    }
                })
            } else {
                ElMessage({
                    message: '请填写完整信息',
                    type: 'error'
                })
                this.flushFrom()
            }
        },
        submitAdd () {
            if(this.form.title !== '' && this.form.description !== '' && this.form.indexUrl !== '' && this.form.avatarUrl !== '') {
                addFriend(this.form.title,this.form.description,this.form.indexUrl,this.form.avatarUrl).then((res) => {
                    if(res.data.code === 200) {
                        ElMessage({
                            message: '添加成功',
                            type: 'success'
                        })
                        this.flushFrom()
                        this.getInfo()
                    } else {
                        ElMessage({
                            message: '添加失败',
                            type: 'error'
                        })
                        this.flushFrom()
                    }
                })
            } else {
                ElMessage({
                    message: '请填写完整信息',
                    type: 'error'
                })
                this.flushFrom()
            }
        },
        flushFrom () {
            this.form.title = ''
            this.form.description = ''
            this.form.indexUrl = ''
            this.form.avatarUrl = ''
            this.form.status = ''
        }
    },
    created() {
        this.getInfo()
    },
}
</script>

<style scoped>
.messageMainContent {
    width: 100%;
    box-shadow: 1px 0 10px 1px rgb(10, 10, 10, 0.1);
    border-radius: 3px;
    display: flex;
    flex-direction: column;
}

.tableContent {
    height: calc(100vh - 60px);
}

.table {
    width: 100%;
}
</style>