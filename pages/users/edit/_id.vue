<template>
    <v-row>
        <v-col cols="10" offset="1">
            <v-card class="mb-2">
                <v-toolbar color="primary" dark>Edit User</v-toolbar>
                <v-card-text>
                    <v-breadcrumbs :items="breadcrumbs" class="pa-0"/>
                    <v-form ref="form">
                        <v-text-field
                            name="fullname"
                            label="Full Name"
                            type="text"
                            :rules="rules.fullname"
                            v-model="form.fullname"/>
                        <v-text-field
                            name="email"
                            label="Email"
                            type="email"
                            :rules="rules.email"
                            v-model="form.email"
                            @keydown="checkEmailExist"/>
                        <v-text-field
                            name="password"
                            label="Password"
                            type="password"
                            :rules="rules.password"
                            v-model="form.password"/>
                        <v-text-field
                            name="retype_password"
                            label="Re-Password"
                            type="password"
                            :rules="rules.retype_password"
                            v-model="form.retype_password"/>
                        <v-select
                            v-model="form.role"
                            :items="roles"
                            label="Role"
                        />
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn 
                        @click="onSubmit" 
                        color="primary"
                        :disabled="isDisable">
                        <span v-if="!isDisable">Save</span>
                        <v-progress-circular
                        v-else
                        color="primary"
                        indeterminate></v-progress-circular>
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-col>
    </v-row>
  </template>
  
  <script>
  
  export default({
    middleware: ['authenticated'],
    asyncData({isDev, route, store, env, params, query, req, res, redirect, error}) {
        return {
            id: params.id
        }
    },
    data() {
        return {
            emailExist: false,
            isDisable: false,
            breadcrumbs: [
                  { text: 'Users', to: '/users', disabled: false, exact: true },
                  { text: 'Update', disabled: true },
              ],
            roles: ['employee', 'cashier', 'admin'],
            form: {
                fullname: '',
                email: '',
                password: '',
                retype_password: '',
                role: ''
            },
            rules: {
                fullname: [
                    v => !!v || this.$t('FIELD_REQUIRED', { field: 'Nama lengkap' })
                ],
                email: [
                    v => !!v || this.$t('FIELD_REQUIRED', { field: 'Email' }),
                    v => /.+@.+/.test(v) || this.$t('EMAIL_INVALID'),
                    v => !this.emailExist || this.$t('EMAIL_EXIST'),
                ],
                password: [
                    v => v.length == 0 || !!v || this.$t('FIELD_REQUIRED', { field: 'Password' }),
                    v => v.length == 0 || v.length >= 6 || this.$t('FIELD_MIN', { field: 'Password', min: 6 })
                ],
                retype_password: [ 
                    v => v === this.form.password || this.$t('FIELD_CONFIRM', { field: 'Password', confirm: 'Re-Password' })
                ],
                role: [
                    v => !!v || this.$t('FIELD_REQUIRED', { field: 'Role' })
                ]
            }
        }
    },
    methods: {
        checkEmailExist() {
          this.emailExist = false
        },
        fecthData() {
            this.$axios.$get(`/users/${this.id}`)
                .then( response => {
                    console.log(response)
                    this.form.fullname = response.user.fullname
                    this.form.email = response.user.email
                    this.form.role = response.user.role
                })
                .catch(error => {
                    this.$router.push({
                        name: 'users___'+ this.$i18n.locale,
                        params: { message: 'ID_NOT_FOUND' }
                    })
                })
        },
        onSubmit() {
            if (this.$refs.form.validate()) {
              this.isDisable = true
              this.$axios.$put(`/users/${this.id}`, this.form)
              .then( response => {
                  this.isDisable = false
                  this.$router.push({
                    name: 'users___'+ this.$i18n.locale,
                    params: { message: 'UPDATE_SUCCESS', fullname: this.form.fullname }
                })
              })
              .catch(error => {
                  if (error.response.data.message == "EMAIL_EXIST") {
                    this.emailExist = true
                    this.$refs.form.validate()
                  }
                  this.isDisable = false
              })
            }
        },
    },
    mounted() {
        this.fecthData()
    }
  })
  
  </script>