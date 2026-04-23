<template>
  <div class="page">
    <div class="bg-dots" aria-hidden="true" />
    <LogoPanel />
    <div class="right-panel">
      <div class="right-inner">
        <LoginForm />
        <p class="footer">CASPER © 2024 ENTERPRISE EDITION</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import LogoPanel from './LogoPanel.vue'
import LoginForm from './LoginForm.vue'
</script>

<style scoped>
.page {
  display: grid;
  grid-template-columns: 1fr 1.5fr;

  @media (max-width: 768px) {
    grid-template-columns: 1fr;
  }

  min-height: 100vh;
  position: relative;
  overflow: hidden;
  background: #0d1521;
}

.bg-dots {
  position: fixed;
  inset: 0;
  background-image: radial-gradient(
    circle,
    rgba(255, 255, 255, 0.03) 1px,
    transparent 1px
  );
  background-size: 28px 28px;
  pointer-events: none;
  z-index: 0;
}

.right-panel {
  position: relative;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 40px;
  border-left: 1px solid rgba(255, 255, 255, 0.07);
}

.right-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  width: 100%;
  max-width: 360px;
}

.footer {
  font-family: 'Montserrat', sans-serif;
  font-size: 11px;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: #484f58;
  text-align: center;
  margin: 0;
}
</style>