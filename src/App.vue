<script setup lang="ts">
import { ref, computed } from "vue";
import { useVuelidate } from "@vuelidate/core";
import { required } from "@vuelidate/validators";

const inputs = { input1: ref(""), input2: ref("") };
const rules = computed(() => {
  const rules: any = {};

  Object.keys(inputs).forEach((key) => {
    rules[key] = {
      required,
    };
  });

  return rules;
});
const v$ = useVuelidate(rules, inputs);

const submit = async () => {
  if (await v$.value.$validate()) console.log("Valid");
  else console.log("Invalid");
};
</script>

<template>
  <div v-for="(_, key) in inputs" :key="key">
    <input type="text" v-model="inputs[key].value" />
    <p>{{ v$[key].$dirty }} {{ v$[key].$invalid }} {{ v$[key].$error }}</p>
    <br />
  </div>
  <button @click="submit">Submit</button>
</template>

<style scoped lang="scss"></style>
