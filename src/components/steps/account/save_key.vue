<template>
  <div class="Box__container">
    <div class="Box__inner">
      <div class="SaveKey__container">
        <loadingbar class="loadingbar"/>
        <h1>Your password key</h1>
        <p>Make sure to save your password at a safe and secure location as IT CANNOT BE RESTORED OR RECOVERED.</p>
        <div 
          style="margin-bottom:30px;" 
          class="PickAccount__Input">
          <img src="./../../../assets/ic_key.svg"><div 
            class="Box__key" 
            placeholder="">{{ generatedPassword }}</div>
        </div>
        <p 
          v-if="created_account" 
          style="margin-bottom:5px;">Congratulations - your Account has been created!</p>
        <p 
          v-if="created_account" 
          style="margin-bottom:25px;">Please remember to save your password before leaving this page.</p>
        <div class="SaveKey__footer">
          <button 
            v-if="!saved_key && !created_account" 
            class="Btn__blue Btn__save" 
            style="width:140px" 
            @click="saveKey()"> Saved Password!</button>
          <button 
            v-if="saved_key && !created_account" 
            :disabled="getting_created" 
            class="Btn__blue Btn__save" 
            style="width:140px" 
            @click="createAccount()">Create Account</button>
          <button 
            v-if="created_account" 
            class="Btn__blue Btn__save" 
            @click="login()">Proceed to Utopian</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import loadingbar from "./../../partials/loading_bars/loading_bar_end.vue";
import { mapGetters } from "vuex";
export default {
  components: {
    loadingbar
  },
  data() {
    return {
      saved_key: false,
      created_account: false,
      input_account: "",
      input_error: "",
      getting_created: false
    };
  },
  computed: {
    ...mapGetters(["chosenAccountName", "generatedPassword"])
  },
  watch: {
    input_account: function() {
      this.getAccount();
    }
  },
  methods: {
    login() {
      if (confirm(`Did you save your password?`)) {
        this.$store.commit("setGeneratedPassword", { password: "" });
        this.$store.dispatch("login");
      }
    },
    saveKey() {
      this.download();
      this.saved_key = true;
    },
    createAccount() {
      this.getting_created = true;
      this.$store.dispatch("createAccount").then(response => {
        if (response.status === 200) {
          this.$cookies.set("c_a", true, Infinity);
          this.$notify({
            group: "main",
            text: response.data ? response.data.message : response.message,
            type: "success"
          });
          this.created_account = true;
        } else {
          this.$notify({
            group: "main",
            text: response.data ? response.data.message : response.message,
            type: "error"
          });
          this.getting_created = false;
        }
      });
    },
    download(filename, text) {
      let element = document.createElement("a");
      let info =
        "Please make sure that you save this password and delete the file afterwards. We advise writing it down on several pieces of papers and storing it in a secure place and/or using a password-safe application.";
      let password = `\r\n\r\nPassword: ${this.generatedPassword}`;
      element.setAttribute(
        "href",
        "data:text/plain;charset=utf-8," + encodeURIComponent(info + password)
      );
      element.setAttribute("download", "utopian_pass");
      element.style.display = "none";
      document.body.appendChild(element);
      element.click();
      document.body.removeChild(element);
    }
  }
};
</script>

<style>
.loadingbar {
  margin-top: 12px !important;
  margin-bottom: 12px !important;
}

.SaveKey__footer {
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.PickAccount__Input {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border: 1px solid #e0e2e5;
  border-radius: 2px;
}

.PickAccount__Input img {
  padding: 0 5px;
}

.Box__key {
  height: 55.44px;
  width: 336px;
  border-left: 1px solid #e0e2e5;
  display: flex;
  align-items: center;
  margin: 0 auto;
  padding-left: 5px;
}

.Input__utopian {
  height: 39.44px;
  flex: 1;
  margin-right: 10px;
  padding-left: 15px;
  border: 1px solid #e0e2e5;
  border-radius: 2px;
}

.Login__text {
  color: #2aa0f8;
  text-decoration: none;
  font-family: "Noto Sans";
  font-size: 12px;
  font-weight: bold;
  line-height: 16px;
  position: absolute;
  right: 25px;
}

.Btn__save {
  margin: 0 auto;
}

.Btn__save img {
  margin-right: 10px;
  margin-left: -8px;
}

.Btn__save:before {
}
</style>
