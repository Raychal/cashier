<template>
    <v-row>
        <v-col cols="10" offset="1" md="4" offset-md="4">
            <v-card class="mb-2">
                <v-toolbar color="primary" dark>Register</v-toolbar>
                <v-card-text>
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
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn 
                        @click="onSubmit" 
                        color="primary"
                        :loading="isDisable">
                        Register
                    </v-btn>
                </v-card-actions>
            </v-card>
            <p>Kamu sudah punya akun ? <v-btn to="/login" plain v-ripple="false">Login</v-btn></p>
        </v-col>
    </v-row>
</template>

<script>

export default({
    middleware: ['unauthenticated'],
    head: {
      title: 'Register'
    },
    data() {
        return {
            emailExist: false,
            isDisable: false,
            form: {
                fullname: '',
                email: '',
                password: '',
                retype_password: ''
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
                    v => !!v || this.$t('FIELD_REQUIRED', { field: 'Password' }),
                    v => v.length >= 6 || this.$t('FIELD_MIN', { field: 'Password', min: 6 })
                ],
                retype_password: [ 
                    v => v === this.form.password || this.$t('FIELD_CONFIRM', { field: 'Password', confirm: 'Re-Password' })
                ]
            }
        }
    },
    methods: {
        checkEmailExist() {
            this.emailExist = false
        },
        onSubmit() {
            if (this.$refs.form.validate()) {
                this.isDisable = true
                this.$axios.$post('/auth/register', this.form)
                .then( res => {
                    this.isDisable = false
                    this.$router.push('/login')
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
    }
})

</script>