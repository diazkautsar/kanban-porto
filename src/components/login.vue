<template>
    <div class="container">
        <div id="form-login">
            <h1 style="font-family: 'Lemonada', cursive;">LOG IN</h1>
            <form v-on:submit.prevent="login">
                <label>EMAIL</label>
                <input type="email" required v-model="email">
                <label>PASSWORD</label>
                <input type="password" required v-model="password">
                <button type="submit" class="btn btn-primary">SUBMIT</button>
            </form>
            <br>
            <h3 style="font-family: 'Lemonada', cursive; font-size: 25px">Or Login via:</h3>
            <a @click="googleLogin"><i class="fab fa-google" style="font-size: 25px"></i></a>
        </div>
    </div>
</template>

<script>
import axios from '../config'

export default {
    name: 'login',
    data () {
        return {
            email: '',
            password: ''
        }
    },
    methods: {
        login () {
            axios({
                method: "post",
                url: "/login",
                data: {
                    email: this.email,
                    password: this.password
                }
            })
                .then(({data}) => {
                    localStorage.setItem('token', data.token)
                    this.notifSuccess('login success')
                    this.changePage('landing')
                })
                .catch(err => {
                    this.notifFailed(err.response.data.msg)
                })
        },
        googleLogin () {
            this.$gAuth.signIn()
                .then(authCode => {
                    const id_token = authCode.getAuthResponse().id_token
                    return axios ({
                        method: 'POST', 
                        url: "/googlelogin",
                        headers: {
                            token: id_token
                        }
                    })
                })
                .then(data =>  {
                    localStorage.setItem('token', data.data.token)
                    this.notifSuccess('login success')
                    this.changePage('landing')
                })
                .catch(err => {
                    this.notifFailed('login failed')
                })
        },
        changePage(page) {
            this.$emit('changePage', page)
        },
        notifSuccess(msg) {
            this.$emit('notifSuccess', msg)
        },
        notifFailed(msg) {
            this.$emit('notifFailed', msg)
        }
    } 

}
</script>

<style>

</style>