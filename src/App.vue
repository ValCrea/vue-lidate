<script setup lang="ts">
import { ref, computed } from "vue";
import { useVuelidate } from "@vuelidate/core";
import { helpers } from "@vuelidate/validators";
import {
  requiredIf,
  minLength,
  maxLength,
  minValue,
  maxValue,
  between,
  alpha,
  numeric,
  alphaNum,
  integer,
  decimal,
  email,
  ipAddress,
  macAddress,
  sameAs,
  url,
} from "@vuelidate/validators";

const condition = true;
const customValidator = (value: string) =>
  !helpers.req(value) || value.includes("vue");

const requirements = [
  minLength(5),
  maxLength(5),
  minValue(10),
  maxValue(10),
  between(1, 10),
  alpha,
  numeric,
  alphaNum,
  integer,
  decimal,
  email,
  ipAddress,
  macAddress(""), // Argument is custom seperator
  sameAs("sameAs"),
  url,
  customValidator,
];
const lables: any = [
  "minLength",
  "maxLength",
  "minValue",
  "maxValue",
  "between",
  "alpha",
  "numeric",
  "alphaNum",
  "integer",
  "decimal",
  "email",
  "ipAddress",
  "macAddress",
  "sameAs",
  "url",
  "customValidator",
];

const inputs: any = {};
requirements.forEach((_, ind) => (inputs[`input${ind}`] = ref()));

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
  <!--Vue error? key isnt number and ind isnt unidentified-->
  <div v-for="(_, key, ind) in inputs" :key="key">
    <input type="text" v-model="inputs[key].value" :placeholder="lables[ind]" />
    <p v-if="v$[key].$error">Error</p>
    <p v-else-if="v$[key].$invalid">Invalid</p>
    <p v-else>Valid</p>
    <br />
  </div>
  <button @click="submit">Submit</button>
</template>

<style scoped lang="scss"></style>
