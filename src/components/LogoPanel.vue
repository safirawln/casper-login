<template>
  <div class="left-panel">
    <div class="glow" />
    <div class="logo-wrap">
      <div class="logo-icon">
        <Icon icon="mdi:tune-vertical" width="38" height="38" color="white" />
      </div>
      <div class="logo-name">CASPER</div>
      <div class="logo-tagline">
        Centralized Automation System &amp;<br />
        Platform for Network Resilience
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue'
</script>

<style scoped>
.left-panel {
  position: relative;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background: #0d1521;
}

.glow {
  position: absolute;
  inset: 0;
  background:
    radial-gradient(ellipse 55% 50% at 100% 0%, rgba(255, 255, 255, 0.06) 0%, transparent 65%),
    radial-gradient(ellipse 55% 50% at 0% 100%, rgba(255, 255, 255, 0.06) 0%, transparent 65%),
    radial-gradient(ellipse 70% 60% at 40% 50%, rgba(37, 99, 235, 0.1) 0%, transparent 70%);
  pointer-events: none;
}

.logo-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  animation: fadeUp 0.7s ease both;
  z-index: 1;
}

.logo-icon {
  width: 72px;
  height: 72px;
  background: linear-gradient(145deg, #2563eb, #1d4ed8);
  border-radius: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.12),
    0 8px 32px rgba(37, 99, 235, 0.45);
}

.logo-name {
  font-family: 'Poppins', sans-serif;
  font-size: 36px;
  font-weight: 700;
  letter-spacing: 0.22em;
  color: #e6edf3;
}

.logo-tagline {
  font-family: 'Barlow Condensed', sans-serif;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 0.12em;
  color: #8ba8c8;
  text-transform: uppercase;
  text-align: center;
  line-height: 1.6;
  max-width: 240px;
}

@keyframes fadeUp {
  from { opacity: 0; transform: translateY(14px); }
  to   { opacity: 1; transform: translateY(0); }
}
</style>