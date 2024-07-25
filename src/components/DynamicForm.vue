<script setup lang="ts">
import { ref, reactive } from 'vue'
import InputComponents from '@/components/InputComponent.vue'
const ALL_QUESTIONS = [
  {
    step: 1,
    title: 'Personal Information',
    description: 'Please fill out your personal information',
    fields: [
      {
        type: 'textfield',
        label: 'Name',
        placeholder: 'Enter your name',
        required: true
      },
      {
        type: 'radio',
        label: 'Gender',
        options: [
          {
            label: 'Male',
            value: 'male'
          },
          {
            label: 'Female',
            value: 'female'
          },
          {
            label: 'Other',
            value: 'other'
          }
        ],
        required: true
      }
    ]
  },
  {
    step: 2,
    title: 'Additional Information',
    description: 'Please provide additional details',
    fields: [
      {
        type: 'textarea',
        label: 'Description',
        placeholder: 'Enter a description',
        required: false
      },
      {
        type: 'autocomplete',
        label: 'Title',
        placeholder: 'Enter a title',
        options: ['Mr.', 'Mrs.', 'Ms.', 'Dr.', 'Prof.'],
        required: true
      }
    ]
  }
]

const currentStep = ref(1)
const questions = ref(ALL_QUESTIONS.find((q) => q.step === currentStep.value))
const maxStep = ALL_QUESTIONS.length

const formData = reactive({})
ALL_QUESTIONS.forEach((question) => {
  question.fields.forEach((field) => {
    formData[field.label] = ''
  })
})

function changeQuestion(isIncrement: boolean) {
  if (isIncrement && currentStep.value < maxStep) {
    if (!validateCurrentStep()) return
    currentStep.value++
  } else if (!isIncrement && currentStep.value > 1) {
    currentStep.value--
  }
  questions.value = ALL_QUESTIONS.find((q) => q.step === currentStep.value)
}

function validateCurrentStep() {
  const currentFields = questions.value.fields
  let isValid = true
  for (const field of currentFields) {
    if (field.required && !formData[field.label]) {
      alert(`Please fill out the ${field.label}.`)
      isValid = false
      break
    }
  }
  return isValid
}

async function handleSubmitForm() {
  console.log('Form submitted', formData)
}
</script>

<template>
  <div class="bg-white shadow-lg mx-auto max-w-4xl w-full p-5 rounded-lg">
    <form class="space-y-4 mt-4" @submit.prevent="handleSubmitForm">
      <h1 class="text-lg font-bold">{{ questions.title }}</h1>
      <p class="text-sm font-light">{{ questions.description }}</p>
      <div v-for="field in questions.fields" :key="field.label">
        <label :for="field.label" class="block mb-2 text-sm font-medium text-gray-900">{{
          field.label
        }}</label>
        <InputComponents
          v-if="field.type === 'textfield'"
          v-model="formData[field.label]"
          :placeholder="field.placeholder"
          :required="field.required"
        />
        <textarea
          v-if="field.type === 'textarea'"
          v-model="formData[field.label]"
          :placeholder="field.placeholder"
          :required="field.required"
          class="block w-full p-2 border border-gray-300 rounded-md"
        ></textarea>
        <div v-if="field.type === 'radio'" class="flex flex-col">
          <div v-for="option in field.options" :key="option.value" class="flex items-center">
            <input
              type="radio"
              :id="option.value"
              :value="option.value"
              v-model="formData[field.label]"
              :name="field.label"
              :required="field.required"
            />
            <label :for="option.value" class="ml-2">{{ option.label }}</label>
          </div>
        </div>
        <input
          v-if="field.type === 'autocomplete'"
          list="autocomplete-options"
          v-model="formData[field.label]"
          :placeholder="field.placeholder"
          :required="field.required"
          class="block w-full p-2 border border-gray-300 rounded-md"
        />
        <datalist id="autocomplete-options">
          <option v-for="option in field.options" :key="option" :value="option"></option>
        </datalist>
      </div>
      <div class="flex flex-row justify-end gap-5">
        <button
          class="bg-blue-500 hover:bg-blue-600 rounded-lg text-white p-2 px-4 mt-4"
          type="button"
          @click="changeQuestion(false)"
          v-if="currentStep > 1"
        >
          Prev
        </button>
        <button
          type="button"
          @click="changeQuestion(true)"
          class="bg-blue-500 hover:bg-blue-600 rounded-lg text-white p-2 px-4 mt-4"
          v-if="currentStep < maxStep"
        >
          Next
        </button>
        <button
          class="bg-blue-500 hover:bg-blue-600 rounded-lg text-white p-2 px-4 mt-4"
          type="submit"
          v-if="currentStep === maxStep"
        >
          Submit
        </button>
      </div>
    </form>
  </div>
</template>
