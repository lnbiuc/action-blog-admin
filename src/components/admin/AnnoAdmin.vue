<template>
<div class="annoMainContent">
    <div class="tableContent">
        <el-table :data="annos" border class="table" max-height="100vh - 100px">
            <el-table-column align="center" header-align="center" label="公告ID" prop="id" sortable/>
            <el-table-column align="center" header-align="center" label="公告内容" prop="content" sortable/>
            <el-table-column align="center" header-align="center" label="公告时间" prop="time" sortable/>
            <el-table-column align="center" header-align="center" width="150px">
                <template #header>
                    <div class="addForm">
                        <el-button @click="dialog = true">发布公告</el-button>
                    </div>
                </template>
                <template #default="scope">
                    <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">Delete
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
    </div>
</div>
    <el-dialog
            v-model="dialog"
            title="发布公告"
            width="30%">
        <el-form>
            <el-form-item label="内容">
                <el-input v-model="publishForm.content">
                </el-input>
            </el-form-item>
            <el-form-item label="时间">
                <el-date-picker
                        v-model="publishForm.time"
                        type="datetime"
                        placeholder="公告发布时间"
                        format="YYYY/MM/DD HH:mm:ss"
                        value-format="YYYY-MM-DD hh:mm:ss"
                />
            </el-form-item>
        </el-form>
        <p>确认时间为：<span style="color: #4285f4">{{publishForm.time}}</span></p>
        <template #footer>
                <span class="dialog-footer">
                    <el-button @click="dialog = false">取消</el-button>
                    <el-button type="primary" @click="dialog = false;publish()">确定</el-button>
                </span>
        </template>
    </el-dialog>
</template>

<script>
import {getAnnoInfo} from "../../axios";

export default {
    name: "AnnoAdmin",
    data() {
        return {
            annos: [],
            dialog: false,
            publishForm: {
                content: '',
                time: ''
            }
        }
    },
    methods: {
        getAnnoInfo() {
            getAnnoInfo().then(res => {
                this.annos = res.data.data
            })
        },
        handleDelete(index,row) {

        },
        publish() {

        }
    },
    created() {
        this.getAnnoInfo()
    }
}
</script>

<style scoped>
.tagMainContent {
    width: 100%;
    box-shadow: 1px 0 10px 1px rgb(10, 10, 10, 0.1);
    border-radius: 3px;
    display: flex;
    flex-direction: column;
}

.tableContent {
    height: calc(100vh - 100px);
}
</style>