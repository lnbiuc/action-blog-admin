<template>
    <div class="messageMainContent">
        <div class="tableContent">
            <el-table :data="data" border class="table" max-height="100vh - 100px">
                <el-table-column align="center" header-align="center" label="ID" prop="id" sortable width="100"/>
                <el-table-column header-align="center" label="昵称" prop="nickname" sortable/>
                <el-table-column header-align="center" label="头像" prop="avatar">
                    <template v-slot="scope">
                        <img :src="scope.row.avatar" alt="" style="height: 60px">
                    </template>
                </el-table-column>
                <el-table-column header-align="center" label="IP" prop="ip"/>
                <el-table-column header-align="center" label="内容" prop="content"/>
                <el-table-column header-align="center" label="时间" prop="time" sortable/>
                <el-table-column>
                    <template #default="scope">
                        <el-button size="small" @click="handleDelete(scope.$index, scope.row)">
                            Delete
                        </el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
        <div class="page">
            <el-pagination
                    :current-page="page.current"
                    :page-size="page.size"
                    :page-sizes="[5,10,20]"
                    :total="page.total"
                    layout="sizes, prev, pager, next, jumper,total"
                    style="width: 100%"
                    @size-change="size"
                    @current-change='getInfo'/>
        </div>
        <el-dialog
                v-model="dialog.delete"
                title="确认"
                width="30%">
            <p>确认删除留言：<br/>ID：
                <span style="font-weight: 800;color: #4285f4">{{ deleteForm.messageId }}</span><br/>
                昵称：
                <span style="font-weight: 800;color: #4285f4">{{ deleteForm.name }}</span><br/>
                内容：
                <span style="font-weight: 800;color: #4285f4">{{ deleteForm.content }}</span><br/>
            </p>
            <template #footer>
                <span class="dialog-footer">
                    <el-button @click="dialog.delete = false">取消</el-button>
                    <el-button type="primary" @click="dialog.delete = false;submitDelete()">确定</el-button>
                </span>
            </template>
        </el-dialog>
    </div>

</template>

<script>
import {deleteMessageById, getAllMessage} from '../../axios'
import {ElMessage} from "element-plus";

export default {
    name: "MessageAdmin",
    data() {
        return {
            page: {
                current: '',
                size: 10,
                total: 0
            },
            data: [],
            dialog: {
                delete: false
            },
            deleteForm: {
                messageId: '',
                content: '',
                name: ''
            }
        }
    },
    methods: {
        getInfo(current) {
            getAllMessage(current, this.page.size).then(res => {
                this.data = res.data.data.data
                this.page.current = res.data.data.current
                this.page.size = res.data.data.size
                this.page.total = res.data.data.total
            })
        },
        size(size) {
            this.page.size = size
            this.getInfo(this.page.current)
        },
        handleDelete(index, row) {
            this.deleteForm.messageId = row.id
            this.deleteForm.content = row.content
            this.deleteForm.name = row.nickname
            this.dialog.delete = true
        },
        submitDelete() {
            deleteMessageById(this.deleteForm.messageId).then(() => {
                ElMessage.success("删除成功")
                this.getInfo(this.page.current)
            })
        }
    },
    created() {
        this.getInfo(1)
    }
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
    height: calc(100vh - 100px);
}

.table {
    width: 100%;
}

.addForm {
    display: flex;
    flex-direction: row;
}
</style>