<template>
  <div class="container mt-5">
    <div class="row justify-content-center">
      
      <div class="col-12 col-sm-10 col-md-8 col-lg-6">
        <h1 class="text-center mb-3">User Information Form</h1>

        <form @submit.prevent="submitForm" novalidate>
  <div class="row g-3">
    <!-- Username -->
    <div class="col-12 col-md-6">
      <label for="username" class="form-label">Username</label>
      <input
        id="username"
        type="text"
        class="form-control"
        v-model="formData.username"
        :class="{ 'is-invalid': errors.username }"
        @input="validateName(false)"
        @blur="validateName(true)"
        aria-invalid="true"
      />
      <div v-if="errors.username" class="invalid-feedback d-block">
        {{ errors.username }}
      </div>
    </div>

    <!-- Password -->
    <div class="col-12 col-md-6">
      <label for="password" class="form-label">Password</label>
      <input
        id="password"
        type="password"
        class="form-control"
        v-model="formData.password"
        :class="{ 'is-invalid': errors.password }"
        @input="validatePassword(false)"
        @blur="validatePassword(true)"
        aria-invalid="true"
      />
      <div v-if="errors.password" class="invalid-feedback d-block">
        {{ errors.password }}
      </div>
    </div>

    <!-- Australian Resident? -->
    <div class="col-12 col-md-6">
      <div class="form-check mt-1 mt-md-4">
        <input
          id="isAustralian"
          type="checkbox"
          class="form-check-input"
          v-model="formData.isAustralian"
          :class="{ 'is-invalid': errors.resident }"
          @change="validateResident(true)"
          aria-invalid="true"
        />
        <label for="isAustralian" class="form-check-label ms-2">Australian Resident?</label>
      </div>
      <div v-if="errors.resident" class="invalid-feedback d-block">
        {{ errors.resident }}
      </div>
    </div>

    <!-- Gender -->
    <div class="col-12 col-md-6">
      <label for="gender" class="form-label">Gender</label>
      <select
        id="gender"
        class="form-select"
        v-model="formData.gender"
        :class="{ 'is-invalid': errors.gender }"
        @change="validateGender(true)"
        @blur="validateGender(true)"
        aria-invalid="true"
      >
        <option value="">Select…</option>
        <option value="male">Male</option>
        <option value="female">Female</option>
        <option value="other">Other</option>
      </select>
      <div v-if="errors.gender" class="invalid-feedback d-block">
        {{ errors.gender }}
      </div>
    </div>

    <!-- Reason -->
    <div class="col-12">
      <label for="reason" class="form-label">Reason for joining</label>
      <textarea
        id="reason"
        rows="3"
        class="form-control"
        v-model="formData.reason"
        :class="{ 'is-invalid': errors.reason }"
        @input="validateReason(false)"
        @blur="validateReason(true)"
        aria-invalid="true"
      ></textarea>
      <div v-if="errors.reason" class="invalid-feedback d-block">
        {{ errors.reason }}
      </div>
    </div>

    <!-- Actions -->
    <div class="col-12 d-flex gap-2 justify-content-end">
      <button type="submit" class="btn btn-primary">Submit</button>
      <button type="button" class="btn btn-secondary" @click="clearForm">Clear</button>
    </div>
  </div>
</form>
<!-- PrimeVue DataTable -->
<DataTable
  v-if="submittedCards.length"
  :value="submittedCards"
  class="mt-4"
  stripedRows
  paginator
  :rows="5"
  dataKey="username"
  :rowsPerPageOptions="[5,10,20]"
>
  <Column field="username" header="Username" sortable />
  <Column field="password" header="Password" />
  <Column header="Australian Resident?">
    <template #body="{ data }">
      {{ data.isAustralian ? 'Yes' : 'No' }}
    </template>
  </Column>
  <Column field="gender" header="Gender" sortable />
  <Column header="Reason">
    <template #body="{ data }">
      <span style="white-space: pre-wrap">{{ data.reason }}</span>
    </template>
  </Column>
</DataTable>



        <!-- Cards (Activity 6.2) -->
        <!-- <div class="row mt-5" v-if="submittedCards.length">
          <div class="d-flex flex-wrap justify-content-start">
            <div
              v-for="(card, index) in submittedCards"
              :key="index"
              class="card m-2"
              style="width: 18rem;"
            >
              <div class="card-header">User Information</div>
              <ul class="list-group list-group-flush">
                <li class="list-group-item">Username: {{ card.username }}</li>
                <li class="list-group-item">Password: {{ card.password }}</li>
                <li class="list-group-item">
                  Australian Resident: {{ card.isAustralian ? 'Yes' : 'No' }}
                </li>
                <li class="list-group-item">Gender: {{ card.gender }}</li>
                <li class="list-group-item">Reason: {{ card.reason }}</li>
              </ul>
            </div>
          </div>
        </div> -->
        <!-- /Cards -->
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import DataTable from 'primevue/datatable'
import Column from 'primevue/column'

const oxfordJoin = (arr) =>
  arr.length <= 1 ? (arr[0] || '')
  : arr.length === 2 ? `${arr[0]} and ${arr[1]}`
  : `${arr.slice(0, -1).join(', ')} and ${arr[arr.length - 1]}`

const formData = ref({
  username: '',
  password: '',
  isAustralian: false,
  reason: '',
  gender: ''
})

const submittedCards = ref([])

const errors = ref({
  username: null,
  password: null,
  gender: null,
  resident: null,
  reason: null,
})

// Username ≥ 3
const validateName = (show) => {
  const v = (formData.value.username || '').trim()
  errors.value.username = v.length < 3 && show
    ? 'Name must be at least 3 characters.'
    : null
}

// Password: 8+ incl upper, lower, number, special
const validatePassword = (show) => {
  const p = formData.value.password || ''
  const missing = []
  if (p.length < 8) missing.push('8+ characters')
  if (!/[A-Z]/.test(p)) missing.push('an uppercase letter')
  if (!/[a-z]/.test(p)) missing.push('a lowercase letter')
  if (!/\d/.test(p))   missing.push('a number')
  if (!/[^A-Za-z0-9]/.test(p)) missing.push('a special character')

  errors.value.password = (missing.length && show)
    ? `Password needs ${oxfordJoin(missing)}.`
    : null
}

const validateGender = (show) => {
  errors.value.gender = !formData.value.gender && show
    ? 'Please select a gender.'
    : null
}

const validateResident = (show) => {
  errors.value.resident = !formData.value.isAustralian && show
    ? 'Please confirm residency.'
    : null
}

const validateReason = (show) => {
  const r = (formData.value.reason || '').trim()
  errors.value.reason = r.length < 10 && show
    ? 'Please write at least 10 characters.'
    : null
}

// Run all validators; return true if any error exists
const runAllValidations = (show = true) => {
  validateName(show)
  validatePassword(show)
  validateGender(show)
  validateResident(show)
  validateReason(show)
  return Object.values(errors.value).some(Boolean)
}

const submitForm = () => {
  // show all messages on submit
  validateName(true);
  validatePassword(true);
  validateGender(true);
  validateResident(true);
  validateReason(true);

  const hasErrors = Object.values(errors.value).some(Boolean);
  if (hasErrors) return;

  submittedCards.value.push({ ...formData.value });
  clearForm(); 
};

const clearForm = () => {
  formData.value = { username: '', password: '', isAustralian: false, reason: '', gender: '' }
 
  Object.keys(errors.value).forEach(k => (errors.value[k] = null))
}
</script>

<style scoped>
.card {
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.card-header {
  background-color: #275fda;
  color: white;
  padding: 10px;
  border-radius: 10px 10px 0 0;
}
.list-group-item { padding: 10px; }
</style>
