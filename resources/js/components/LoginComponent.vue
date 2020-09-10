<template>
    <v-app id="inspire">
        <v-main>
            <v-container
                class="fill-height"
                fluid
            >
                <v-row
                    align="center"
                    justify="center"
                >
                    <v-col
                        cols="12"
                        sm="8"
                        md="4"
                    >
                        <v-card class="elevation-12">
                            <v-toolbar
                                color="error"
                                dark
                                flat
                            >
                                <v-toolbar-title>Login Form</v-toolbar-title>
                                <v-spacer></v-spacer>

                            </v-toolbar>
                            <v-card-text>
                                <v-progress-linear
                                    :active="loading"
                                    :indeterminate="loading"
                                    absolute
                                    top
                                    color="white accent-4"
                                ></v-progress-linear>
                                <v-form
                                    ref="form"
                                    v-model="valid"
                                >
                                    <v-text-field color="error"
                                        label="E-mail"
                                        name="login"
                                        v-model="email"
                                        :rules="emailRules"
                                        required
                                        prepend-icon="mdi-account-circle-outline"
                                        type="email"
                                    ></v-text-field>

                                    <v-text-field
                                        id="password" color="erloggedInror"
                                        label="Password"
                                        v-model="password"
                                        name="password"
                                        prepend-icon="mdi-account-lock-outline"
                                        type="password"
                                        required
                                    ></v-text-field>
                                </v-form>
                            </v-card-text>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="error" :disabled="!valid" block @click="login">Login</v-btn>
                            </v-card-actions>
                        </v-card>
                        <v-snackbar
                            v-model="snackbar"
                        >
                            {{ text }}
                            <template v-slot:action="{ attrs }">
                                <v-btn
                                    color="pink"
                                    text
                                    v-bind="attrs"
                                    @click="snackbar = false"
                                >
                                    Close
                                </v-btn>
                            </template>
                        </v-snackbar>
                    </v-col>
                </v-row>
            </v-container>
        </v-main>
    </v-app>
</template>

<script>
    export default {
        name: "LoginComponent",
        data() {
            return {
                valid: true,
                email : '',
                emailRules: [
                    v => !!v || 'E-mail is required',
                    v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
                ],
                password: '',
                passwordRules: [
                    v => !!v || 'Password is required',
                ],
                loading: false,
                snackbar: false,
                text: '',
            }
        },
        created() {
            this.$vuetify.theme.dark = true;
        },
        methods: {
            login: function() {
                // Add a request interceptor
                axios.interceptors.request.use((config) => {
                    this.loading = true;
                    return config;
                },  (error) => {
                    this.loading = false;
                    return Promise.reject(error);
                });

                // Add a response interceptor
                axios.interceptors.response.use( (response) => {
                    this.loading = false;
                    return response;
                },  (error) => {
                    this.loading = false;
                    return Promise.reject(error);
                });
                axios.post('/api/login', {'email': this.email, 'password': this.password})
                    .then(res => {
                        localStorage.setItem('token', res.data.token)
                        localStorage.setItem('loggedIn', true)
                        this.$router.push('/admin')
                        .then(res => console.log('LoggedIn Successffully'))
                        .catch(err => console.log(err))
                    })
                    .catch( err => {
                        this.text = err.response.data.status
                        this.snackbar = true;
                    })
            }
        }
    }
</script>

<style scoped>

</style>
