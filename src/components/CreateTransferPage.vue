<template>
  <v-app id="inspire">
    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="4">
            <v-card class="elevation-12">
              <v-card-text>
                <v-form>
                  <v-text-field
                    v-model="input.account_number"
                    label="Numer konta odbiorcy"
                    name="account_number"
                    type="text"
                    :rules="[rules.required, rules.account_number, rules.numbers]"
                  />
                  <v-text-field
                    v-model="input.amount"
                    label="Kwota transakcji"
                    name="amount"
                    type="text"
                    :rules="[rules.required, rules.amount]"
                  />
                  <v-text-field
                    v-model="input.title"
                    label="Tytuł przelewu"
                    name="title"
                    :rules="[rules.required]"
                  />
                </v-form>
              </v-card-text>
              <v-card-actions>
                <v-btn text @click="handlePressBack()" color="primary">Powrót</v-btn>
                <v-spacer />
                <v-btn @click="handlePressConfirm()" color="primary">Zatwierdż</v-btn>
              </v-card-actions>
            </v-card>
            <v-snackbar v-model="snackbar" :timeout="timeout">
              {{ snackText }}
              <v-btn color="blue" text @click="snackbar = false">Zamknij</v-btn>
            </v-snackbar>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import router from "../router/index";
export default {
  name: "CreateTransferPage",
  data() {
    return {
      snackbar: false,
      timeout: 3000,
      snackText: "",
      regex: /^\d+$/,
      input: {
        account_number: "",
        amount: "",
        title: ""
      },
      rules: {
        required: value => {
          return !!value || "Pole nie może być puste";
        },
        account_number: value => {
          return value.length === 26 || "Nieprawidłowa długość numeru konta";
        },
        numbers: value => {
          return this.regex.test(value) || "Nieprawidłowe znaki.";
        },
        amount: value => {
          return !isNaN(value) || "Nieprawidłowa kwota.";
        }
      }
    };
  },
  beforeMount() {
    if (
      this.$route.params.account_number &&
      this.$route.params.amount &&
      this.$route.params.title
    ) {
      this.input.account_number = this.$route.params.account_number;
      this.input.amount = this.$route.params.amount;
      this.input.title = this.$route.params.title;
    }
  },
  methods: {
    handlePressBack() {
      router.push({
        name: "history",
        params: {
          account_number: this.$route.params.account_number,
          amount: this.$route.params.amount,
          title: this.$route.params.title
        }
      });
    },
    handlePressConfirm() {
      if (
        !this.input.account_number ||
        !this.input.amount ||
        !this.input.title
      ) {
        this.snackText = "Pola nie mogą być puste!";
        this.snackbar = true;
      } else if (
        !this.regex.test(this.input.account_number) ||
        this.input.account_number.length != 26 ||
        isNaN(this.input.amount)
      ) {
        this.snackText = "Nieprawidłowe dane!";
        this.snackbar = true;
      } else {
        router.push({
          name: "confirmtransfer",
          params: {
            account_number: this.input.account_number,
            amount: this.input.amount,
            title: this.input.title
          }
        });
      }
    }
  }
};
</script>