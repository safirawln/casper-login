<template>
  <aside :class="['sidebar', { collapsed }]">

    <button class="toggle-circle" @click="$emit('toggle')">
      <Icon
        :icon="collapsed ? 'mdi:chevron-right' : 'mdi:chevron-left'"
        width="16"
      />
    </button>


    <div class="brand">
      <div class="brand-icon">
        <Icon icon="mdi:tune-vertical" width="20" color="white" />
      </div>
      <span class="brand-name" v-show="!collapsed">Push Config</span>
    </div>

    <nav class="nav-body">
      <template v-for="group in menuGroups" :key="group.label">
        <p class="group-label" v-show="!collapsed">{{ group.label }}</p>
        <div v-for="item in group.items" :key="item.name" class="nav-row">
          <a
            href="#"
            class="nav-item"
            :class="{ active: active === item.name }"
            @click.prevent="active = item.name"
          >
            <Icon :icon="item.icon" class="nav-ic" width="16" />
            <span class="nav-txt" v-show="!collapsed">{{ item.name }}</span>
            <span v-if="item.badge" class="nav-badge" v-show="!collapsed">{{ item.badge }}</span>
          </a>
          <div class="tooltip" v-show="collapsed">
            {{ item.name }}
            <span v-if="item.badge" class="tooltip-badge">{{ item.badge }}</span>
          </div>
        </div>
      </template>
    </nav>

    <div class="sidebar-foot">
      <div class="nav-row">
        <a href="#" class="nav-item" @click.prevent="$emit('toggle-theme')">
          <Icon :icon="isDark ? 'mdi:weather-sunny' : 'mdi:weather-night'" class="nav-ic" width="16" />
          <span class="nav-txt" v-show="!collapsed">{{ isDark ? 'Light Mode' : 'Dark Mode' }}</span>
        </a>
        <div class="tooltip" v-show="collapsed">{{ isDark ? 'Light Mode' : 'Dark Mode' }}</div>
      </div>

      <div class="user-row" :class="{ 'user-center': collapsed }">
        <div class="user-av">A</div>
        <div class="user-info" v-show="!collapsed">
          <p class="user-name">Administrator</p>
          <p class="user-email">admin@pushconfig.io</p>
        </div>
        <button class="logout-btn" v-show="!collapsed" title="Logout">
          <Icon icon="mdi:logout-variant" width="14" />
        </button>
      </div>
    </div>

  </aside>
</template>

<script setup>
import { ref, computed } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  collapsed: Boolean,
  theme: String
})
defineEmits(['toggle', 'toggle-theme'])

const isDark = computed(() => props.theme === 'dark')
const active = ref('Sessions')

const menuGroups = [
  {
    label: 'DASHBOARD',
    items: [
      { name: 'Sessions',  icon: 'vaadin:line-bar-chart' },
      { name: 'Jobs',      icon: 'mdi:briefcase-clock-outline' },
      { name: 'Workers',   icon: 'uil:processor' },
    ]
  },
  {
    label: 'INBOX',
    items: [
      { name: 'Approval Queue',   icon: 'fluent:mail-inbox-all-20-regular', badge: 4 },
      { name: 'My Requests',      icon: 'uil:clipboard-notes' },
      { name: 'Approval History', icon: 'material-symbols:clock-loader-60-sharp' },
    ]
  },
  {
    label: 'OPERATIONS',
    items: [
      { name: 'Workflow Catalog',    icon: 'hugeicons:workflow-square-09' },
      { name: 'Job History',         icon: 'mdi:clipboard-text-clock-outline' },
      { name: 'Interactive Session', icon: 'mdi:console' },
      { name: 'Command Directory',   icon: 'material-symbols:code' },
    ]
  },
  {
    label: 'PARAMETER',
    items: [
      { name: 'Devices',   icon: 'uil:wifi-router' },
      { name: 'Commands',  icon: 'ph:brackets-curly' },
      { name: 'Brands',    icon: 'tdesign:device' },
      { name: 'Workflows', icon: 'mdi:sitemap-outline' },
      { name: 'Variables', icon: 'material-symbols:variable-insert-outline-rounded' },
      { name: 'Functions', icon: 'mdi:sigma' },
    ]
  },
  {
    label: 'ADMINISTRATION',
    items: [
      { name: 'Users',         icon: 'mdi:users-outline' },
      { name: 'Permissions',   icon: 'material-symbols:shield-outline' },
      { name: 'Roles',         icon: 'mdi:shield-account-outline' },
      { name: 'Organizations', icon: 'fluent:building-multiple-20-regular' },
      { name: 'Audit Log',     icon: 'pajamas:log' },
    ]
  },
]
</script>

<style scoped>
.sidebar {
  position: fixed;
  top: 0; left: 0; bottom: 0;
  width: 220px;
  background: var(--surface);
  border-right: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  z-index: 100;
  overflow: visible;
  transition: width 0.22s cubic-bezier(.4,0,.2,1);
}
.sidebar.collapsed { width: 56px; }

.toggle-circle {
  position: absolute;
  top: 80px;
  right: -14px;
  width: 28px;
  height: 28px;
  border-radius: 40%;
  background: #fff;
  border: 1px solid var(--border);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: var(--muted);
  box-shadow: 0 4px 10px rgba(0,0,0,0.08);
  transition: 0.2s;
}
.toggle-circle:hover {
  background: var(--surface-2);
  color: var(--text);
  transform: scale(1.05);
}


.brand {
  height: 52px;
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 0 12px;
  border-bottom: 1px solid var(--border);
  flex-shrink: 0;
}
.brand-icon {
  width: 32px; height: 32px;
  background: linear-gradient(145deg, #2563eb, #1d4ed8);
  border-radius: 9px;
  display: flex; align-items: center; justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.4);
}
.brand-name {
  flex: 1;
  font-size: 13.5px;
  font-weight: 700;
  color: var(--text);
  white-space: nowrap;
  overflow: hidden;
}

/* Nav */
.nav-body {
  flex: 1;
  overflow-y: auto;
  overflow-x: hidden;
  padding: 6px 0;
  scrollbar-width: thin;
  scrollbar-color: var(--border) transparent;
}

.group-label {
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 0.1em;
  color: var(--dimmed);
  padding: 10px 14px 3px;
  white-space: nowrap;
  overflow: hidden;
  margin: 0;
}

.nav-row { position: relative; }

.nav-item {
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 7px 12px;
  margin: 1px 6px;
  border-radius: 7px;
  text-decoration: none;
  color: var(--muted);
  font-size: 13px;
  font-weight: 500;
  white-space: nowrap;
  cursor: pointer;
  transition: background 0.15s, color 0.15s;
}
.nav-item:hover { background: var(--surface-2); color: var(--text); }
.nav-item.active {
  background: var(--accent-bg);
  color: var(--accent);
  font-weight: 600;
}

.nav-ic { flex-shrink: 0; }
.nav-txt { flex: 1; overflow: hidden; text-overflow: ellipsis; }

.nav-badge {
  background: rgb(245, 158, 11);
  color: #fff;
  font-size: 10px;
  font-weight: 700;
  padding: 2px 6px;
  border-radius: 5px;
  min-width: 18px;
  text-align: center;
  margin-left: auto;
}

/* Tooltip */
.tooltip {
  display: flex;
  align-items: center;
  gap: 6px;
  position: absolute;
  left: calc(56px + 6px);
  top: 50%;
  transform: translateY(-50%);
  background: var(--text);
  color: var(--bg);
  font-size: 12px;
  font-weight: 600;
  padding: 5px 10px;
  border-radius: 6px;
  white-space: nowrap;
  z-index: 200;
  pointer-events: none;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.15s, visibility 0.15s;
}
.tooltip::before {
  content: '';
  position: absolute;
  left: -5px; top: 50%;
  transform: translateY(-50%);
  border: 5px solid transparent;
  border-right-color: var(--text);
  border-left: 0;
}
.tooltip-badge {
  background: #ef4444;
  color: #fff;
  font-size: 10px;
  padding: 0 5px;
  border-radius: 8px;
}
.sidebar.collapsed .nav-row:hover .tooltip {
  opacity: 1;
  visibility: visible;
}

/* Footer */
.sidebar-foot {
  border-top: 1px solid var(--border);
  padding: 6px 0 0;
  flex-shrink: 0;
}

.user-row {
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 9px 12px;
  cursor: default;
}
.user-center { justify-content: center; padding: 9px 0; }
.user-av {
  width: 28px; height: 28px;
  background: var(--accent);
  border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: 11px;
  font-weight: 700;
  color: #fff;
  flex-shrink: 0;
}
.user-info { flex: 1; overflow: hidden; }
.user-name {
  font-size: 12px;
  font-weight: 600;
  color: var(--text);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  margin: 0;
}
.user-email {
  font-size: 11px;
  color: var(--muted);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  margin: 0;
}
.logout-btn {
  background: none;
  border: none;
  color: var(--dimmed);
  cursor: pointer;
  display: flex;
  padding: 3px;
  border-radius: 4px;
  transition: color 0.15s;
}
.logout-btn:hover { color: var(--red); }
</style>