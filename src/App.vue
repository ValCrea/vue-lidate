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

let sucess = false;
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
      req: requiredIf(!sucess),
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
  sucess = await v$.value.$validate();
};
</script>

<template>
  <section class="vuelidate">
    <div v-for="(_, key, ind) in inputs" :key="key" class="vuelidate__inputs">
      <input
        type="text"
        v-model="inputs[key].value"
        :placeholder="lables[ind || -1] || 'Unknown'"
        class="vuelidate__input"
      />
      <p v-if="sucess" class="vuelidate__sucess">Sucess</p>
      <p v-else-if="v$[key].$error" class="vuelidate__error">Error</p>
      <p v-else-if="v$[key].$invalid" class="vuelidate__warning">Invalid</p>
      <p v-else class="vuelidate__valid">Valid</p>
      <br />
    </div>
    <button @click="submit">Submit</button>
  </section>
</template>

<style scoped lang="scss">
.vuelidate {
  max-width: 400px;
  width: 90%;
  padding: 3rem;
  margin-inline: auto;
  margin-block: 3rem;

  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;

  background-color: #f0f0f0;
  border-radius: 2rem;

  &__inputs {
    width: 100%;

    display: flex;
    justify-content: space-between;
    gap: 0.5rem;
  }

  &__input {
    width: 75%;
    padding: 0.2rem 0.4rem;
  }

  &__error {
    color: firebrick;
  }

  &__warning {
    color: orange;
  }

  &__valid {
    color: royalblue;
  }

  &__sucess {
    color: forestgreen;
  }
}
</style>
