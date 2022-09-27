<template>
    <div class="testapp-login-container">
        <div class="testapp-form-container testapp-box">
            
            <!-- Header info -->
            <div class="testapp-logo mb-4"></div>
            <div class="row mb-2">
                <div class="col">
                    <h1 class="mb-1">{{ text.title }}</h1>
                    <p>{{ text.additional }}</p>
                </div>
            </div>

            <!-- Login Form -->
            <div class="testapp-form row">
                <div class="col">
                    <!-- Username field -->
                    <input 
                        class="mb-3 w-100"
                        name="email"
                        type="email" 
                        required
                        v-model="username"
                        :placeholder="text.emailPlaceholder"
                        @keyup="validateField('email', username)"
                    >

                    <!-- Password field -->
                    <input 
                        class="mb-4 w-100"
                        name="password"
                        type="password" 
                        required
                        v-model="password"
                        :placeholder="text.passwordPlaceholder"
                        @keyup="validateField('password', password)"
                    >
                </div>
            </div>

            <!-- Login button -->
            <div class="row">
                <div class="col">
                    <button class="testapp-btn w-100" type="submit" @click="logIn">{{ text.loginButton }}</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'LoginScreen',

    data() {
        return {
            usernameValid: false,
            passwordValid: false,
            username: '',
            password: '',
            text: {
                title: 'Sign in',
                additional: 'Please enter your email and password',
                emailPlaceholder: 'Your email address',
                passwordPlaceholder: 'Password',
                loginButton: 'Sign In'
            }
        }
    },

    methods: {
        validateField(fieldType, fieldValue) {
            // Check and set email and password validity
            if (fieldType === 'email') {
                /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(fieldValue) ? 
                    this.usernameValid = true : 
                    this.usernameValid = false;
            } else if (fieldType === 'password') {
                fieldValue.length >= 8 ? 
                    this.passwordValid = true : 
                    this.passwordValid = false;
            }
        },

        logIn() {
            if (
                this.usernameValid &&
                this.passwordValid
            ) {
                this.$emit('pageLoad', 1);
            }
        }
    },
}
</script>

<style scoped lang="scss">
    @import "../styles/variables";
    @import "../styles/component-loginScreen";
</style>
