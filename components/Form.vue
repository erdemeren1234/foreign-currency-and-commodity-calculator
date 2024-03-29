<script lang="ts" setup>
import { formDataType, formInputType } from "~~/types/types";
import usePreferences from "~~/storage/preferences";
import useStates from "~~/storage/states";

const { useAutoAnimate } = await import("@formkit/auto-animate/vue");
const states = storeToRefs(useStates());
const { inputGroupUUIDKeys } = states;
const [animate] = useAutoAnimate();
const { formSelectValues } = usePreferences();

const emit = defineEmits<{
  (e: "onFormSubmit"): void;
}>();

/**
 * Submit
 */
async function submit(formData: formDataType) {
  const regex = /^\d+(,\d+)?$/;

  const formInputs: formInputType[] = Object.values(formData).filter((input: formInputType) => {
    if (typeof input.marketUnit === "string" && typeof input.quantity === "string") {
      input.quantity = input.quantity.trim();
      if (regex.test(input.quantity)) {
        return input;
      }
    }
  });

  //if submitted with an empty input or unvalidated input, prevented submit behaviour
  if (formInputs.length === 0) {
    throw createError("Please input valid quantity value.");
  }

  useHandleFormSubmit(formInputs);
  emit("onFormSubmit");
}

onMounted(() => {
  //in order to prevent to pass different random uuid to inputUUIDKeys array and select input name
  useAddInputGroupUUID(useRandomUUID());
});
</script>

<template>
  <FormKit
    type="form"
    @submit="submit"
    :submit-attrs="{
      inputClass: 'submitButton disabled:remove-disabled',
    }"
  >
    <section ref="animate" class="grid grid-cols-2 max-[452px]:grid-cols-1">
      <section v-for="UUIDKey in inputGroupUUIDKeys" :key="UUIDKey" class="p-2 max-sm:p-1 caret-orange-900">
        <FormKit type="group" :name="UUIDKey">
          <section name="inputs" class="p-2 max-sm:p-1 max border-2 border-[rgb(8,77,69)] rounded-lg">
            <FormKit
              type="select"
              name="marketUnit"
              label="Select currency or commodity type"
              label-class="label"
              input-class="inputClass"
              outer-class="sm:my-1"
            >
              <optgroup v-for="{ label, options } in formSelectValues" :label="label">
                <option
                  class="font-['Tilt_Neon'] text-[#455015]"
                  v-for="{ value, text } in options"
                  :value="value"
                >
                  {{ text }}
                </option>
              </optgroup>
            </FormKit>

            <FormKit
              @keydown.enter.prevent=""
              type="text"
              name="quantity"
              label="Quantity"
              placeholder="Like 100 or 9,99"
              label-class="label"
              input-class="inputClass max-sm:placeholder:text-xs"
            />
          </section>

          <section name="buttons">
            <button
              @click.stop.prevent="useRemoveInputGroupUUID($event)"
              :data-key="UUIDKey"
              class="remove disabled:remove-disabled"
              :disabled="inputGroupUUIDKeys.length <= 1"
            >
              Remove
            </button>
            <button @click.stop.prevent="useAddInputGroupUUID(useRandomUUID())" class="add">Add</button>
          </section>
        </FormKit>
      </section>
    </section>
  </FormKit>
</template>
