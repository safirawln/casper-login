<template>
  <div class="input-wrap" :class="{ 'has-error': !!error }">
    <span class="input-icon">
      <slot name="icon" />
    </span>

    <input
      :value="modelValue"
      :type="inputType"
      :placeholder="placeholder"
      :autocomplete="autocomplete"
      :style="isPassword ? 'padding-right: 42px' : ''"
      @input="$emit('update:modelValue', $event.target.value)"
      @keyup.enter="$emit('enter')"
    />

    <button
      v-if="isPassword"
      class="eye-btn"
      type="button"
      tabindex="-1"
      @click="showPass = !showPass"
    >
      <svg v-if="!showPass" width="15" height="15" viewBox="0 0 24 24" fill="none"
        stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
        <circle cx="12" cy="12" r="3"/>
      </svg>

      <svg v-else width="15" height="15" viewBox="0 0 24 24" fill="none"
        stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"/>
        <line x1="1" y1="1" x2="23" y2="23"/>
      </svg>
    </button>

    <div v-if="error" class="error-msg">
      <svg width="12" height="12" viewBox="0 0 24 24" fill="none"
        stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
        <circle cx="12" cy="12" r="10"/>
        <line x1="12" y1="8" x2="12" y2="12"/>
        <line x1="12" y1="16" x2="12.01" y2="16"/>
      </svg>
      {{ error }}
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  modelValue:   { type: String,  default: '' },
  placeholder:  { type: String,  default: '' },
  isPassword:   { type: Boolean, default: false },
  autocomplete: { type: String,  default: 'off' },
  error:        { type: String,  default: '' },
})

defineEmits(['update:modelValue', 'enter'])

const showPass = ref(false)

const inputType = computed(() => {
  if (!props.isPassword) return 'text'
  return showPass.value ? 'text' : 'password'
})
</script>

<style scoped>
.input-wrap {
  position: relative;
  display: flex;
  flex-direction: column;
}

.input-icon {
  position: absolute;
  top: 50%;
  left: 12px;
  transform: translateY(-50%);
  color: #484f58;
  display: flex;
  pointer-events: none;
  transition: color 0.2s;
  z-index: 1;
}

.input-wrap.has-error .input-icon { top: calc(50% - 10px); }

.input-wrap:focus-within .input-icon { color: #3b82f6; }

input {
  width: 100%;
  background: #1a2435;
  border: 1px solid rgba(255, 255, 255, 0.07);
  border-radius: 8px;
  padding: 10px 12px 10px 38px;
  font-family: 'Montserrat', sans-serif;
  font-size: 14px;
  color: #e6edf3;
  outline: none;
  transition: border-color 0.2s, background 0.2s, box-shadow 0.2s;
}

input::placeholder { color: #484f58; }

input:hover { background: #1e2a3d; }

input:focus {
  border-color: rgba(56, 139, 253, 0.5);
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.12);
  background: #1e2a3d;
}

.eye-btn {
  position: absolute;
  top: 50%;
  right: 10px;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
  color: #484f58;
  display: flex;
  padding: 4px;
  border-radius: 4px;
  transition: color 0.2s;
}
.eye-btn:hover { color: #8b949e; }

.input-wrap.has-error .eye-btn { top: calc(50% - 10px); }

.error-msg {
  display: flex;
  align-items: center;
  gap: 5px;
  margin-top: 6px;
  font-size: 12px;
  color: #f85149;
  animation: shake 0.3s ease;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25%       { transform: translateX(-4px); }
  75%       { transform: translateX(4px); }
}
</style>
