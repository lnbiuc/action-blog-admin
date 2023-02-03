<template>
    <div class="mainContent">
        <p>index working on it</p>
    </div>
</template>

<script>
import {autoSave, getLastEdit, upLoadImg} from "../axios";
import {ElMessage} from "element-plus";

export default {
    name: "Index",
    data() {
        return {
            form: {
                articleId: '',
                content: ''
            },
            article: {},
            time: null
        }
    },
    methods: {
        // handleUploadImage(event, insertImage, files) {
        //     upLoadImg(files).then(res => {
        //         insertImage({
        //             url: res.data.data,
        //             width: 'auto',
        //             height: 'auto',
        //             desc: 'image'
        //         })
        //     })
        // },
        // load() {
        //     getLastEdit().then(res => {
        //         this.article = res.data.data;
        //         this.form.articleId = res.data.data.articleId
        //         this.form.content = res.data.data.content
        //     })
        // },
        // save() {
        //     autoSave(this.form.articleId, this.form.content).then(() => {
        //         ElMessage.success("保存成功")
        //     })
        // },
    },
    mounted() {
        // this.time = setInterval(() => {
        //     this.save()
        // }, 180000)
    },
    created() {
        // this.load()
    },
    // beforeDestroy() {
    //     clearInterval(this.time);
    //     this.time = null;
    // },
    // destroy() {
    //     clearInterval(this.time);
    //     this.time = null;
    // },
    // beforeRouteLeave() {
    //     clearInterval(this.time);
    //     this.time = null;
    // },
    beforeRouteEnter(to, from, next) {
        if (!window.localStorage.getItem('token'))
            return next({name: 'login'})
        next();
    }
}
</script>

<style scoped>
.mainContent {
    display: flex;
    flex-direction: column;
    padding: 20px 20px;
}

.edit {
    overflow: hidden;
}
</style>