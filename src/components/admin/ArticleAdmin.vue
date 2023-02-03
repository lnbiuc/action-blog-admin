<template>
    <div class="articleMainContent">
        <div class="tableContent">

            <el-table :data="data" border class="table" max-height="100vh - 100px">
                <el-table-column align="center" fixed header-align="center" label="ID" prop="article.articleId"
                                 sortable/>
                <el-table-column align="center" header-align="center" label="标题" prop="article.title" sortable/>
                <el-table-column align="center" :show-overflow-tooltip="true" header-align="center" label="简介" prop="article.introduction" sortable/>
                <el-table-column align="center" :show-overflow-tooltip="true" header-align="center" label="发表时间" prop="article.releaseTime" sortable/>
                <el-table-column align="center" :show-overflow-tooltip="true" header-align="center" label="更新时间" prop="article.updateTime" sortable/>
                <el-table-column align="center" header-align="center" label="置顶" prop="article.setTop" sortable>
                    <template #default="scope">
                        <el-tag v-if="scope.row.article.setTop" type="success">{{ scope.row.article.setTop }}</el-tag>
                        <el-tag v-else type="danger">{{ scope.row.article.setTop }}</el-tag>
                    </template>
                </el-table-column>
                <el-table-column align="center" header-align="center" label="删除" prop="article.deleted" sortable>
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
                <el-table-column label="标签" prop="tagNames" width="150">
                    <template v-slot="scope">
                        <el-tag v-for="tag in scope.row.tagNames" :key="tag.tagId" size="mini" style="margin: 2px 2px">
                            {{ tag.tagName }}
                        </el-tag>
                    </template>
                </el-table-column>
                <el-table-column align="center" header-align="center" label="分类" prop="filingName.filingName" sortable/>
                <el-table-column align="center" fixed="right" header-align="center" label="操作">
                    <template #default="scope">
                        <el-button size="small" type="success" @click="handleEdit(scope.$index, scope.row)">编辑
                        </el-button>
                        <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除
                        </el-button>
                        <el-button size="small" type="primary" @click="handleSetTop(scope.$index, scope.row)">置顶
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
                    layout="prev, pager, next, jumper,total"
                    style="width: 100%"
                    @current-change='getInfo'/>
        </div>

        <el-dialog v-model="dialog.delete" title="确认" width="30%">
            <p>确认删除文章，标题：
                <span style="font-weight: 800;color: #4285f4">{{ deleteForm.title }}</span>
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

import {articleSetTop, deleteArticle, getArticleAdmin} from "../../axios";
import {ElMessage} from "element-plus";

export default {
    name: "ArticleAdmin",
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
                articleId: '',
                title: ''
            }
        }
    },
    methods: {
        getInfo(current) {
            getArticleAdmin(current, this.page.size).then(res => {
                this.data = res.data.data.paramsList
                this.page.size = res.data.data.pageSize
                this.page.total = res.data.data.total
                this.page.current = res.data.data.currentPage
            })
        },
        submitDelete() {
            deleteArticle(this.deleteForm.articleId).then(() => {
                ElMessage.success("删除成功")
                this.getInfo(this.page.current, this.page.size)
            })
        },
        handleDelete(index, row) {
            this.deleteForm.articleId = row.article.articleId
            this.deleteForm.title = row.article.title
            this.dialog.delete = true
        },
        handleEdit(index, row) {
            this.$router.push({name: 'editArticle', params: {'articleId': row.article.articleId}})
        },
        handleSetTop(index, row) {
            articleSetTop(row.article.articleId).then(() => {
                this.getInfo(this.page.current, this.page.size)
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