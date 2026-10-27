<template>
  <div class="main-wrapper">
    <!-- Komponen 1: Halaman Login -->
    <Login 
      v-if="currentRole === 'login'" 
      @login-siswa="handleLoginSiswa" 
      @login-admin="currentRole = 'admin'" 
    />

    <!-- Komponen 2: Dashboard Siswa -->
    <SiswaDashboard 
      v-else-if="currentRole === 'siswa'" 
      :siswa="dataSiswa" 
      :kandidat="kandidat" 
      :sudahMemilih="sudahMemilih"
      @kirim-suara="simpanSuara"
      @logout="currentRole = 'login'" 
    />

    <!-- Komponen 3: Dashboard Admin -->
    <AdminDashboard 
      v-else-if="currentRole === 'admin'" 
      :kandidat="kandidat" 
      :logRiwayat="logRiwayat"
      @reset-data="resetSistem"
      @logout="currentRole = 'login'" 
    />
  </div>
</template>

<script>
import Login from './views/Login.vue'
import SiswaDashboard from './views/SiswaDashboard.vue'
import AdminDashboard from './views/AdminDashboard.vue'

export default {
  name: 'App',
  components: { Login, SiswaDashboard, AdminDashboard },
  data() {
    return {
      currentRole: 'login', 
      sudahMemilih: false,
      dataSiswa: { nama: '', kelas: '' },

      kandidat: [
        { 
          id: 1, 
          nama: "Ahmad & Siti", 
          visi: "Mewujudkan siswa yang kreatif, aktif, dan disiplin.", 
          foto: "https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=150&auto=format&fit=crop&q=80", 
          fotoDokumentasi: "https://images.unsplash.com/photo-1517245386807-bb43f82c33c4?w=300&auto=format&fit=crop&q=80",
          suara: 0 
        },
        { 
          id: 2, 
          nama: "Budi & Rina", 
          visi: "Meningkatkan prestasi akademik dan fasilitas ekstrakurikuler.", 
          foto: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=150&auto=format&fit=crop&q=80", 
          fotoDokumentasi: "https://images.unsplash.com/photo-1531482615713-2afd69097998?w=300&auto=format&fit=crop&q=80",
          suara: 0 
        },
        { 
          id: 3, 
          nama: "Candra & Dewi", 
          visi: "Optimalisasi transparansi program kerja OSIS berbasis digital.", 
          foto: "https://images.unsplash.com/photo-1500648767791-00dcc994a43e?w=150&auto=format&fit=crop&q=80", 
          fotoDokumentasi: "https://images.unsplash.com/photo-1522071820081-009f0129c71c?w=300&auto=format&fit=crop&q=80",
          suara: 0 
        }
      ],

      logRiwayat: []
    }
  },
  methods: {
    handleLoginSiswa(info) {
      this.dataSiswa = info;
      this.sudahMemilih = false;
      this.currentRole = 'siswa';
    },
    simpanSuara(idKandidat) {
      const target = this.kandidat.find(k => k.id === idKandidat);
      if (target) {
        target.suara += 1;
        this.sudahMemilih = true;

        const waktuSekarang = new Date().toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
        this.logRiwayat.unshift({
          waktu: waktuSekarang,
          nama: this.dataSiswa.nama,
          kelas: this.dataSiswa.kelas,
          pilihanId: idKandidat
        });
      }
    },
    resetSistem() {
      if (confirm("Yakin ingin mereset seluruh data suara dan log?")) {
        this.kandidat.forEach(k => k.suara = 0);
        this.logRiwayat = [];
        this.currentRole = 'login';
      }
    }
  }
}
</script>

<style>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f0f2f5;
  margin: 0;
  padding: 20px;
}
.main-wrapper {
  max-width: 800px;
  margin: 0 auto;
}
</style>