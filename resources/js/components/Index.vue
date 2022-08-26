<template>
    <div class="w-25">
        <input v-model="title" type="text" class="mb-3 form-control" placeholder="title">
        <div ref="dropzone" class="mb-3 btn d-block p-5 bg-dark text-center text-light">
            upload
        </div>
        <div class="mb-3">
            <vue-editor v-model="content" />
        </div>
        <input @click.prevent="store" type="submit" class="mb-3 btn btn-primary" value="add">

        <div class="mt-5">
            <div v-if="post">
                <h4>{{ post.title }}</h4>
                <div v-for="image in post.images">
                    <img :src="image.preview_url" class="mb-3">
                    <img :src="image.url">
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Dropzone from 'dropzone'
import { VueEditor } from"vue2-editor"
export default {
    name: "Index",

    data() {
        return {
            dropzone: null,
            title: '',
            post: [],
            content: null
        }
    },
    components: {
      VueEditor
    },
    mounted() {
        this.dropzone = new Dropzone(this.$refs.dropzone, {
            url: "/api/posts",
            autoProcessQueue: false,
            addRemoveLinks: true,
        })
        this.getPost()
    },
    methods: {
        store() {
            const data = new FormData()
            const files = this.dropzone.getAcceptedFiles()
            files.forEach(file => {
                data.append('images[]', file)
                this.dropzone.removeFile(file)

            })
            data.append('title', this.title)
            axios.post('/api/posts', data)
            .then(res => {
                this.getPost()
            })
            this.title = ''
        },
        getPost() {
            axios.get('/api/posts')
                .then(res => {
                    this.post = res.data.data
                })
        }
    }

}
</script>

<style scoped>

</style>
