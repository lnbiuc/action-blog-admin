<template>
    <div>
        <v-md-editor v-model="form.content"
                     :disabled-menus="[]"
                     height="85vh"
                     @upload-image="handleUploadImage" />
        <div class="btn">
            <el-button style="width:300px;"
                       type="primary"
                       @click="send">提交</el-button>
        </div>
    </div>
</template>
<script>
import { ElMessage } from 'element-plus'
import {getAboutMeInfo, sendAboutMeInfo, upLoadImg} from '../../axios'
export default {
    name: 'AboutMeAdmin',
    data() {
        return {
            form: {
                content: '',
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
        getInfo() {
            getAboutMeInfo().then((res) => {
                this.form.content = res.data.data.value
            })
        },
        send() {
            if (!this.form.content) {
                ElMessage.error('数据为空')
                return
            }
            sendAboutMeInfo(this.form.content).then((res) => {
                ElMessage.success('修改成功')
                this.getInfo()
            })
        },
    },
    created() {
        this.getInfo()
    },
}
</script>
<style scoped>
.btn {
    display: flex;
    flex-direction: row;
    justify-content: end;
}
</style>