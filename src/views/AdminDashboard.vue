<template>
  <div class="admin-wrapper">
    <!-- Header Admin -->
    <header class="admin-header">
      <div>
        <h2>🛠️ Control Panel Administrator</h2>
        <p>Manajemen kandidat, log riwayat, dan ekspor data pemilu.</p>
      </div>
      <button @click="$emit('logout')" class="btn-logout">
        Keluar Admin 🚪
      </button>
    </header>

    <!-- Statistik Ringkas -->
    <div class="stats-grid">
      <div class="stat-card">
        <span class="stat-label">Total Suara Masuk</span>
        <div class="stat-value">{{ totalSuara }}</div>
      </div>
      <div class="stat-card">
        <span class="stat-label">Total Paslon</span>
        <div class="stat-value">{{ kandidat.length }}</div>
      </div>
    </div>

    <!-- Live Chart Summary -->
    <section class="admin-section">
      <h3>📊 Perolehan Suara Real-Time</h3>
      <div class="chart-list">
        <div v-for="k in kandidat" :key="k.id" class="progress-item">
          <div class="progress-info">
            <span><strong>Paslon 0{{ k.id }}</strong> - {{ k.nama }}</span>
            <span><strong>{{ k.suara }} Suara</strong> ({{ hitungPersen(k.suara) }}%)</span>
          </div>
          <div class="progress-bar-bg">
            <div class="progress-bar-fill" :style="{ width: hitungPersen(k.suara) + '%' }"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- Form Edit Kandidat -->
    <section class="admin-section">
      <h3>✏️ Edit Informasi & Dokumentasi Paslon</h3>
      <div class="edit-grid">
        <div v-for="k in kandidat" :key="k.id" class="edit-card">
          <div class="edit-card-title">Paslon No. 0{{ k.id }}</div>
          <div class="form-group">
            <label>Nama Pasangan</label>
            <input type="text" v-model="k.nama" />
          </div>
          <div class="form-group">
            <label>URL Foto Profil</label>
            <input type="text" v-model="k.foto" />
          </div>
          <div class="form-group">
            <label>URL Foto Dokumentasi</label>
            <input type="text" v-model="k.fotoDokumentasi" />
          </div>
          <div class="form-group">
            <label>Visi & Misi</label>
            <textarea v-model="k.visi" rows="2"></textarea>
          </div>
        </div>
      </div>
    </section>

    <!-- Log Pemilih & Spreadsheet -->
    <section class="admin-section">
      <div class="table-header-row">
        <div>
          <h3>📜 Log Riwayat Pemilih</h3>
          <p>Daftar riwayat aktivitas siswa yang telah menggunakan hak pilih.</p>
        </div>
        <button @click="eksporKeSpreadsheet" class="btn-excel">
          📥 Ekspor ke Spreadsheet (.CSV)
        </button>
      </div>

      <div class="filter-box">
        <label>Filter Berdasarkan Kelas:</label>
        <select v-model="filterKelas">
          <option value="">Semua Kelas</option>
          <option value="XI RPL">XI RPL</option>
          <option value="XI TJKT">XI TJKT</option>
          <option value="XI DKV">XI DKV</option>
        </select>
      </div>

      <div class="table-responsive">
        <table class="table-custom">
          <thead>
            <tr>
              <th>Waktu</th>
              <th>Nama Siswa</th>
              <th>Kelas</th>
              <th>Pilihan</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="filteredLog.length === 0">
              <td colspan="4" class="empty-cell">Belum ada data log pemilihan.</td>
            </tr>
            <tr v-for="(log, index) in filteredLog" :key="index">
              <td>{{ log.waktu }}</td>
              <td><strong>{{ log.nama }}</strong></td>
              <td><span class="badge-kelas">{{ log.kelas }}</span></td>
              <td><span class="badge-paslon">Paslon 0{{ log.pilihanId }}</span></td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>

    <div class="danger-zone">
      <button @click="$emit('reset-data')" class="btn-danger">
        ⚠️ Reset Seluruh Data Pemilu
      </button>
    </div>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'admindashboard',
  props: ['kandidat', 'logRiwayat'],
  data() {
    return {
      filterKelas: ''
    }
  },
  computed: {
    totalSuara() {
      return this.kandidat.reduce((sum, k) => sum + k.suara, 0);
    },
    filteredLog() {
      if (!this.filterKelas) return this.logRiwayat;
      return this.logRiwayat.filter(l => l.kelas === this.filterKelas);
    }
  },
  methods: {
    hitungPersen(suara) {
      if (this.totalSuara === 0) return 0;
      return ((suara / this.totalSuara) * 100).toFixed(1);
    },
    eksporKeSpreadsheet() {
      let csv = "data:text/csv;charset=utf-8,Waktu,Nama Siswa,Kelas,Pilihan Paslon\n";
      this.logRiwayat.forEach(row => {
        csv += `${row.waktu},${row.nama},${row.kelas},Paslon 0${row.pilihanId}\n`;
      });
      const encodedUri = encodeURI(csv);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "laporan_pemilu_osis.csv");
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  }
}
</script>

<style scoped>
.admin-wrapper {
  background: #ffffff;
  border-radius: 20px;
  padding: 30px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
  border: 1px solid #f1f5f9;
  animation: fadeIn 0.4s ease-out;
}

.admin-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}
.admin-header h2 {
  margin: 0;
  font-size: 22px;
  color: #0f172a;
}
.admin-header p {
  margin: 4px 0 0;
  color: #64748b;
  font-size: 13px;
}
.btn-logout {
  background: #fee2e2;
  color: #dc2626;
  border: none;
  padding: 8px 14px;
  border-radius: 8px;
  font-weight: 600;
  font-size: 13px;
  cursor: pointer;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 15px;
  margin-bottom: 30px;
}
.stat-card {
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  padding: 18px;
  border-radius: 14px;
}
.stat-label {
  font-size: 12px;
  font-weight: 600;
  color: #64748b;
}
.stat-value {
  font-size: 28px;
  font-weight: 800;
  color: #2563eb;
  margin-top: 5px;
}

.admin-section {
  margin-bottom: 35px;
  border-top: 1px solid #f1f5f9;
  padding-top: 20px;
}
.admin-section h3 {
  margin: 0 0 15px;
  font-size: 17px;
  color: #0f172a;
}

.edit-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 15px;
}
.edit-card {
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  padding: 16px;
}
.edit-card-title {
  font-weight: 700;
  font-size: 13px;
  color: #0f172a;
  margin-bottom: 10px;
}
.form-group {
  margin-bottom: 10px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.form-group label {
  font-size: 11px;
  font-weight: 600;
  color: #64748b;
}
.form-group input, .form-group textarea {
  padding: 8px 10px;
  border: 1px solid #cbd5e1;
  border-radius: 6px;
  font-size: 13px;
  outline: none;
}

.table-header-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 10px;
  margin-bottom: 15px;
}
.btn-excel {
  background: #16a34a;
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}

.filter-box {
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 13px;
}
.filter-box select {
  padding: 6px 12px;
  border-radius: 6px;
  border: 1px solid #cbd5e1;
}

.table-responsive {
  overflow-x: auto;
}
.table-custom {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
}
.table-custom th, .table-custom td {
  padding: 12px;
  border-bottom: 1px solid #e2e8f0;
  text-align: left;
}
.table-custom th {
  background: #f8fafc;
  color: #475569;
  font-weight: 600;
}
.badge-kelas {
  background: #e2e8f0;
  color: #334155;
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 700;
}
.badge-paslon {
  background: #dbeafe;
  color: #1e40af;
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 700;
}
.empty-cell {
  text-align: center;
  color: #94a3b8;
}

.danger-zone {
  margin-top: 20px;
  text-align: right;
}
.btn-danger {
  background: #dc2626;
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
}

/* Progress Bars */
.chart-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.progress-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.progress-info {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
}
.progress-bar-bg {
  background: #e2e8f0;
  height: 12px;
  border-radius: 10px;
  overflow: hidden;
}
.progress-bar-fill {
  background: linear-gradient(90deg, #2563eb, #3b82f6);
  height: 100%;
  border-radius: 10px;
  transition: width 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>