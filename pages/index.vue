<template>
    <b-container class="pt-5">
        <h1>Daftar User</h1>
        <b-button variant="primary" class="float-right mb-3" @click="addUser">
            Buat User
        </b-button>
        <b-table id="users-table" :items="users" :fields="fields" :busy="isLoading" striped hover show-empty>
            <template #table-busy>
                <div class="text-center my-2">
                    <b-spinner class="align-middle"></b-spinner>
                    <strong>Loading...</strong>
                </div>
            </template>
            <template #cell(action)="row">
                <b-button size="sm" variant="link" :to="`/user/${row.item.id}`">
                    View
                </b-button>
                <b-button size="sm" variant="success" @click="updateUser(row.item)">
                    Update
                </b-button>
                <b-button size="sm" variant="danger" @click="deleteUser(row.item.id)">
                    Delete
                </b-button>
            </template>
        </b-table>
        <b-pagination 
            v-model="currentPage"
            :total-rows="rows"
            :per-page="perPage"
            @change="handlePageChange"
            align="center"
        />
        <UserModal :title="title" :user="selectedUser" v-on:reloadUsers="getUsers" />
    </b-container>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
    name: 'IndexPage',
    
    data() {
        return {
            perPage: 10,
            currentPage: 1,
            rows: 100,
            fields: ['id', 'name', 'email', 'gender', 'status', 'action'],
            users: [],
            title: 'Add User',
            selectedUser: {},
            showModal: false,
            isLoading: true
        }
    },

    mounted() {
        this.getUsers()
    },
    
    methods: {
        handlePageChange(page: any){
            this.currentPage = page
            this.getUsers()
        },
        addUser(){
            this.title = 'Add User'
            this.selectedUser = {id: null, email: null, name: null, gender: null, status: null}
            this.$bvModal.show('user-modal')
        },
        updateUser(user: Object) {
            this.title = 'Update User'
            this.selectedUser = user
            this.$bvModal.show('user-modal')
        },
        async getUsers() {
            console.log({currentPage: this.currentPage})
            this.isLoading = true
            try {
                const users = await this.$axios.$get(`/users?page=${this.currentPage}&per_page=${this.perPage}`)
                this.users = users
                this.isLoading = false
            } catch (error: any) {
                console.log(error)
                this.isLoading = false
            }
        },
        async deleteUser(id: Number|String) {
            const confirm = await this.$bvModal.msgBoxConfirm(`Yakin ingin menghapus user dengan id: ${id}?`, {
                centered: true,
                okVariant: "danger"
            })
            if (confirm) {
                try {
                    await this.$axios.delete(`/users/${id}`)
                    await this.$bvModal.msgBoxOk('Berhasil menghapus user!', {centered: true})
                    this.getUsers()
                } catch (error: any) {
                    await this.$bvModal.msgBoxOk(error.message, {
                        title: 'Gagal menghapus user!',
                        okVariant: 'danger',
                        centered: true
                    })
                }
            }
        }
    }
})
</script>
