<template>
   <form @submit.prevent="signIn" class="mx-2">
      <div class="font-weight-bold headline">Login</div>

      <v-text-field
         @blur="$v.usernameOrEmail.$touch()"
         :error-messages="usernameOrEmailErrors"
         v-model="usernameOrEmail"
         label="Email/Username"
      ></v-text-field>

      <v-text-field
         @blur="$v.password.$touch()"
         :error-messages="passwordErrors"
         v-model="password"
         label="Password"
         type="password"
         class="mb-2"
      ></v-text-field>
      <v-btn type="submit" class="font-weight-bold primary px-10">Login</v-btn>

      <router-link exact :to="{ name: 'auth.forgotPassword' }">
         <a style="color: grey" class="ml-2 caption nobr">Forgot Password?</a>
      </router-link>
   </form>
</template>

<script>
import { required, minLength } from 'vuelidate/lib/validators';

export default {
   data() {
      return {
         usernameOrEmail: '',
         password: '',
      };
   },
   validations: {
      usernameOrEmail: { required },
      password: { required, minLength: minLength(10) },
   },
   computed: {
      usernameOrEmailErrors() {
         const errors = [];
         if (!this.$v.usernameOrEmail.$dirty) return errors;
         !this.$v.usernameOrEmail.required && errors.push('Email required');
         return errors;
      },
      passwordErrors() {
         const errors = [];
         if (!this.$v.password.$dirty) return errors;
         !this.$v.password.required && errors.push('Password required');
         !this.$v.password.minLength && errors.push('Password needs to be at least 10 characters.');
         return errors;
      },
   },
   methods: {
      async signIn() {
         this.$v.$touch();
         if (this.$v.$anyError) return;

         try {
            const { usernameOrEmail, password } = this;
            const payload = { usernameOrEmail, password };
            const user = await this.$store.dispatch('signInUser', payload);
            this.$toast.success(`Welcome ${user.username}`);
            this.$router.push({ name: 'todo.index' });
         } 
         catch (exception) {
            this.$toast.error(exception);
         }
      },
   },
};
</script>