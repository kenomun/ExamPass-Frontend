<template>
  <div class="flex items-center justify-center mt-32 bg-cover bg-center">
    <div
      class="bg-white bg-opacity-80 backdrop-blur-lg shadow-lg rounded-lg p-8 max-w-md w-full"
    >
      <h2 class="text-3xl font-semibold text-center text-gray-800 mb-6">
        Inicio de Sesión
      </h2>
      <div class="mt-6">
        <div
          class="flex flex-col items-center justify-left space-y-2 rounded-md"
        >
          <GoogleLogin
            :clientId="google_client_id"
            :callback="loginWithGoogle"
            ref="a"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { GoogleLogin, decodeCredential } from "vue3-google-login";
import { useRouter } from "vue-router";
import { useUserStore } from "@/store/useStore.js";
import { API_BASE_URL } from "../config/config";
import axios from "axios";

export default {
  components: {
    GoogleLogin,
  },
  setup() {
    const google_client_id = import.meta.env.VITE_CLIENTID;

    const router = useRouter();

    const loginWithGoogle = async (response) => {
      try {
        const user = decodeCredential(response.credential);
        const res = await axios.get(`${API_BASE_URL}/login/${user.email}`);

        const userData = res.data.data;
        console.log("userData", userData);
        const imagen = user.picture;
        const googleToken = response.credential;
        if (res.status === 200) {
          const userStore = useUserStore();
          userStore.login({
            id: userData.id,
            name: userData.name,
            token: userData.token,
            email: userData.email,
            role: userData.roleId,
            picture: imagen,
            googleToken: googleToken,
          });

          if (userData.roleId == 2)
            router.push({ name: "AdminDashboardContent" });
          if (userData.roleId == 3)
            router.push({ name: "StudentDashboardContent" });
        }
      } catch (error) {
        console.log(`error: ${error}`);
        if (error.response.status === 401) {
          // Usuario no registrado
          toast(error.response.data.msg, {
            type: "error",
          });
        } else if (error.response.status === 403) {
          // Usuario sin autorización para acceder
          toast(error.response.data.msg, {
            type: "error",
          });
        } else {
          toast(error.response.data.msg, {
            type: "error",
          });
        }
      }
    };

    return {
      google_client_id,
      loginWithGoogle,
    };
  },
};
</script>

<style scoped>
/* Puedes añadir estilos adicionales específicos para este componente aquí */
</style>
