<template>
    <v-row class="frame-content">
        <v-col cols="10" offset="1">
            <v-card class="my-3">
                <v-toolbar color="primary" dark>
                    Users
                </v-toolbar>
                <v-card-text>
                    <div class="mb-4">
                        <v-breadcrumbs :items="breadcrumbs" class="pa-0"/>
                    </div>
    
                    <v-data-table
                        :headers="headers"
                        :items-per-page="10"
                        :items="users" />
                </v-card-text>
            </v-card>
        </v-col>
    </v-row>
</template>

<script>

export default {
    data() {
        return {
            users: [],
            headers: [
                { text: 'Fullname', value: 'fullname' },
                { text: 'Email', value: 'email' },
                { text: 'Role', value: 'role' },
            ],
            breadcrumbs: [
                {
                    text: 'Users',
                    disabled: true,
                    to: '/users'
                }
            ]
        }
    },
    methods: {
        fecthUsers() {
            this.$axios.$get(`http://localhost:3000/users`)
                .then(response => {
                    this.users = response.users.docs
                })
                .catch(err => {
                    console.log(err)
                })
        }
    },
    mounted() {
        this.fecthUsers()
    }
}

</script>