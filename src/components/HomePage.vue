<template>
  <v-card>
    <v-card-text class="pa-0 ma-0 pt-3">
      <div align="center" text>
        <h3 class="">
          {{ getCurrentDate() }}
        </h3>
      </div>
    </v-card-text>
    <v-card-title>
      <v-spacer></v-spacer>

      <div text>
        <p class="display-2">
          {{ currentTime }}
        </p>
      </div>

      <v-spacer></v-spacer>
    </v-card-title>

    <v-card-title>
      <v-spacer></v-spacer>
      <v-btn
        color="secondary"
        @click="markAttendence"
        elevation="4"
        :disabled="loading"
      >
        {{ loggedIn === true ? "Sign Off" : "Sign In" }}
      </v-btn>
      <v-spacer></v-spacer>
    </v-card-title>
  </v-card>
</template>

<script>
import axios from "axios";
export default {
  name: "HomePage",
  mounted() {
    setInterval(this.getCurrentTime, 1);

    axios
      .get("/api/attendences")
      .then(({ data }) => {
        this.data = data.data;

        if (
          this.data &&
          this.data.length > 0 &&
          this.data[0].action &&
          this.data[0].action === "IN"
        ) {
          this.loggedIn = true;
        }
      })
      .catch((e) => {
        console.log({ e });
      })
      .finally(() => {
        this.loading = false;
      });
  },
  data: () => {
    return {
      search: "",
      loading: false,
      currentTime: null,
      loggedIn: false,
      data: [],
    };
  },
  methods: {
    getCurrentDate() {
      const today = new Date();
      return (
        today.getFullYear() +
        "-" +
        (today.getMonth() + 1) +
        "-" +
        today.getDate()
      );
    },
    getCurrentTime() {
      const today = new Date();

      const time =
        today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
      this.currentTime = time;
    },
    getSignInText() {
      return this.loggedIn === true ? "OUT" : "IN";
    },
    markAttendence() {
      this.loading = true;
      axios
        .post("/api/attendences", {
          action: this.getSignInText(),
        })
        .then(({ data }) => {
          this.data = data.data;
          this.$swal("Logged In", "", "info");

          this.$swal(
            `Sign${this.getSignInText().toLowerCase()} Successful`,
            "",
            "success"
          );

          this.loggedIn = !this.loggedIn;
        })
        .catch((e) => {
          console.log({ e });
        })
        .finally(() => {
          this.loading = false;
        });
    },
  },
};
</script>

<style>
</style>