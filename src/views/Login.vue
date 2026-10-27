<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="login-wrapper">
    <div class="login-box">
      <div class="brand-header">
        <div class="logo-badge">🗳️</div>
        <h2>Portal E-Voting OSIS</h2>
        <p>Sistem Pemilihan Ketua & Wakil Ketua OSIS</p>
      </div>

      <!-- Tab Switcher -->
      <div class="tab-container">
        <button 
          @click="mode = 'siswa'" 
          :class="{ active: mode === 'siswa' }"
          type="button"
        >
          👤 Pemilih (Siswa)
        </button>
        <button 
          @click="mode = 'admin'" 
          :class="{ active: mode === 'admin' }"
          type="button"
        >
          🔐 Admin Panitia
        </button>
      </div>

      <!-- Form Siswa -->
      <form v-if="mode === 'siswa'" @submit.prevent="submitSiswa" class="form-content">
        <div class="form-group">
          <label>Nama Lengkap</label>
          <input type="text" v-model="nama" placeholder="Contoh: Budi Santoso" required />
        </div>
        <div class="form-group">
          <label>Kelas & Jurusan</label>
          <select v-model="kelas" required>
            <option disabled value="">-- Pilih Kelas --</option>
            <option value="X">X</option>
            <option value="XI">XI</option>
            <option value="XII">XII</option>
          </select>
        </div>
        <button type="submit" class="btn-action btn-primary">
          Masuk ke Bilik Suara ➔
        </button>
      </form>

      <!-- Form Admin -->
      <form v-else @submit.prevent="submitAdmin" class="form-content">
        <div class="form-group">
          <label>Username Admin</label>
          <input type="text" v-model="username" placeholder="Masukkan username" required />
        </div>
        <div class="form-group">
          <label>Password</label>
          <input type="password" v-model="password" placeholder="Masukkan password" required />
        </div>
        <button type="submit" class="btn-action btn-dark">
          Masuk Panel Admin 🔓
        </button>
        <span class="hint-text">
          Default Akun: <code>admin</code> / <code>smk123</code>
        </span>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Login',
  data() {
    return {
      mode: 'siswa',
      nama: '',
      kelas: '',
      username: '',
      password: ''
    }
  },
  methods: {
    submitSiswa() {
      if (!this.nama || !this.kelas) return;
      this.$emit('login-siswa', { nama: this.nama, kelas: this.kelas });
    },
    submitAdmin() {
      if (this.username === 'admin' && this.password === 'smk123') {
        this.$emit('login-admin');
      } else {
        alert('Username atau Password Admin salah!');
      }
    }
  }
}
</script>

<style scoped>
.login-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
  padding: 20px;
}
.login-box {
  width: 100%;
  max-width: 440px;
  background: #ffffff;
  padding: 35px 30px;
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
  border: 1px solid rgba(226, 232, 240, 0.8);
  animation: fadeIn 0.4s ease-out;
}
.brand-header {
  text-align: center;
  margin-bottom: 25px;
}
.logo-badge {
  font-size: 40px;
  margin-bottom: 8px;
}
.brand-header h2 {
  margin: 0;
  color: #0f172a;
  font-size: 22px;
  font-weight: 700;
}
.brand-header p {
  margin: 5px 0 0;
  color: #64748b;
  font-size: 13px;
}

.tab-container {
  display: flex;
  background: #f1f5f9;
  padding: 4px;
  border-radius: 12px;
  margin-bottom: 25px;
}
.tab-container button {
  flex: 1;
  padding: 10px;
  background: transparent;
  border: none;
  font-size: 13px;
  font-weight: 600;
  color: #64748b;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
}
.tab-container button.active {
  background: #ffffff;
  color: #2563eb;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
}

.form-content {
  display: flex;
  flex-direction: column;
  gap: 18px;
}
.form-group {
  display: flex;
  flex-direction: column;
  gap: 6px;
}
.form-group label {
  font-size: 13px;
  font-weight: 600;
  color: #334155;
}
.form-group input, .form-group select {
  padding: 12px 14px;
  border: 1px solid #cbd5e1;
  border-radius: 10px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
}
.form-group input:focus, .form-group select:focus {
  border-color: #2563eb;
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.15);
}

.btn-action {
  width: 100%;
  padding: 12px;
  border: none;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.1s ease, background-color 0.2s;
  margin-top: 5px;
}
.btn-action:active {
  transform: scale(0.98);
}
.btn-primary {
  background: #2563eb;
  color: white;
}
.btn-primary:hover {
  background: #1d4ed8;
}
.btn-dark {
  background: #0f172a;
  color: white;
}
.btn-dark:hover {
  background: #1e293b;
}

.hint-text {
  font-size: 11px;
  color: #94a3b8;
  text-align: center;
  display: block;
  margin-top: 5px;
}
code {
  background: #f1f5f9;
  padding: 2px 6px;
  border-radius: 4px;
  color: #0f172a;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>