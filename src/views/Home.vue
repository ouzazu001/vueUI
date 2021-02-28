<template>
  <div class="home">
    <v-btn @click="dialog = true">create event</v-btn>
    <v-dialog
      v-model="dialog"
      scrollable
      persistent
      :overlay="false"
      max-width="500px"
      transition="dialog-transition"
    >
      <v-card>
        <v-card-title primary-title>
          สร้าง event
          <v-spacer></v-spacer>
          <v-btn icon @click="(form = {}) && (dialog = false)"
            ><v-icon>mdi-close</v-icon></v-btn
          >
        </v-card-title>
        <v-card-text>
          <form @submit.prevent="storeEvent()" class="mt-4">
            <v-text-field
              label="ชื่อ"
              append-icon=""
              v-model="form.name"
              outlined
              color
            ></v-text-field>
            <v-text-field
              label="รายระเอียด"
              append-icon=""
              v-model="form.detail"
              outlined
              color
            ></v-text-field>
            <v-btn v-if="!form.id" type="submit">Save</v-btn>
            <v-btn v-else type="submit">Edit</v-btn>
          </form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <div class="m-4 p-4" v-for="(list, index) in listEvents" :key="index">
      <v-card>
        <v-card-title primary-title>
          {{ list.name }}
        </v-card-title>
        <v-card-text>
          {{ list.detail }}
        </v-card-text>
        <v-card-actions>
          <v-btn @click="openEdit(list)">Edit</v-btn>
          <v-btn @click="deleteEvent(list.id)">Delete</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "@/plugins/axios";
@Component({
  components: {},
})
export default class Home extends Vue {
  listEvents: any = [];
  dialog: boolean = false;
  form: any = {};

  async load() {
    let data = await axios
      .get("/api/eventmain/")
      .then((r) => {
        return r.data;
      })
      .catch((e) => {
        return e.reponse;
      });

    this.listEvents = data;

    console.log(data);
  }

  async storeEvent() {
    if (!this.form.id) {
      let data = await axios
        .post("/api/eventmain/", this.form)
        .then((r) => {
          return r.data;
        })
        .catch((e) => {
          return e.reponse;
        });
      if (data.id) {
        alert("Save Success !!!");
        await this.load();
        this.dialog = false;
      } else {
        alert("Error Save!!!");
      }
    } else {
      await this.updateEvent();
    }
  }

  async updateEvent() {
    let data = await axios
      .put("/api/eventmain/" + this.form.id + "/", this.form)
      .then((r) => {
        return r.data;
      })
      .catch((e) => {
        return e.reponse;
      });
    if (data.id) {
      alert("Edit Success !!!");
      await this.load();
      this.dialog = false;
    } else {
      alert("Error Edit!!!");
    }
  }

  async openEdit(form: any) {
    this.form = form;
    this.dialog = true;
  }

  async deleteEvent(id: number) {
    let check = confirm("Are You sure?");
    if (check) {
      let data = await axios.delete("/api/eventmain/" + id + "/");
      alert("Delete Success !!!");
      await this.load();
    }
  }

  async checkLogin(){
       let data = await axios.get("/auth2/profile/")
       .then((r)=>{
           return true;
           })
       .catch((e)=>{
 return false;
       })
       if(!data){
           alert('Session หมดอายุ')
           await this.$router.replace('/login')
       }
       console.log(data);
  }

  async created() {
      await this.checkLogin();
    await this.load();
  }
}
</script>
