<template>
  <div class="login-card">
    <div v-if="loginSuccess" class="success-overlay">
      <div class="success-icon">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none"
          stroke="currentColor" stroke-width="2.5"
          stroke-linecap="round" stroke-linejoin="round">
          <polyline points="20 6 9 17 4 12"/>
        </svg>
      </div>
      <p class="success-title">Access granted</p>
      <p class="success-sub">Redirecting to dashboard…</p>
    </div>

    <template v-else>
      <div class="field">
        <div class="field-header">
          <label class="field-label">Username</label>
          <div class="tag-row">
            <button
              class="tag tag-ldap"
              :class="{ active: authMode === 'ldap' }"
              @click="authMode = 'ldap'"
            >LDAP</button>
            <button
              class="tag tag-isc"
              :class="{ active: authMode === 'isc' }"
              @click="authMode = 'isc'"
            >ISC Access</button>
          </div>
        </div>
        <BaseInput
          v-model="username"
          placeholder="Enter username"
          autocomplete="username"
          :error="errors.username"
          @enter="handleLogin"
          @update:modelValue="errors.username = ''"
        >
          <template #icon>
            <svg width="15" height="15" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2"
              stroke-linecap="round" stroke-linejoin="round">
              <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/>
              <circle cx="12" cy="7" r="4"/>
            </svg>
          </template>
        </BaseInput>
      </div>

      <div class="field">
        <div class="field-header">
          <label class="field-label">Password</label>
        </div>
        <BaseInput
          v-model="password"
          placeholder="Password"
          :is-password="true"
          autocomplete="current-password"
          :error="errors.password"
          @enter="handleLogin"
          @update:modelValue="errors.password = ''"
        >
          <template #icon>
            <svg width="15" height="15" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2"
              stroke-linecap="round" stroke-linejoin="round">
              <rect x="3" y="11" width="18" height="11" rx="2" ry="2"/>
              <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
            </svg>
          </template>
        </BaseInput>
      </div>

      <button class="btn-login" :disabled="loading" @click="handleLogin">
        <span v-if="loading" class="spinner" />
        {{ loading ? 'Authenticating…' : 'Login' }}
      </button>

      <button class="back-link" @click="resetForm">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none"
          stroke="currentColor" stroke-width="2"
          stroke-linecap="round" stroke-linejoin="round">
          <line x1="19" y1="12" x2="5" y2="12"/>
          <polyline points="12 19 5 12 12 5"/>
        </svg>
        Back to login
      </button>

    </template>
  </div>
</template>

<script setup>
import { defineEmits, ref, reactive } from 'vue'
import BaseInput from './BaseInput.vue'

const emit = defineEmits(['login-success'])
const username     = ref('')
const password     = ref('')
const authMode     = ref('isc')
const loading      = ref(false)
const loginSuccess = ref(false)

const errors = reactive({ username: '', password: '' })

function validate() {
  let valid = true
  errors.username = ''
  errors.password = ''

  if (!username.value.trim()) {
    errors.username = 'Username is required.'
    valid = false
  }
  if (!password.value) {
    errors.password = 'Password is required.'
    valid = false
  }
  return valid
}

async function handleLogin() {
  if (loading.value || !validate()) return

  loading.value = true

  await new Promise(r => setTimeout(r, 1400))
  loading.value = false
  loginSuccess.value = true
  setTimeout(() => emit('login-success'), 700)
}

function resetForm() {
  username.value = ''
  password.value = ''
  errors.username = ''
  errors.password = ''
  loginSuccess.value = false
}
</script>

<style scoped>
.login-card {
  width: 100%;
  max-width: 360px;
  background: #161f2e;
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 12px;
  padding: 32px 28px;
  box-shadow:
    0 24px 64px rgba(0, 0, 0, 0.5),
    0 0 0 1px rgba(255, 255, 255, 0.03);
  animation: fadeUp 0.7s 0.1s ease both;
}


.field { margin-bottom: 18px; }

.field-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 8px;
}

.field-label {
  font-size: 13px;
  font-weight: 600;
  color: #e6edf3;
}

.tag-row { display: flex; gap: 6px; }

.tag {
  font-family: 'Montserrat', sans-serif;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  padding: 2px 8px;
  border-radius: 4px;
  cursor: pointer;
  transition: opacity 0.15s;
  border: 1px solid transparent;
}

.tag-ldap {
  background: rgba(139, 148, 158, 0.12);
  color: #8b949e;
  border-color: rgba(139, 148, 158, 0.2);
}

.tag-isc {
  background: rgba(37, 99, 235, 0.18);
  color: #58a6ff;
  border-color: rgba(88, 166, 255, 0.3);
}

.tag.active  { outline: 1px solid currentColor; }
.tag:hover   { opacity: 0.75; }

.btn-login {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  width: 100%;
  padding: 11px;
  background: #2563eb;
  border: none;
  border-radius: 8px;
  font-family: 'Montserrat', sans-serif;
  font-size: 15px;
  font-weight: 600;
  color: #fff;
  cursor: pointer;
  letter-spacing: 0.04em;
  margin-top: 8px;
  transition: background 0.2s, box-shadow 0.2s, transform 0.1s;
  position: relative;
  overflow: hidden;
}

.btn-login::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, rgba(255,255,255,0.09) 0%, transparent 100%);
  pointer-events: none;
}

.btn-login:hover:not(:disabled) {
  background: #3b82f6;
  box-shadow: 0 4px 20px rgba(37, 99, 235, 0.4);
}

.btn-login:active:not(:disabled) { transform: scale(0.98); }
.btn-login:disabled { opacity: 0.6; cursor: not-allowed; }

.spinner {
  display: inline-block;
  width: 13px;
  height: 13px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-top-color: #fff;
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
  flex-shrink: 0;
}

.back-link {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  margin-top: 20px;
  width: 100%;
  font-size: 13px;
  color: #8b949e;
  background: none;
  border: none;
  cursor: pointer;
  font-family: 'Montserrat', sans-serif;
  transition: color 0.2s;
}
.back-link:hover { color: #e6edf3; }

.success-overlay {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 14px;
  padding: 16px 0;
  animation: fadeUp 0.4s ease both;
}

.success-icon {
  width: 52px;
  height: 52px;
  background: rgba(35, 134, 54, 0.15);
  border: 1px solid rgba(35, 134, 54, 0.4);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #3fb950;
}

.success-title {
  font-size: 15px;
  font-weight: 500;
  color: #e6edf3;
}

.success-sub {
  font-size: 13px;
  color: #8b949e;
}

@keyframes fadeUp {
  from { opacity: 0; transform: translateY(14px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
</style>
