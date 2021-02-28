<template>
  <div class="home">
    <form @submit.prevent="login()">
      <v-text-field v-model="form.username" label="Username"></v-text-field>
      <v-text-field v-model="form.password" label="Password"></v-text-field>
      <v-btn color="success" type="submit">Login</v-btn>
    </form>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "@/plugins/axios";
@Component({
  components: {},
})
export default class Login extends Vue {
  form: any = {};

  async login() {
    let data = await axios
      .post("/auth/login/", this.form)
      .then((r) => {
        return r.data;
      })
      .catch((e) => {
        return e.reponse;
      });
      console.log(data);
  }
  async created() {}
}
</script>
