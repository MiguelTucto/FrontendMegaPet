<template>
  <div class="card">
    <div class="flex justify-content-center mb-5">
      <pv-img src="https://i.ibb.co/V0FB0MQ/icon.png" width="120" />
    </div>
    <div class="grid">
      <div class="col-5 flex justify-content-center">
        <v-form ref="form" v-model="form.isValid">
          <div class="p-fluid">
            <b><p :class="wrongEmailorPassword ? 'd-block' : 'd-none'" class="text-red-500">Wrong Email or Password</p></b>
            <div class="p-inputgroup mb-2">
            <span class="p-inputgroup-addon">
              <i class="pi pi-user"></i>
            </span>
              <pv-input-text id="username" type="text" placeHolder="Email" v-model="form.email" />
            </div>
            <div class="p-inputgroup mb-2">
            <span class="p-inputgroup-addon">
              <i class="pi pi-key" />
            </span>

              <pv-password v-model="form.password" :feedback="false" toggleMask placeholder="Password"/>

            </div>
            <pv-button label="Login"  @click="login()"></pv-button>
          </div>
        </v-form>

      </div>
      <div class="col-2">
        <pv-divider layout="vertical">
          <b>OR</b>
        </pv-divider>
      </div>
      <div class="col-5 flex align-items-center justify-content-center">
        <a @click="$router.push('/register')" style="text-decoration: none"
          ><pv-button
            label="Sign Up"
            icon="pi pi-user-plus"
            class="p-button-success"
          ></pv-button
        ></a>
      </div>
    </div>
  </div>
</template>

<script>
import { UsersApiService } from "../../user/services/users-api.service";
import { SheltersApiService } from "../../shelter/services/shelters-api.service";
import UsersService from "../../user/services/users.service";
import SheltersService from "../../shelter/services/shelters.service";
export default {
  name: "HomeFView",
  data() {
    return {
      form: {
        email: "",
        password: "",
        isValid: false
      },
      users: [],
      user: {},
      shelters: [],
      shelter: {},
      userService: null,
      shelterService: null,
      wrongEmailorPassword: false,
    };
  },

  created() {
    this.userService = new UsersApiService();
    this.userService.getAll().then((response)=>{
      this.users=response.data();
      console.log("created");
    });
    this.shelterService = new SheltersApiService();
    this.shelterService.getAll().then((response) => {
      this.shelters = response.data;
      console.log("created");
    });
  },

  methods: {

    submit(){
      this.form = { email: "", password: "" };
    },
    async login(){
      let noLogin = true;


      await UsersService.getByEmailAndPassword(this.form.email, this.form.password)
          .then(response => {
            if (response.data.length !== 0) {
              localStorage.setItem('clientId', response.data[0].id.toString());
              noLogin = false;
              this.$router.push("/user");
            }
          })

      await SheltersService.getByEmailAndPassword(this.form.email, this.form.password)
          .then(response => {
            if (response.data.length !== 0) {
              localStorage.setItem('clientId', response.data[0].id.toString());
              noLogin = false;
              this.$router.push("/shelters");
            }
          })

          .catch(e => {
            console.log(e);
          })
      if (noLogin){
        this.wrongEmailorPassword = true;
      } else {
        this.submit();
      }
    }


  },
};
</script>

<style scoped>
.card {
  padding-top: 10%;
}
</style>
