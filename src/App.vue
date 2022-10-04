<script setup lang="ts">
import { ref, computed } from "vue";
import { useVuelidate } from "@vuelidate/core";
import { requiredIf, minLength, maxLength } from "@vuelidate/validators";

const condition = true;

const requirements = [minLength(5), maxLength(5)];
const inputs: any = {};
requirements.forEach((req, ind) => (inputs[`input${ind}`] = ref("")));

const rules = computed(() => {
  const rules: any = {};

  Object.keys(inputs).forEach((key, ind) => {
    rules[key] = {
      req: requiredIf(condition),
    };

    rules[key] = {
      ...rules[key],
      con: requirements[ind],
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
