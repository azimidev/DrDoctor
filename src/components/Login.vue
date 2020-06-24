<template>
  <div>
    <div class="flex justify-center mt-10">
      <img alt="logo" src="../assets/logo.png" width="400" />
    </div>
    <form
      @submit.prevent="nextToContactDetails"
      class="mt-10 flex flex-col items-center"
    >
      <h1 class="text-2xl font-extrabold">Login</h1>
      <div class="text-left mt-5">
        <div class="mt-3">
          <label class="mr-5">Last name</label>
          <input
            title="last name"
            required
            maxlength="50"
            v-model="lastName"
            type="text"
            placeholder="Last name"
            class="border border-transparent rounded-lg bg-gray-200 py-2 pr-4 pl-5 block w-full appearance-none leading-normal ds-input"
          />
        </div>
        <div class="mt-3">
          <label class="mr-5">Date of Birth</label>
          <input
            title="date of birth"
            required
            v-model="dob"
            type="date"
            placeholder="Date of Birth"
            class="border border-transparent rounded-lg bg-gray-200 py-2 pr-4 pl-5 block w-full appearance-none leading-normal ds-input"
          />
        </div>
        <div class="mt-3">
          <label class="mr-5">Post Code</label>
          <input
            title="post code"
            required
            v-model="postCode"
            pattern="[a-zA-Z]{1,2}[0-9]{1,2}\s?[0-9][a-zA-Z]{2}"
            type="text"
            placeholder="Post Code"
            class="border border-transparent rounded-lg bg-gray-200 py-2 pr-4 pl-5 block w-full appearance-none leading-normal ds-input"
          />
        </div>
        <div class="text-center mt-5">
          <button
            type="submit"
            class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
          >
            Next
          </button>
        </div>
      </div>
    </form>
    <div v-if="revealContactDetails" class="my-10">
      <h1 class="text-2xl font-extrabold">
        How do you want to receive your one-time code?
      </h1>
      <div class="mt-5 flex justify-around my-5">
        <div class="flex items-center">
          <input
            type="radio"
            class="border-4 p-3 mx-1 border-green-500 cursor-pointer"
            value="landline"
            v-model="selected"
          />
          <label>Landline</label>
        </div>

        <div class="flex items-center">
          <input
            type="radio"
            class="border-4 p-3 mx-1 border-green-500 cursor-pointer"
            value="mobile"
            v-model="selected"
          />
          <label>Mobile</label>
        </div>

        <div class="flex items-center">
          <input
            type="radio"
            class="border-4 p-3 mx-1 border-green-500 cursor-pointer"
            value="email"
            v-model="selected"
          />
          <label>Email</label>
        </div>
      </div>
      <div class="text-center mt-5">
        <button
          v-show="selected"
          @click="nextToCodeBox"
          type="button"
          class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
        >
          Next
        </button>
      </div>
      <div v-if="revealCodeBox" class="my-10">
        <h1 class="text-2xl font-extrabold">
          Please enter your code below:
        </h1>
        <div class="flex justify-center text-left">
          <div class="my-3">
            <label class="mr-5">Your one-time code</label>
            <input
              required
              v-model.trim="code"
              type="text"
              placeholder="Post Code"
              class="border border-transparent rounded-lg bg-gray-200 py-2 pr-4 pl-5 block w-full appearance-none leading-normal ds-input"
            />
          </div>
        </div>
        <div class="my-3">
          <button
            v-show="code"
            @click="login"
            type="button"
            class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded"
          >
            Login
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      lastName: "",
      dob: "",
      postCode: "",
      revealContactDetails: false,
      selected: "",
      revealCodeBox: false,
      code: ""
    };
  },
  methods: {
    nextToContactDetails() {
      if (this.validate()) {
        this.revealContactDetails = true;
      }
    },
    nextToCodeBox() {
      if (this.validate() && this.selected) {
        this.revealCodeBox = true;
      }
    },
    login() {
      if (this.validate() && this.selected) {
        // NOTE: Assume ths code is 0000
        if (this.code !== "0000") {
          alert("The code is wrong!");
          return;
        }
        this.$store
          .dispatch("login", {
            lastName: this.lastName,
            dob: this.dob,
            postCode: this.postCode
          })
          .then(() => this.$router.push("/secure"))
          .catch(err => console.error(err));
      } else {
        alert("something went wrong!");
      }
    },
    validate() {
      if (!this.lastName || this.lastName.length > 50) {
        alert("The last name is not valid");
        return false;
      }

      let date_regex = /^\d{2,4}([/-])\d{2}\1\d{2,4}$/;
      if (!date_regex.test(this.dob)) {
        alert("The date is not valid");
        return false;
      }

      let post_code = this.postCode.replace(/\s/g, "");
      let post_code_regex = /^[A-Z]{1,2}[0-9]{1,2} ?[0-9][A-Z]{2}$/i;
      if (!post_code_regex.test(post_code)) {
        alert("The post code is not valid");
        return false;
      }

      return true;
    }
  }
};
</script>
