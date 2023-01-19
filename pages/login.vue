<template>
    <v-row>
        <v-col cols="10" offset="1" md="4" offset-md="4">
            <v-card class="mb-2">
                <v-toolbar color="primary" dark>Login</v-toolbar>
                <v-card-text>
                    <v-alert
                    v-if="isError"
                    color="red lighten-2"
                    dark
                    >{{ $t(message) }}</v-alert>
                    <v-form>
                        <v-text-field
                            name="email"
                            label="email"
                            type="email"
                            v-model="form.email"/>
                        <v-text-field
                            name="password"
                            label="password"
                            type="password"
                            v-model="form.password"/>
                    </v-form>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn 
                        @click="onSubmit" 
                        color="primary"
                        :disabled="isDisable">
                        <span v-if="!isDisable">Login</span>
                        <v-progress-circular
                        v-else
                        color="primary"
                        indeterminate></v-progress-circular>
                    </v-btn>
                </v-card-actions>
            </v-card>
            <p>Kamu sudah belum punya akun ? <v-btn to="/register" plain v-ripple="false">Register</v-btn></p>
        </v-col>
    </v-row>
</template>

<script>

import { mapMutations } from 'vuex'

export default({
    middleware: ['unauthenticated'],
    data() {
        return {
            message: '',
            isDisable: false,
            isError: false,
            form: {
                email: '',
                password: '',
            }
        }
    },
    methods: {
        ...mapMutations('auth', {
            setFullname : 'setFullname',
            setAccessToken : 'setAccessToken',
            setRefreshToken : 'setRefreshToken',
        }),
        storeWelcomeScreen() {
            localStorage.setItem("welcomeScreen", true)
        },
        onSubmit() {
            this.isDisable = true
            this.$axios.$post('http://localhost:3000/auth/login', this.form)
            .then(response => {
                if (!localStorage.welcomeScreen) {
                    this.storeWelcomeScreen()
                    alert()
                }
                this.isDisable = false

                // store auth data
                this.setFullname(response.fullname)
                this.setAccessToken(response.accessToken)
                this.setRefreshToken(response.refreshToken)

                this.$router.push('/dashboard')

            })
            .catch(error => {
                this.message = error.response.data.message 
                this.isDisable = false
                this.isError = true
            })
        },
    },
    mounted() {
        if (this.$route.params.message == 'AUTH_REQUIRED') {
            this.message = this.$route.params.message
            this.isError = true
        }
    }
})

</script>