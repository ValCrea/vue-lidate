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

let sucess = ref(false);
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
      req: requiredIf(!sucess.value),
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
  sucess.value = await v$.value.$validate();
};
</script>

<template>
  <h1 class="title">Vuelidate</h1>
  <section class="vuelidate">
    <div v-for="(_, key, ind) in inputs" :key="key" class="vuelidate__inputs">
      <input
        type="text"
        v-model="inputs[key].value"
        :placeholder="lables[ind !== undefined ? ind : -1] || 'Unknown'"
        class="vuelidate__input"
      />
      <p v-if="sucess" class="vuelidate__sucess">Sucess</p>
      <p v-else-if="v$[key].$error" class="vuelidate__error">Error</p>
      <p v-else-if="v$[key].$invalid" class="vuelidate__warning">Invalid</p>
      <p v-else class="vuelidate__valid">Valid</p>
      <br />
    </div>
    <button @click="submit" class="vuelidate__submit">Submit</button>
  </section>
</template>

<style scoped lang="scss">
:global(body) {
  background-color: #fdfdff;
}

.title {
  padding-top: 2rem;
  text-align: center;
  font-family: "Trebuchet MS", "Lucida Sans Unicode", "Lucida Grande",
    "Lucida Sans", Arial, sans-serif;
  font-size: 3rem;
  color: #0f0f5f;
}

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

  background-color: #f0f0f3;
  border-radius: 2rem;

  &__inputs {
    width: 100%;

    display: flex;
    justify-content: space-between;
    gap: 0.5rem;
  }

  &__input {
    flex: 1;
    padding: 0.2rem 0.4rem;

    border-radius: 0.2rem;
    border: 2px solid rgba(0, 0, 0, 0);
    background-color: #fafafe;

    &:focus {
      outline: none;
      border-color: #0f0f5f;
    }
  }

  &__text {
    width: 6ch;
    display: flex;
    align-items: center;
    justify-content: flex-end;
  }

  &__error {
    @extend .vuelidate__text;
    color: firebrick;
  }

  &__warning {
    @extend .vuelidate__text;
    color: orange;
  }

  &__valid {
    @extend .vuelidate__text;
    color: royalblue;
  }

  &__sucess {
    @extend .vuelidate__text;
    color: forestgreen;
  }

  &__submit {
    width: 50%;
    padding: 0.4rem;

    cursor: pointer;
    border: solid 2px #0f0f5f;
    border-radius: 0.5rem;
    background-color: #e5e5f5;

    &:hover {
      background-color: #d5d5e5;
    }

    &:focus {
      background-color: #c5c5d5;
    }
  }
}
</style>
