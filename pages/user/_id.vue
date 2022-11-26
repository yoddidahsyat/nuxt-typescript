<template>
    <b-container class="pt-5">
        <b-row>
            <b-col>
                <b-link to="/">&lt back</b-link>
            </b-col>
        </b-row>
        <b-row>
            <b-col>
                <h1>Detail User</h1>
                <div v-if="userLoading" class="mt-3"><b-spinner></b-spinner> Loading...</div>
                <div v-else class="mt-3">
                    <p>Nama : {{user.name}}</p>
                    <p>Gender : {{user.gender}}</p>
                    <p>Email : {{user.email}}</p>
                </div>
            </b-col>
        </b-row>
        <b-row>
            <b-col>
                <h5>Daftar Post</h5>
            </b-col>
            <b-col>
                <b-button variant="primary" class="float-right mb-3" @click="addPost">
                    Buat Post
                </b-button>
            </b-col>
        </b-row>
        <b-row>
                <b-table striped hover :items="posts" :fields="fields" :busy="postLoading" show-empty>
                    <template #table-busy>
                        <div class="text-center my-2">
                            <b-spinner class="align-middle"></b-spinner>
                            <strong>Loading...</strong>
                        </div>
                    </template>
                    <template #cell(action)="row">
                        <b-button size="sm" variant="danger" @click="deletePost(row.item.id)">
                            Delete
                        </b-button>
                    </template>
                </b-table>
            </b-col>
        </b-row>
        <PostModal :user-id="user.id" v-on:reloadPosts="getPosts" />
    </b-container>
</template>

<script lang="ts">
    import Vue from 'vue';
    
    export default Vue.extend({
        name: 'DetailPage',
        data() {
            return {
                user: {
                    id: Number,
                    email: String,
                    name: String,
                    gender: String,
                    status: String
                },
                userLoading: true,
                postLoading: true,
                posts: [],
                fields: ['title', 'body', 'action']
            }
        },
        mounted() {
            const userId = this.$route.params.id
            this.getUser(userId)
            this.getPosts(userId)
        },
        methods: {
            addPost(){
                this.$bvModal.show('post-modal')
            },
            
            async getUser(userId: Number|String) {
                this.userLoading = true
                try {
                    const response = await this.$axios.get(`/users/${userId}`)
                    // console.log(response.data)
                    this.user = response.data
                    this.userLoading = false
                }catch(error: any) {
                    this.userLoading = false
                    console.log(error.response.data)
                }
            },
            
            async getPosts(userId: Number|String) {
                this.postLoading = true
                try {
                    const response = await this.$axios.get(`/users/${userId}/posts`)
                    console.log(response.data)
                    this.posts = response.data
                    this.postLoading = false
                } catch(error: any) {
                    this.postLoading = false
                    console.log(error.response.data)
                }
            },
            
            async deletePost(postId: Number|String, userId: Number|String) {
                const confirm = await this.$bvModal.msgBoxConfirm(`Yakin ingin menghapus post dengan id: ${postId}?`, {
                    centered: true,
                    okVariant: "danger"
                })
                if (confirm) {
                    try {
                        await this.$axios.delete(`/users/${userId}/posts/${postId}`)
                        await this.$bvModal.msgBoxOk('Berhasil menghapus post!', {centered: true})
                        this.getPosts(userId)
                    } catch (error: any) {
                        await this.$bvModal.msgBoxOk(error.message, {
                            title: 'Gagal menghapus post!',
                            okVariant: 'danger',
                            centered: true
                        })
                    }
                }
            }
        }
    })
</script>