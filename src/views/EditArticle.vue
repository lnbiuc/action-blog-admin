<template>
    <div class="publishContent">
        <div class="form">
            <div class="titleForm">
                <el-form ref="baseForm"
                         :model="ruleForm"
                         :rules="rules">
                    <el-form-item label="名称"
                                  prop="title">
                        <el-input v-model="ruleForm.title" />
                    </el-form-item>
                    <el-form-item label="简介"
                                  prop="introduction">
                        <el-input v-model="ruleForm.introduction" />
                    </el-form-item>
                    <el-form-item label="分类"
                                  prop="filing">
                        <el-select v-model="ruleForm.filingName"
                                   allow-create
                                   class="m-2"
                                   clearable
                                   default-first-option
                                   filterable
                                   placeholder="选择分类">
                            <el-option v-for="filing in info.filingNames"
                                       :key="filing.id"
                                       :label="filing.filingName"
                                       :value="filing.filingName" />
                        </el-select>
                    </el-form-item>
                    <el-form-item label="标签"
                                  prop="tag">
                        <el-select v-model="ruleForm.tag"
                                   allow-create
                                   class="m-2"
                                   clearable
                                   default-first-option
                                   filterable
                                   multiple
                                   multiple-limit="4"
                                   placeholder="选择分类">
                            <el-option v-for="tag in info.tags"
                                       :key="tag.tagId"
                                       :label="tag.tagName"
                                       :value="tag.tagName" />
                        </el-select>
                    </el-form-item>
                    <el-form-item label="背景"
                                  prop="img">
                        <el-input v-model="ruleForm.bgImg" />
                    </el-form-item>
                </el-form>
            </div>
            <div class="rightForm">
                <div class="imgUp">
                    <el-upload :on-error="this.upError"
                               :on-success="this.upSuccess"
                               action='https://beyondhorizon.top/api/admin/upload/img'
                               auto-upload
                               class="upload-demo"
                               :headers="token"
                               drag
                               enctype="multipart/form-data"
                               name="img">
                        <el-icon class="el-icon--upload">
                            <upload-filled />
                        </el-icon>
                        <div class="el-upload__text">
                            Drop file here or <em>click to upload</em>
                        </div>
                        <template #tip>
                            <div class="el-upload__tip">
                                max image size = 10mb
                            </div>
                        </template>
                    </el-upload>
                </div>
            </div>
        </div>
        <div class="submit">
            <el-form>
                <el-form-item>
                    <el-button style="width: 300px;float: right"
                               type="primary"
                               @click="confirmOperation()">发 布</el-button>
                </el-form-item>
            </el-form>
        </div>
        <div id="edit">
            <v-md-editor v-model="ruleForm.content"
                         :disabled-menus="[]"
                         height="100vh"
                         :tab-size="4"
                         :default-show-toc="true"
                         :autofocus="true"
                         @save="confirmOperation"
                         @upload-image="handleUploadImage">
            </v-md-editor>
        </div>
    </div>
</template>

<script>
import { UploadFilled } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'
import {
    editArticle,
    getArticleById,
    getFiling,
    getTag,
    upLoadImg,
} from '../axios'

export default {
    name: 'EditArticle',
    components: { UploadFilled },
    data() {
        return {
            rules: {
                title: [
                    { required: true, message: '不可为空', trigger: 'change' },
                ],
                introduction: [
                    { required: true, message: '不可为空', trigger: 'change' },
                ],
            },
            ruleForm: {
                articleId: '',
                title: '',
                introduction: '',
                content: '',
                filingName: '',
                tag: [],
                bgImg: '',
            },
            info: {
                filingNames: [],
                tags: [],
            },
            token: {
                token: '',
            },
        }
    },
    methods: {
        handleUploadImage(event, insertImage, files) {
            upLoadImg(files).then((res) => {
                insertImage({
                    url: res.data.data,
                    desc: 'image',
                })
            })
        },
        confirmOperation() {
            this.$refs.baseForm.validate((valid) => {
                if (valid) {
                    editArticle(this.ruleForm).then(() => {
                        ElMessage.success('修改成功')
                    })
                }
            })
        },
        getFiling() {
            getFiling().then((res) => {
                this.info.filingNames = res.data.data
            })
        },
        getTag() {
            getTag().then((res) => {
                this.info.tags = res.data.data
            })
        },
        upSuccess(response, file, fileList) {
            if (response.code === 200) {
                ElMessage.success('上传成功')
                this.ruleForm.bgImg = response.data
            }
        },
        upError(err, file, fileList) {
            let msg = '上传失败' + err
            ElMessage.error('上传失败' + msg)
        },
        loadArticle() {
            getArticleById(this.ruleForm.articleId).then((res) => {
                this.ruleForm.title = res.data.data.title
                this.ruleForm.introduction = res.data.data.introduction
                this.ruleForm.content = res.data.data.content
                let tags = []
                for (let tag of res.data.data.tagNames) {
                    tags.push(tag.tagName)
                }
                this.ruleForm.tag = tags
                this.ruleForm.filingName = res.data.data.filingName.filingName
                this.ruleForm.bgImg = res.data.data.bgImg
            })
        },
    },
    created() {
        this.ruleForm.articleId = this.$route.params.articleId
        this.loadArticle()
        this.getFiling()
        this.getTag()
        this.token.token = window.localStorage.getItem('token')
    },
}
</script>

<style scoped>
.publishContent {
    display: flex;
    flex-direction: column;
    width: 100%;
}

.titleForm {
    display: flex;
    flex-direction: column;
    flex: 1;
    padding: 15px 15px;
}

.rightForm {
    display: flex;
    flex-direction: row;
    flex: 1;
    padding: 15px 15px;
    width: 100vw;
}

.selectForm {
    flex: 1;
}

.imgUp {
    flex: 1;

    display: flex;
    align-items: center;
    justify-content: center;
}

.form {
    display: flex;
    flex-direction: row;
}

.upload-demo {
    width: 100%;
}

.submit {
    margin: 0 auto;
}
</style>