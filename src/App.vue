<template>
  <div>
    <template v-if="!isAuthenticated">
      <CasperLogin @login-success="isAuthenticated = true" />
    </template>

    <template v-else>
      <div :class="['app-shell', theme]">
        <Sidebar
          :collapsed="sidebarCollapsed"
          :theme="theme"
          @toggle="sidebarCollapsed = !sidebarCollapsed"
          @toggle-theme="toggleTheme"
        />

        <div class="main-wrap" :class="{ collapsed: sidebarCollapsed }">
          <header class="topbar">
            <div class="topbar-search">
              <Icon icon="mdi:magnify" class="search-ic" />
              <input type="text" placeholder="Search devices, workflows, commands..." />
            </div>
            <div class="topbar-right">
              <button class="tb-btn tb-btn--outline">
                <Icon icon="mdi:console" />
                Start Interactive
              </button>
              <button class="tb-btn tb-btn--primary">
                <Icon icon="mdi:play" />
                Run Workflow
              </button>
              <button class="tb-icon-btn">
                <Icon icon="mdi:bell-outline" />
                <span class="notif-dot"></span>
              </button>
            </div>
          </header>

          <main class="page-content">
            <div class="breadcrumb">
              <span>Monitoring</span>
              <Icon icon="mdi:chevron-right" />
              <span class="bc-active">Sessions</span>
            </div>

            <div class="page-header">
              <div>
                <h1 class="page-title">Monitoring Session Dashboard</h1>
                <p class="page-sub">Real-time enterprise network session overview and control.</p>
              </div>
              <div class="page-header-right">
                <div class="live-badge">
                  <span class="live-dot"></span>
                  LIVE UPDATE
                </div>
                <button class="btn-new-session">
                  <Icon icon="mdi:plus" />
                  New Session
                </button>
              </div>
            </div>

            <div class="stats-grid">
              <div class="stat-card">
                <div class="stat-icon stat-icon--blue">
                  <Icon icon="material-symbols:sensors" width="20" />
                </div>
                <p class="stat-label">ACTIVE SESSIONS</p>
                <div class="stat-value">4</div>
                <div class="stat-trend trend-up">
                  <Icon icon="mdi:trending-up" />
                  +12% <span>vs last hour</span>
                </div>
              </div>

              <div class="stat-card">
                <div class="stat-icon stat-icon--orange">
                  <Icon icon="material-symbols:hourglass-top" width="20" />
                </div>
                <p class="stat-label">QUEUE LENGTH</p>
                <div class="stat-value">2</div>
                <div class="stat-trend trend-warn">
                  <Icon icon="mdi:trending-down" />
                  -5% <span>stabilizing</span>
                </div>
              </div>

              <div class="stat-card">
                <div class="stat-icon stat-icon--purple">
                  <Icon icon="mdi:view-grid-outline" width="20" />
                </div>
                <p class="stat-label">OCCUPANCY</p>
                <div class="occ-grid">
                  <div class="occ-row">
                    <span v-for="(d,i) in occRow1" :key="'a'+i" class="occ-dot" :class="d"></span>
                  </div>
                  <div class="occ-row">
                    <span v-for="(d,i) in occRow2" :key="'b'+i" class="occ-dot" :class="d"></span>
                  </div>
                </div>
                <p class="stat-sub">Peak density at 14:00 (High)</p>
              </div>

              <div class="stat-card">
                <div class="stat-icon stat-icon--red">
                  <Icon icon="mdi:alert-outline" width="20" />
                </div>
                <p class="stat-label">ALERTS</p>
                <div class="stat-value stat-value--red">2</div>
                <div class="stat-trend trend-danger">
                  <Icon icon="mdi:arrow-up" />
                  +1 <span>Requires attention</span>
                </div>
              </div>
            </div>

            <div class="table-card">
              <div class="table-toolbar">
                <div class="toolbar-left">
                  <div class="filter-wrap">
                    <Icon icon="mdi:filter-outline" class="filter-ic" />
                    <input v-model="filterText" type="text" placeholder="Filter by ID or User" />
                  </div>
                  <div class="sel-wrap">
                    <select v-model="filterMode">
                      <option value="All">Mode: All</option>
                      <option value="WRITE">WRITE</option>
                      <option value="VIEW">VIEW</option>
                    </select>
                    <Icon icon="mdi:chevron-down" class="sel-arrow" />
                  </div>
                  <div class="sel-wrap">
                    <select v-model="filterStatus">
                      <option value="All">Status: All</option>
                      <option value="ACTIVE">ACTIVE</option>
                      <option value="QUEUED">QUEUED</option>
                    </select>
                    <Icon icon="mdi:chevron-down" class="sel-arrow" />
                  </div>
                </div>
                <div class="toolbar-right">
                  <Icon icon="mdi:refresh" class="toolbar-icon" title="Refresh" />
                  <Icon icon="mdi:download-outline" class="toolbar-icon" title="Export CSV" />
                  <div class="toolbar-divider"></div>
                  <span class="show-count">Showing <strong>{{ filteredSessions.length }}</strong> sessions</span>
                </div>
              </div>

              <div class="table-scroll">
                <table class="data-table">
                  <thead>
                    <tr>
                      <th>SESSION ID</th>
                      <th>USER</th>
                      <th>DEVICE</th>
                      <th>MODE</th>
                      <th>STATUS</th>
                      <th>STARTED AT</th>
                      <th>LAST ACTIVITY</th>
                      <th>WORKER</th>
                      <th>REASON</th>
                      <th>ACTIONS</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="s in paginatedSessions" :key="s.id">
                      <td><a href="#" class="sess-link" @click.prevent>{{ s.id }}</a></td>
                      <td>
                        <div class="user-cell">
                          <div class="av" :style="{ background: '#1b2b3e'  }">{{ s.initials }}</div>
                          {{ s.user }}
                        </div>
                      </td>
                      <td>
                        <div class="device-cell">
                          <span class="device-ip">{{ s.ip }}</span>
                          <span class="device-name">{{ s.device }}</span>
                        </div>
                      </td>
                      <td>
                        <span class="mode-tag" :class="s.mode === 'WRITE' ? 'mode-write' : 'mode-view'">
                          {{ s.mode }}
                        </span>
                      </td>
                      <td>
                        <span class="status-tag" :class="s.status === 'ACTIVE' ? 'st-active' : 'st-queued'">
                          <span class="st-dot"></span>{{ s.status }}
                        </span>
                      </td>
                      <td class="mono">{{ s.startedAt }}</td>
                      <td class="mono muted">{{ s.lastActivity || '—' }}</td>
                      <td class="worker-plain">{{ s.worker }}</td>
                      <td class="muted reason-col">{{ s.reason }}</td>
                      <td>
                        <div class="row-actions">
                          <Icon icon="mdi:open-in-new" class="act-icon act-icon--open" width="20" title="Open" />
                          <Icon icon="mdi:close-circle-outline" class="act-icon act-icon--close" width="20" title="Terminate" />
                        </div>
                      </td>
                    </tr>
                    <tr v-if="filteredSessions.length === 0">
                      <td colspan="10" class="empty-td">No sessions found.</td>
                    </tr>
                  </tbody>
                </table>
              </div>

              <div class="table-footer">
                <span class="show-count">Showing {{ filteredSessions.length }} sessions</span>
                <div class="pager">
                  <button
                    v-for="p in totalPages" :key="p"
                    class="page-btn" :class="{ active: currentPage === p }"
                    @click="currentPage = p"
                  >{{ p }}</button>
                </div>
              </div>
            </div>

            <div class="status-bar">
              <div class="status-item">
                <span class="status-dot-green"></span>System Operational
              </div>
              <div class="status-item">
                <Icon icon="material-symbols:database-outline" width="13" />1.2ms latency
              </div>
              <div class="status-item">
                <Icon icon="uil:processor" width="13" />Worker Load: 42%
              </div>
              <span class="status-right">Last full sync: 13:21:44 UTC</span>
            </div>
          </main>
        </div>
      </div>
    </template>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { Icon } from '@iconify/vue'
import CasperLogin from './components/CasperLogin.vue'
import Sidebar from './components/Sidebar.vue'

const isAuthenticated = ref(false)
const theme = ref('light')
const sidebarCollapsed = ref(false)
const filterText = ref('')
const filterMode = ref('All')
const filterStatus = ref('All')
const currentPage = ref(1)
const pageSize = 10

function toggleTheme() {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
}

const sessions = ref([
  { id: 'sess_1qn0er', user: 'ahmad.rizky',    initials: 'AR', color: '#6366f1', ip: '180.250.225.248', device: 'R5.REN.PE-MOBILE.1', mode: 'WRITE', status: 'ACTIVE',  startedAt: '12:10:00', lastActivity: '2s ago',  worker: 'worker-node-01', reason: 'Config update #442' },
  { id: 'sess_e56y1k', user: 'budi.santoso',   initials: 'BS', color: '#f59e0b', ip: '119.98.9.137',    device: 'R2.BRN.RR-T5EL.1',  mode: 'VIEW',  status: 'QUEUED', startedAt: '13:15:00', lastActivity: null,      worker: 'worker-node-02', reason: 'Resource lock wait' },
  { id: 'sess_4dw1ek', user: 'dewi.lestari',   initials: 'DL', color: '#10b981', ip: '172.30.40.42',    device: 'ME-D1-PBRC-RR',      mode: 'VIEW',  status: 'ACTIVE',  startedAt: '14:20:00', lastActivity: '8s ago',  worker: 'worker-node-03', reason: 'Security policy audit' },
  { id: 'sess_x06ccx', user: 'eko.prasetyo',   initials: 'EP', color: '#3b82f6', ip: '172.30.193.17',   device: 'ME-D3-JBL',          mode: 'WRITE', status: 'ACTIVE',  startedAt: '15:25:00', lastActivity: '11s ago', worker: 'worker-node-04', reason: 'Routine monitoring' },
  { id: 'sess_cekana', user: 'fitri.handayani', initials: 'FH', color: '#ec4899', ip: '172.30.1.230',    device: 'ME-D1-SNBA',         mode: 'VIEW',  status: 'QUEUED', startedAt: '16:30:00', lastActivity: null,      worker: 'worker-node-01', reason: 'Manual review' },
  { id: 'sess_a6et3z', user: 'siti.nurhaliza',  initials: 'SN', color: '#8b5cf6', ip: '172.30.193.130',  device: 'ME2-D3-TTC',         mode: 'VIEW',  status: 'ACTIVE',  startedAt: '17:35:00', lastActivity: '17s ago', worker: 'worker-node-02', reason: 'Interface bounce' },
])

const filteredSessions = computed(() => sessions.value.filter(s => {
  const t = filterText.value.toLowerCase()
  return (!t || s.id.includes(t) || s.user.toLowerCase().includes(t)) &&
    (filterMode.value === 'All' || s.mode === filterMode.value) &&
    (filterStatus.value === 'All' || s.status === filterStatus.value)
}))

const totalPages = computed(() => Math.max(1, Math.ceil(filteredSessions.value.length / pageSize)))
const paginatedSessions = computed(() => {
  const start = (currentPage.value - 1) * pageSize
  return filteredSessions.value.slice(start, start + pageSize)
})

const occRow1 = ['od-4','od-4','od-3','od-4','od-4','od-3','od-2','od-2','od-1','od-1']
const occRow2 = ['od-3','od-2','od-4','od-4','od-3','od-3','od-2','od-1','od-1','od-1']

watch([filterText, filterMode, filterStatus], () => {
  currentPage.value = 1
})
</script>

<style scoped>
.app-shell {
  min-height: 100vh;
  display: flex;
  font-family: 'Montserrat', sans-serif;
  transition: background 0.25s, color 0.25s;
}

.app-shell.light {
  --bg: #f4f6f9;
  --surface: #ffffff;
  --surface-2: #f0f2f5;
  --border: rgba(0,0,0,0.08);
  --text: #111827;
  --muted: #6b7280;
  --dimmed: #9ca3af;
  --accent: #2563eb;
  --accent-bg: #eff6ff;
  --green: #16a34a; --green-bg: #dcfce7;
  --orange: #d97706; --orange-bg: #fef3c7;
  --red: #dc2626; --red-bg: #fee2e2;
  --shadow: 0 1px 4px rgba(0,0,0,0.07);
  --write-bg: #dbeafe; --write-text: #1d4ed8;
  --view-bg: #f3f4f6;  --view-text: #374151;
}

.app-shell.dark {
  --bg: #0f1623;
  --surface: #161f2e;
  --surface-2: #1a2540;
  --border: rgba(255,255,255,0.07);
  --text: #e2e8f0;
  --muted: #94a3b8;
  --dimmed: #4b5563;
  --accent: #3b82f6;
  --accent-bg: rgba(59,130,246,0.12);
  --green: #4ade80; --green-bg: rgba(74,222,128,0.12);
  --orange: #fbbf24; --orange-bg: rgba(251,191,36,0.12);
  --red: #f87171; --red-bg: rgba(248,113,113,0.12);
  --shadow: 0 2px 12px rgba(0,0,0,0.3);
  --write-bg: rgba(59,130,246,0.18); --write-text: #93c5fd;
  --view-bg: rgba(255,255,255,0.07); --view-text: #94a3b8;
}

.main-wrap {
  flex: 1;
  display: flex;
  flex-direction: column;
  margin-left: 220px;
  transition: margin-left 0.22s cubic-bezier(.4,0,.2,1);
  min-height: 100vh;
  background: var(--bg);
}
.main-wrap.collapsed { margin-left: 56px; }

.topbar {
  position: sticky;
  top: 0;
  z-index: 80;
  height: 52px;
  background: var(--surface);
  border-bottom: 1px solid var(--border);
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 0 20px;
  box-shadow: var(--shadow);
}

.topbar-search {
  flex: 1;
  max-width: 380px;
  position: relative;
  display: flex;
  align-items: center;
}
.search-ic {
  position: absolute;
  left: 9px;
  color: var(--dimmed);
  font-size: 15px;
}
.topbar-search input {
  width: 100%;
  height: 32px;
  padding: 0 10px 0 30px;
  background: var(--surface-2);
  border: 1px solid var(--border);
  border-radius: 8px;
  font-family: 'Montserrat', sans-serif;
  font-size: 12.5px;
  color: var(--text);
  outline: none;
}
.topbar-search input::placeholder { color: var(--dimmed); }
.topbar-search input:focus { border-color: var(--accent); }

.topbar-right {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-left: auto;
}

.tb-btn {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  height: 32px;
  padding: 0 12px;
  border: 1px solid var(--border);
  border-radius: 7px;
  background: var(--surface);
  color: var(--text);
  font-family: 'Montserrat', sans-serif;
  font-size: 12px;
  font-weight: 600;
  cursor: pointer;
  white-space: nowrap;
  transition: background 0.15s;
}
.tb-btn:hover { background: var(--surface-2); }
.tb-btn--primary { background: var(--accent); color: #fff; border-color: var(--accent); }
.tb-btn--primary:hover { opacity: 0.88; }
.tb-btn--outline { }

.tb-icon-btn {
  position: relative;
  width: 32px; height: 32px;
  display: flex; align-items: center; justify-content: center;
  background: none;
  border: 1px solid var(--border);
  border-radius: 8px;
  color: var(--muted);
  cursor: pointer;
  font-size: 16px;
  transition: background 0.15s;
}
.tb-icon-btn:hover { background: var(--surface-2); }
.notif-dot {
  position: absolute;
  top: 6px; right: 6px;
  width: 7px; height: 7px;
  background: var(--red);
  border-radius: 50%;
  border: 1.5px solid var(--surface);
}

.page-content {
  flex: 1;
  padding: 22px 26px;
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.breadcrumb {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12.5px;
  color: var(--dimmed);
}
.bc-active { color: var(--text); font-weight: 500; }

.page-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
  flex-wrap: wrap;
}
.page-title {
  font-size: 22px;
  font-weight: 700;
  color: var(--text);
  margin: 0 0 4px;
  letter-spacing: -0.3px;
}
.page-sub { font-size: 13px; color: var(--muted); margin: 0; }
.page-header-right { display: flex; align-items: center; gap: 10px; }

.live-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.07em;
  color: var(--green);
  background: var(--green-bg);
  padding: 4px 10px;
  border-radius: 20px;
}
.live-dot {
  width: 6px; height: 6px;
  background: var(--green);
  border-radius: 50%;
  animation: pulse 1.5s infinite;
}
@keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:.4;transform:scale(.75)} }

.btn-new-session {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  padding: 7px 14px;
  background: var(--accent);
  color: #fff;
  border: none;
  border-radius: 8px;
  font-family: 'Montserrat', sans-serif;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  transition: opacity 0.15s;
}
.btn-new-session:hover { opacity: 0.88; }

.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 14px;
}
@media (max-width: 1100px) { .stats-grid { grid-template-columns: repeat(2,1fr); } }

.stat-card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 12px;
  padding: 16px 18px;
  box-shadow: var(--shadow);
  position: relative;
  overflow: hidden;
}
.stat-icon {
  position: absolute;
  top: 16px; right: 16px;
  width: 36px; height: 36px;
  border-radius: 8px;
  display: flex; align-items: center; justify-content: center;
}
.stat-icon--blue   { background: var(--accent-bg); color: var(--accent); }
.stat-icon--orange { background: var(--orange-bg); color: var(--orange); }
.stat-icon--purple { background: rgba(139,92,246,.12); color: #8b5cf6; }
.stat-icon--red    { background: var(--red-bg); color: var(--red); }

.stat-label {
  font-size: 10.5px;
  font-weight: 700;
  letter-spacing: 0.08em;
  color: var(--muted);
  margin: 0 0 6px;
}
.stat-value {
  font-size: 32px;
  font-weight: 700;
  color: var(--text);
  line-height: 1;
  margin-bottom: 8px;
}
.stat-value--red { color: var(--red); }
.stat-sub { font-size: 11.5px; color: var(--muted); margin: 4px 0 0; }

.stat-trend {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  font-weight: 600;
}
.stat-trend span { color: var(--muted); font-weight: 400; }
.trend-up     { color: var(--green); }
.trend-warn   { color: var(--orange); }
.trend-danger { color: var(--red); }

.occ-grid {
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-bottom: 10px;
}
.occ-row {
  display: flex;
  gap: 4px;
  align-items: center;
}
.occ-dot {
  width: 11px;
  height: 11px;
  border-radius: 50%;
  background: #3b82f6;
  flex-shrink: 0;
}
.od-4 { opacity: 1.00; }
.od-3 { opacity: 0.55; }
.od-2 { opacity: 0.28; }
.od-1 { opacity: 0.12; }

.table-card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 12px;
  overflow: hidden;
  box-shadow: var(--shadow);
}

.table-toolbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 8px;
  padding: 11px 14px;
  border-bottom: 1px solid var(--border);
}
.toolbar-left  { display: flex; align-items: center; gap: 7px; flex-wrap: wrap; }
.toolbar-right { display: flex; align-items: center; gap: 7px; margin-left: auto; }

.filter-wrap {
  position: relative;
  display: flex;
  align-items: center;
}
.filter-ic {
  position: absolute;
  left: 8px;
  color: var(--dimmed);
  font-size: 14px;
}
.filter-wrap input {
  height: 30px;
  padding: 0 10px 0 27px;
  background: var(--surface-2);
  border: 1px solid var(--border);
  border-radius: 7px;
  font-family: 'Montserrat', sans-serif;
  font-size: 12.5px;
  color: var(--text);
  outline: none;
  width: 190px;
}
.filter-wrap input:focus { border-color: var(--accent); }
.filter-wrap input::placeholder { color: var(--dimmed); }

.sel-wrap {
  position: relative;
  display: flex;
  align-items: center;
}
.sel-arrow {
  position: absolute;
  right: 6px;
  pointer-events: none;
  color: var(--dimmed);
  font-size: 14px;
}
.sel-wrap select {
  height: 30px;
  padding: 0 22px 0 9px;
  background: var(--surface-2);
  border: 1px solid var(--border);
  border-radius: 7px;
  font-family: 'Montserrat', sans-serif;
  font-size: 12.5px;
  color: var(--text);
  appearance: none;
  outline: none;
  cursor: pointer;
}
.sel-wrap select:focus { border-color: var(--accent); }

.show-count { font-size: 12.5px; color: var(--muted); }
.show-count strong { color: var(--text); }

.toolbar-icon {
  font-size: 17px;
  color: var(--muted);
  cursor: pointer;
  transition: color 0.15s;
}
.toolbar-icon:hover { color: var(--text); }

.toolbar-divider {
  width: 1px;
  height: 20px;
  background: var(--border);
  margin: 0 4px;
}

/* Table */
.table-scroll { overflow-x: auto; }
.data-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}
.data-table thead { background: var(--surface-2); }
.data-table th {
  padding: 9px 13px;
  font-size: 10.5px;
  font-weight: 700;
  letter-spacing: 0.07em;
  color: var(--muted);
  text-align: left;
  border-bottom: 1px solid var(--border);
  white-space: nowrap;
}
.data-table td {
  padding: 11px 13px;
  border-bottom: 1px solid var(--border);
  color: var(--text);
  vertical-align: middle;
}
.data-table tbody tr:last-child td { border-bottom: none; }
.data-table tbody tr:hover td { background: var(--surface-2); }

.sess-link {
  color: var(--accent);
  text-decoration: none;
  font-weight: 600;
  font-size: 12px;
  font-family: 'Courier New', monospace;
}
.sess-link:hover { text-decoration: underline; }

.user-cell {
  display: flex;
  align-items: center;
  gap: 8px;
}
.av {
  width: 30px; height: 30px;
  border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: 11px;
  font-weight: 700;
  color: #fff;
  flex-shrink: 0;
}

.device-cell { display: flex; flex-direction: row; align-items: baseline; gap: 7px; white-space: nowrap; }
.device-ip   { font-family: 'Courier New', monospace; font-size: 13px; font-weight: 500; white-space: nowrap; }
.device-name { font-family: 'Courier New', monospace; font-size: 10.5px; color: var(--dimmed); white-space: nowrap; }

.mode-tag {
  font-size: 10.5px;
  font-weight: 700;
  letter-spacing: 0.05em;
  padding: 2px 8px;
  border-radius: 4px;
}
.mode-write { background: var(--write-bg); color: var(--write-text); }
.mode-view  { background: var(--view-bg);  color: var(--view-text); }

.status-tag {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  font-size: 11.5px;
  font-weight: 700;
  letter-spacing: 0.04em;
}
.st-active { color: var(--green); }
.st-queued { color: var(--orange); }
.st-dot {
  width: 6px; height: 6px;
  border-radius: 50%;
  background: currentColor;
}

.mono  { font-family: 'Courier New', monospace; font-size: 12.5px; }
.muted { color: var(--muted); }
.reason-col { max-width: 160px; }

.worker-plain {
  font-size: 12px;
  font-family: 'Courier New', monospace;
  color: var(--muted);
}

.row-actions { display: flex; gap: 8px; align-items: center; }
.act-icon {
  cursor: pointer;
  color: var(--muted);
  transition: color 0.15s;
}
.act-icon--open:hover  { color: var(--accent); }
.act-icon--close:hover { color: var(--red); }

.empty-td { text-align: center; color: var(--dimmed); padding: 32px !important; font-size: 13px; }

/* Table footer */
.table-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 14px;
  border-top: 1px solid var(--border);
}
.pager { display: flex; gap: 4px; }
.page-btn {
  width: 28px; height: 28px;
  border-radius: 6px;
  border: 1px solid var(--border);
  background: var(--surface-2);
  color: var(--muted);
  font-family: 'Montserrat', sans-serif;
  font-size: 12.5px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s;
}
.page-btn:hover { background: var(--surface); color: var(--text); }
.page-btn.active { background: var(--accent); color: #fff; border-color: var(--accent); }

.status-bar {
  display: flex;
  align-items: center;
  gap: 20px;
  font-size: 12px;
  color: var(--muted);
  flex-wrap: wrap;
  padding-bottom: 4px;
}
.status-item { display: flex; align-items: center; gap: 5px; }
.status-dot-green {
  width: 7px; height: 7px;
  background: var(--green);
  border-radius: 50%;
}
.status-right { margin-left: auto; color: var(--dimmed); }
</style>