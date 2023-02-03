<template>
    <div class="truckMainContent">
        <div class="tableContent">
            <el-table :data="data" border class="table" max-height="100vh - 100px">
                <el-table-column align="center" header-align="center" label="id" prop="id" sortable/>
                <el-table-column :show-overflow-tooltip="true" align="center" header-align="center" label="模块"
                                 prop="module" width="200px"/>
                <el-table-column :show-overflow-tooltip="true" align="center" header-align="center" label="操作"
                                 prop="operation" width="250px"/>
                <el-table-column align="center" header-align="center" label="ip" prop="ip"/>
                <el-table-column align="center" header-align="center" label="ip属地" prop="ipLocation"/>
                <el-table-column align="center" header-align="center" label="浏览器" prop="brName"/>
                <el-table-column align="center" header-align="center" label="系统" prop="osName"/>
                <el-table-column align="center" header-align="center" label="请求时间" prop="time" sortable/>
                <el-table-column align="center" header-align="center" label="数据写入时间" prop="dumpTime"/>
            </el-table>
        </div>
        <div class="page">
            <el-pagination
                    :current-page="page.current"
                    :page-size="page.size"
                    :page-sizes="[10,20,50]"
                    :total="page.total"
                    layout="sizes, prev, pager, next, jumper,total"
                    style="width: 100%"
                    @size-change="size"
                    @current-change='getInfo'/>
            <div class="form">
                <el-form ref="baseForm" :model="ruleForm" :rules="rules" label-width="auto" status-icon>
                    <el-form-item label="ip" prop="ip">
                        <el-input v-model="ruleForm.ip"/>
                    </el-form-item>
                </el-form>
            </div>
            <el-button type="primary" @click="confirmOperation">清除自己的访问数据</el-button>
            <el-button type="danger" @click="collectionData">立即收集数据</el-button>
        </div>
    </div>
</template>

<script>
import {collectionInfo, deleteTruckLogByIp, getTruckLog} from "../../axios";
import {ElMessage} from "element-plus";

export default {
    name: "TruckLogAdmin",
    data() {
        return {
            page: {
                current: '',
                size: 20,
                total: 0
            },
            data: [],
            rules: {
                ip: [{required: true, message: '不可为空', trigger: 'change'}],
            },
            ruleForm: {
                ip: '',
            }
        }
    },
    methods: {
        getInfo(current) {
            getTruckLog(current, this.page.size).then(res => {
                this.data = res.data.data.data
                this.page.size = res.data.data.size
                this.page.current = res.data.data.current
                this.page.total = res.data.data.total
            })
        },
        collectionData() {
            collectionInfo().then(res => {
                ElMessage.success(res.data)
                this.getInfo(1)
            })
        },
        size(size) {
            this.page.size = size
            this.getInfo(this.page.current)
        },
        confirmOperation() {
            this.$refs.baseForm.validate((valid) => {
                if (valid) {
                    deleteTruckLogByIp(this.ruleForm.ip).then(res => {
                        ElMessage.success("删除了" + res.data.data + "条数据")
                        this.ruleForm.ip = ''
                        this.getInfo(1)
                    })
                }
            })
        },
        handleEdit(index, row) {
            let ip = row.ip
            window.open("https://www.ip138.com/iplookup.asp?ip=" + ip + "&action=2")
        }
    },
    created() {
        this.getInfo(1)
    }
}
</script>

<style scoped>
.truckMainContent {
    width: 100%;
    box-shadow: 1px 0 10px 1px rgb(10, 10, 10, 0.1);
    border-radius: 3px;
    display: flex;
    flex-direction: column;
}

.tableContent {
    height: calc(100vh - 100px);
}

.page {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
}
</style>