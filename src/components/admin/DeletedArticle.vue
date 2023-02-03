<template>
    <div class="articleMainContent">
        <div class="tableContent">

            <el-table :data="data" border class="table" max-height="100vh - 100px">
                <el-table-column align="center" fixed header-align="center" label="ID" prop="article.articleId"
                                 sortable/>
                <el-table-column align="center" header-align="center" label="标题" prop="article.title" sortable/>
                <el-table-column align="center" header-align="center" label="简介" prop="article.introduction" sortable/>
                <el-table-column align="center" header-align="center" label="发表时间" prop="article.releaseTime"
                                 sortable
                                 width="170px"/>
                <el-table-column align="center" header-align="center" label="更新时间" prop="article.updateTime"
                                 sortable
                                 width="170px"/>
                <el-table-column align="center" header-align="center" label="置顶" prop="article.setTop" sortable
                                 width="80px">
                    <template #default="scope">
                        <el-tag v-if="scope.row.article.setTop" type="success">{{ scope.row.article.setTop }}</el-tag>
                        <el-tag v-else type="danger">{{ scope.row.article.setTop }}</el-tag>
                    </template>
                </el-table-column>
                <el-table-column align="center" header-align="center" label="删除" prop="article.deleted" sortable
                                 width="80px">
                    <template #default="scope">
                        <el-tag v-if="scope.row.article.deleted" type="success">{{ scope.row.article.deleted }}</el-tag>
                        <el-tag v-else type="danger">{{ scope.row.article.deleted }}</el-tag>
                    </template>
                </el-table-column>
                <el-table-column align="center" header-align="center" label="阅读量" prop="article.visitsCount" sortable/>
                <el-table-column align="center" header-align="center" label="点赞量" prop="article.likeCount" sortable/>
                <el-table-column label="背景图" prop="article.bgImg" width="120">
                    <template v-slot="scope">
                        <img :src="scope.row.article.bgImg" alt="" style="height: 60px">
                    </template>
                </el-table-column>
                <el-table-column align="center" fixed="right" header-align="center" label="操作">
                    <template #default="scope">
                        <el-button size="small" type="success" @click="handleRecover(scope.$index, scope.row)">恢复
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
    </div>
    <div>
        <el-dialog v-model="recoverDialog" title="确认恢复" width="30%">
            <p>确认恢复文章：&nbsp;&nbsp;<span style="color: #4285f4;font-weight: 800">{{ title }}</span></p>
            <span>注：不会恢复文章分类和标签信息</span>
            <template #footer>
                <span class="dialog-footer">
                    <el-button @click="recoverDialog = false">取消</el-button>
                    <el-button type="primary" @click="recoverDialog = false;submitRecover">确定</el-button>
                </span>
            </template>
        </el-dialog>
    </div>
</template>

<script>
import {getDeletedArticle, recoverArticle} from "../../axios";
import {ElMessage} from "element-plus";

export default {
    name: "DeletedArticle",
    data() {
        return {
            page: {
                current: '',
                size: 10,
                total: 0
            },
            data: [],
            recoverDialog: false,
            articleId: '',
            title: ''
        }
    },
    methods: {
        getInfo(current) {
            getDeletedArticle(current, this.page.size).then(res => {
                this.data = res.data.data.articles
                this.page.size = res.data.data.size
                this.page.total = res.data.data.total
                this.page.current = res.data.data.current
            })
            console.log("getInfo")
        },
        size(size) {
            this.page.size = size
            this.getInfo(this.page.current)
        },
        handleRecover(index, row) {
            this.recoverDialog = true
            this.title = row.article.title
            this.articleId = row.article.articleId
        },
        submitRecover() {
            recoverArticle(this.articleId).then(res => {
                if (res.data.code === 200) {
                    ElMessage.success("恢复成功")
                    this.getInfo(this.page.current)
                } else {
                    ElMessage.error("恢复失败")
                }
            })
        }
    },
    created() {
        this.getInfo(1, this.page.size)
    }
}
</script>

<style scoped>
.articleMainContent {
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