<template>
    <div
        class="row justify-content-center align-items-center"
        style="height: 100vh"
    >
        <form-login @handle-submit="procesarLogin" :user="user" />
    </div>
</template>

<script>
import FormLogin from "@/components/users/FormLogin.vue";

import { mapActions, mapMutations, mapState } from "vuex";
import { emailRegex } from "../Helpers/helperFunctions.js";
export default {
    name: "LoginView",
    data() {
        return {
            user: {
                email: "",
                password: "",
            },
            authCheckedCompleted: false,
        };
    },
    created() {
        const token = localStorage.getItem("token");
        const usuarioLogin = JSON.parse(localStorage.getItem("user_back"));

        if (token && usuarioLogin) {
            this.setToken(token);
            this.setIsAuthenticated(true);
            this.setUsuario(usuarioLogin);
        }

        if (this.isAuthenticated) {
            this.$router.push({ name: "tareas" });
        }
    },

    computed: {
        ...mapState("usuarios", ["isAuthenticated", "errorLogin"]),
    },
    methods: {
        ...mapActions("usuarios", ["login"]),
        ...mapMutations("usuarios", [
            "setToken",
            "setIsAuthenticated",
            "setUsuario",
        ]),

        async procesarLogin() {
            if (this.user.email === "" || this.user.password === "") {
                alert("Debe ingresar un email y un password");
                return;
            }

            // Expresión regular para validar correo electrónico
            if (!emailRegex.test(this.user.email)) {
                alert("Debe ingresar un email válido");
                return;
            }

            await this.login(this.user);
            if (this.errorLogin) {
                alert("Usuario y/o contraseña inválida");
            } else if (this.isAuthenticated) {
                this.$router.push({ name: "tareas" });
            }
        },
    },
    components: {
        FormLogin,
    },
};
</script>
