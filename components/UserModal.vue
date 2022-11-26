<template>
    <b-modal id="user-modal" :title="title" no-stacking hide-footer>
        <template #default="{cancel}">
            <b-form @submit="onSubmit">
                <b-form-group id="input-group-1" label="Email address:" label-for="input-1">
                    <b-form-input
                    id="input-1"
                    v-model="form.email"
                    type="email"
                    placeholder="Enter email"
                    required
                    ></b-form-input>
                </b-form-group>

                <b-form-group id="input-group-2" label="Your Name:" label-for="input-2">
                    <b-form-input
                    id="input-2"
                    v-model="form.name"
                    placeholder="Enter name"
                    required
                    ></b-form-input>
                </b-form-group>

                <b-form-group id="input-group-3" label="Gender:" label-for="input-3">
                    <b-form-select
                    id="input-3"
                    v-model="form.gender"
                    :options="gender"
                    required
                    ></b-form-select>
                </b-form-group>

                <b-form-group id="input-group-4" label="Status:" label-for="input-4">
                    <b-form-select
                    id="input-4"
                    v-model="form.status"
                    :options="status"
                    required
                    ></b-form-select>
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
    import Vue from 'vue'
    
export default Vue.extend({
    name: 'UserModal',
    props: ['title', 'user'],
    data() {
        return {
            form: {
                id: Number,
                email: String,
                name: String,
                gender: String,
                status: String
            },
            gender: [{text: '-Select one-', value: null}, 'male', 'female'],
            status: [{text: '-Select one-', value: null}, 'active', 'inactive'],
            isLoading: false
        }
    },
    watch: {
        user() {
            this.form = {...this.user}
        }
    },
    methods: {
        onSubmit(event: Event) {
            event.preventDefault()
            this.title === 'Add User' ? this.createUser() : this.updateUser()
            // console.log({form: this.form})
        },

        async createUser() {
            this.isLoading = true
            try {
                await this.$axios.post('/users', this.form)
                this.isLoading = false
                // console.log({response})
                const created = await this.$bvModal.msgBoxOk('User baru berhasil dibuat!', {
                    title: 'SUCCESS!',
                    centered: true
                })
                if(created) this.$emit('reloadUsers')
            } catch (error: any) {
                // console.log({error})
                this.isLoading = false
                await this.$bvModal.msgBoxOk(error.message, {
                    title: 'Gagal membuat user baru!',
                    okVariant: 'danger',
                    centered: true
                })
            }
        },

        async updateUser() {
            this.isLoading = true
            try {
                await this.$axios.put(`/users/${this.form.id}`, this.form)
                this.isLoading = false
                // console.log({response})
                const updated = await this.$bvModal.msgBoxOk('User berhasil diupdate!', {
                    title: 'SUCCESS!',
                    centered: true
                })
                if(updated) this.$emit('reloadUsers')
            } catch (error: any) {
                // console.log({error})
                this.isLoading = false
                await this.$bvModal.msgBoxOk(error.message, {
                    title: 'Gagal update user!',
                    okVariant: 'danger',
                    centered: true
                })
            }
        }
    }
})
</script>