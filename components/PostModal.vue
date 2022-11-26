<template>
    <b-modal id="post-modal" title="Buat Post" no-stacking hide-footer>
        <template #default="{cancel}">
            <b-form @submit="onSubmit">
                <b-form-group id="input-group-1" label="Title:" label-for="input-1">
                    <b-form-input
                    id="input-1"
                    v-model="form.title"
                    type="text"
                    placeholder="Tuliskan Judul Post Anda"
                    required
                    ></b-form-input>
                </b-form-group>

                <b-form-group id="input-group-2" label="Body:" label-for="input-2">
                    <b-form-textarea
                    id="input-2"
                    v-model="form.body"
                    placeholder="Tuliskan Isi Post Anda"
                    rows="3"
                    required
                    ></b-form-textarea>
                </b-form-group>

                <div class="float-right">
                    <b-button variant="secondary" @click="cancel()">Batal</b-button>
                    <b-button type="submit" variant="primary" :disabled="isLoading">
                        <span v-if="isLoading"><b-spinner small></b-spinner> Loading...</span>
                        <span v-else>Simpan</span>
                    </b-button>
                </div>
            </b-form>
        </template>
    </b-modal>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
    name:'PostModal',
    props: ['userId'],
    data() {
        return {
            form: {
                title: '',
                body: ''
            },
            isLoading: false
        }
    },
    methods: {
        onSubmit(e: Event) {
            e.preventDefault()
            this.createPost()
        },
        async createPost() {
            this.isLoading = true
            try {
                await this.$axios.post(`/users/${this.userId}/posts`, this.form)
                this.isLoading = false
                const created = await this.$bvModal.msgBoxOk('Post baru berhasil dibuat!', {
                    title: 'SUCCESS!',
                    centered: true
                })
                if(created) this.$emit('reloadPosts')
            } catch (error: any) {
                this.isLoading = false
                await this.$bvModal.msgBoxOk(error.message, {
                    title: 'Gagal membuat post baru!',
                    okVariant: 'danger',
                    centered: true
                })
            }
        }
        
    }
})

</script>