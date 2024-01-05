<script setup>
import { ref } from "vue";
import { useUserStore } from "@/stores/user";
import { toast } from "vue3-toastify";
import { setAccessToken, getAccessToken } from "@/composables/useLocalStorage";

const authStore = useUserStore();
const isLogin = ref(true);
const fullName = ref("");
const email = ref("");
const password = ref("");
const confirmPassword = ref("");
const phoneNumber = ref(""); // Thêm trường phoneNumber
const userName = ref(""); // Thêm trường userName
const nickname = ref(""); // Thêm trường nickname
const token = getAccessToken();
if (token) {
  window.location.href = "./home";
}

const handleLogin = async () => {
  if (isLogin.value) {
    // Xử lý đăng nhập
    try {
      let response = await fetch("http://localhost:5000/api/auth/login", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ user_name: email.value, password: password.value }),
      });
      response = await response.json();
      console.log(response, "response");
      if (response && response.uid) {
        setAccessToken(response.uid);
        window.location.href = "./home";
      } else {
        toast("Invalid login credentials!", {
          autoClose: 1000,
          position: toast.POSITION.TOP_RIGHT,
          type: "error",
        });
      }
    } catch (e) {
      console.log(e, "login error");
    }
  } else {
    // Xử lý đăng ký
   if (password.value !== confirmPassword.value) {
      toast("Passwords do not match!", {
        autoClose: 1000,
        position: toast.POSITION.TOP_RIGHT,
        type: "error",
      });
      return;
    }
    try {
      let response = await fetch("http://localhost:5000/api/auth/register", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          fullName: fullName.value,
          email: email.value,
          phone_number: phoneNumber.value,
          user_name: userName.value,
          password: password.value,
          nickname: nickname.value,
        }),
      });
      const data = await response.json();
        // Nếu đăng ký thành công
        toast("Registration successful!", {
          autoClose: 1000,
          position: toast.POSITION.TOP_RIGHT,
          type: "success",
        });

    } catch (e) {
      console.error(e);
      toast("Registration success!", {
        autoClose: 1000,
        position: toast.POSITION.TOP_RIGHT,
        type: "success",
      });
    }
  }
};
</script>

<template>
  <div>
    <main>
      <section class="absolute w-full h-full">
        <div
          class="absolute top-0 w-full h-full bg-gray-900"
          style="background-size: 100%; background-repeat: no-repeat"
          :style="`background-image: url(src/assets/img/register_bg_2.png);`"
        ></div>
        <div class="container mx-auto px-4 h-full">
          <div class="flex content-center items-center justify-center h-full">
            <div class="w-full lg:w-4/12 px-4">
              <div
                class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded-lg bg-gray-300 border-0"
              >
                <div class="rounded-t mb-0 px-6 py-6">
                  <div class="text-center mb-3">
                    <h6 class="text-gray-600 text-sm font-bold">
                      {{ isLogin ? "Login" : "Register" }}
                    </h6>
                  </div>
                  <hr class="mt-6 border-b-1 border-gray-400" />
                </div>
                <div class="flex-auto px-4 lg:px-10 py-10 pt-0">
                  <form>
                    <!-- Phần Đăng Ký -->
                    <div v-if="!isLogin">
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="email">Email</label>
                        <input v-model="email" type="email" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Email" />
                      </div>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="phone_number">Phone Number</label>
                        <input v-model="phoneNumber" type="text" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Phone Number" />
                      </div>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="user_name">User Name</label>
                        <input v-model="userName" type="text" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="User Name" />
                      </div>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="nickname">Nickname</label>
                        <input v-model="nickname" type="text" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Nickname" />
                      </div>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="password">Password</label>
                        <input v-model="password" type="password" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Password" />
                      </div>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="confirmPassword">Confirm Password</label>
                        <input v-model="confirmPassword" type="password" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Confirm Password" />
                      </div>
                    </div>

                    <!-- Phần Đăng Nhập -->
                    <div v-else>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="email">Username</label>
                        <input v-model="email" type="email" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Username" />
                      </div>
                      <div class="relative w-full mb-3">
                        <label class="block uppercase text-gray-700 text-xs font-bold mb-2" for="password">Password</label>
                        <input v-model="password" type="password" class="border-0 px-3 py-3 placeholder-gray-400 text-gray-700 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full" placeholder="Password" />
                      </div>
                    </div>

                    <!-- Nút submit -->
                    <div class="text-center mt-6">
                      <button class="bg-gray-900 text-white active:bg-gray-700 text-sm font-bold uppercase px-6 py-3 rounded shadow hover:shadow-lg outline-none focus:outline-none mr-1 mb-1 w-full" type="button" @click="handleLogin">
                        {{ isLogin ? "Login" : "Register" }}
                      </button>
                    </div>

                    <!-- Nút chuyển đổi giữa Đăng nhập và Đăng ký -->
                    <div class="text-center mt-6">
                      <button class="text-sm text-gray-600 underline" type="button" @click="isLogin = !isLogin">
                        {{ isLogin ? "Need an account? Register" : "Already have an account? Login" }}
                      </button>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>



