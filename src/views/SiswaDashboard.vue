<template>
  <div class="dashboard-wrapper">
    <!-- Header Siswa -->
    <header class="user-header">
      <div class="user-info">
        <div class="avatar">👤</div>
        <div>
          <h3>{{ siswa.nama }}</h3>
          <span class="badge-kelas">{{ siswa.kelas }}</span>
        </div>
      </div>
      <button @click="$emit('logout')" class="btn-logout">
        Keluar 🚪
      </button>
    </header>

    <!-- Navigation Tabs -->
    <nav class="nav-tabs">
      <button 
        @click="menuAktif = 'home'" 
        :class="{ active: menuAktif === 'home' }"
      >
        🗳️ 1. Bilik Suara
      </button>
      <button 
        @click="menuAktif = 'dokumentasi'" 
        :class="{ active: menuAktif === 'dokumentasi' }"
      >
        📁 2. Dokumentasi Paslon
      </button>
      <button 
        @click="menuAktif = 'hasil'" 
        :class="{ active: menuAktif === 'hasil' }"
      >
        📊 3. Hasil Real-Time
      </button>
    </nav>

    <!-- TAB 1: BILIK SUARA -->
    <main v-if="menuAktif === 'home'" class="tab-content">
      <div v-if="!sudahMemilih">
        <div class="section-title">
          <h2>Surat Suara Digital</h2>
          <p>Tentukan pilihanmu dengan menekan tombol coblos pada kandidat pilihan.</p>
        </div>
        
        <div class="kandidat-grid">
          <div v-for="k in kandidat" :key="k.id" class="kandidat-card">
            <div class="paslon-number">0{{ k.id }}</div>
            <img :src="k.foto" alt="Foto Paslon" class="foto-paslon" />
            <div class="kandidat-details">
              <h4>{{ k.nama }}</h4>
              <p class="visi-text"><strong>Visi & Misi:</strong> {{ k.visi }}</p>
            </div>
            <button @click="pilihKandidat(k.id)" class="btn-coblos">
              Coblos Paslon 0{{ k.id }}
            </button>
          </div>
        </div>
      </div>
      
      <div v-else class="success-box">
        <div class="success-icon">🎉</div>
        <h2>Suara Anda Berhasil Direkam!</h2>
        <p>Terima kasih telah berpartisipasi aktif dalam demokrasi sekolah.</p>
        <button @click="menuAktif = 'hasil'" class="btn-action-view">
          Lihat Perolehan Suara Real-Time ➔
        </button>
      </div>
    </main>

    <!-- TAB 2: DOKUMENTASI -->
    <main v-else-if="menuAktif === 'dokumentasi'" class="tab-content">
      <div class="section-title">
        <h2>Dokumentasi & Program Kerja</h2>
        <p>Rekam jejak dan portofolio kegiatan dari setiap pasangan kandidat.</p>
      </div>
      
      <div class="dok-grid">
        <div v-for="k in kandidat" :key="k.id" class="dok-card">
          <div class="dok-header">
            <span class="tag-paslon">Paslon 0{{ k.id }}</span>
            <h4>{{ k.nama }}</h4>
          </div>
          <div class="dok-body">
            <img :src="k.fotoDokumentasi" alt="Kegiatan Dok" class="foto-dok" />
            <div class="dok-info">
              <p><strong>Program Unggulan:</strong> {{ k.visi }}</p>
              <span class="sub-text">📷 Sesi orasi & sosialisasi program kerja OSIS digital.</span>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- TAB 3: HASIL REAL-TIME -->
    <main v-else-if="menuAktif === 'hasil'" class="tab-content">
      <div class="section-title">
        <h2>Hasil Perolehan Suara Real-Time</h2>
        <p>Persentase suara diperbarui secara otomatis setiap kali ada pemilih baru.</p>
      </div>

      <div class="chart-container">
        <div v-for="k in kandidat" :key="k.id" class="progress-item">
          <div class="progress-info">
            <span class="paslon-label"><strong>Paslon 0{{ k.id }}</strong> - {{ k.nama }}</span>
            <span class="vote-count"><strong>{{ k.suara }} suara</strong> ({{ hitungPersen(k.suara) }}%)</span>
          </div>
          <div class="progress-bar-bg">
            <div class="progress-bar-fill" :style="{ width: hitungPersen(k.suara) + '%' }"></div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'siswadashboard',
  props: ['siswa', 'kandidat', 'sudahMemilih'],
  data() {
    return {
      menuAktif: 'home'
    }
  },
  computed: {
    totalSuara() {
      return this.kandidat.reduce((sum, k) => sum + k.suara, 0);
    }
  },
  methods: {
    pilihKandidat(id) {
      this.$emit('kirim-suara', id);
      this.menuAktif = 'hasil'; 
    },
    hitungPersen(suara) {
      if (this.totalSuara === 0) return 0;
      return ((suara / this.totalSuara) * 100).toFixed(1);
    }
  }
}
</script>

<style scoped>
.dashboard-wrapper {
  background: #ffffff;
  border-radius: 20px;
  padding: 30px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.05);
  border: 1px solid #f1f5f9;
  animation: fadeIn 0.4s ease-out;
}

.user-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #f8fafc;
  padding: 16px 20px;
  border-radius: 14px;
  border: 1px solid #e2e8f0;
  margin-bottom: 25px;
}
.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
}
.avatar {
  font-size: 24px;
  background: #e2e8f0;
  width: 42px;
  height: 42px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
}
.user-info h3 {
  margin: 0;
  font-size: 16px;
  color: #0f172a;
}
.badge-kelas {
  background: #dbeafe;
  color: #1e40af;
  font-size: 12px;
  font-weight: 700;
  padding: 3px 8px;
  border-radius: 6px;
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
  transition: background 0.2s;
}
.btn-logout:hover {
  background: #fca5a5;
}

.nav-tabs {
  display: flex;
  gap: 10px;
  border-bottom: 2px solid #f1f5f9;
  padding-bottom: 12px;
  margin-bottom: 25px;
  overflow-x: auto;
}
.nav-tabs button {
  background: #f8fafc;
  color: #64748b;
  border: 1px solid #e2e8f0;
  padding: 10px 18px;
  border-radius: 10px;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  white-space: nowrap;
  transition: all 0.2s;
}
.nav-tabs button.active {
  background: #2563eb;
  color: white;
  border-color: #2563eb;
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.25);
}

.section-title h2 {
  margin: 0;
  font-size: 20px;
  color: #0f172a;
}
.section-title p {
  margin: 5px 0 20px;
  color: #64748b;
  font-size: 13px;
}

/* Voting Grid Cards */
.kandidat-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 20px;
}
.kandidat-card {
  position: relative;
  background: #ffffff;
  border: 1px solid #e2e8f0;
  border-radius: 16px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  transition: transform 0.2s, box-shadow 0.2s;
}
.kandidat-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.06);
}
.paslon-number {
  position: absolute;
  top: 12px;
  left: 12px;
  background: #0f172a;
  color: white;
  font-size: 12px;
  font-weight: 700;
  padding: 4px 8px;
  border-radius: 6px;
}
.foto-paslon {
  width: 90px;
  height: 90px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid #2563eb;
  margin-top: 10px;
  margin-bottom: 15px;
}
.kandidat-details h4 {
  margin: 0 0 8px;
  font-size: 16px;
  color: #0f172a;
}
.visi-text {
  font-size: 12px;
  color: #64748b;
  margin-bottom: 20px;
  line-height: 1.5;
}
.btn-coblos {
  width: 100%;
  margin-top: auto;
  background: #16a34a;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 8px;
  font-weight: 700;
  cursor: pointer;
  transition: background 0.2s;
}
.btn-coblos:hover {
  background: #15803d;
}

/* Success State */
.success-box {
  text-align: center;
  padding: 40px 20px;
  background: #f0fdf4;
  border-radius: 16px;
  border: 1px solid #bbf7d0;
}
.success-icon {
  font-size: 50px;
  margin-bottom: 10px;
}
.success-box h2 {
  color: #166534;
  margin: 0 0 8px;
}
.success-box p {
  color: #15803d;
  font-size: 14px;
  margin-bottom: 20px;
}
.btn-action-view {
  background: #16a34a;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
}

/* Dokumentasi Grid */
.dok-grid {
  display: flex;
  flex-direction: column;
  gap: 15px;
}
.dok-card {
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  padding: 18px;
  background: #fafafa;
}
.dok-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}
.tag-paslon {
  background: #0f172a;
  color: white;
  font-size: 11px;
  font-weight: 700;
  padding: 3px 8px;
  border-radius: 4px;
}
.dok-header h4 {
  margin: 0;
  color: #0f172a;
}
.dok-body {
  display: flex;
  gap: 15px;
  align-items: center;
}
.foto-dok {
  width: 120px;
  height: 80px;
  object-fit: cover;
  border-radius: 8px;
  border: 1px solid #cbd5e1;
  flex-shrink: 0;
}
.dok-info p {
  margin: 0 0 6px;
  font-size: 13px;
  color: #334155;
}
.sub-text {
  font-size: 12px;
  color: #94a3b8;
}

/* Progress Bars */
.chart-container {
  display: flex;
  flex-direction: column;
  gap: 18px;
}
.progress-item {
  display: flex;
  flex-direction: column;
  gap: 6px;
}
.progress-info {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
}
.paslon-label {
  color: #334155;
}
.vote-count {
  color: #2563eb;
}
.progress-bar-bg {
  background: #e2e8f0;
  height: 14px;
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