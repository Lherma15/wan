absensi-siswa/
├── server.js
const express = require('express');
const fs = require('fs');
const path = require('path');

const app = express();
const PORT = 3000;
const DATA_FILE = path.join(__dirname, 'data', 'absensi.json');

app.use(express.json());
app.use(express.static('public'));

// Endpoint untuk mendapatkan data absensi
app.get('/api/absensi', (req, res) => {
  const data = fs.readFileSync(DATA_FILE, 'utf-8');
  res.json(JSON.parse(data));
});

// Endpoint untuk menambah absensi
app.post('/api/absensi', (req, res) => {
  const { nama, tanggal, status } = req.body;
  if (!nama || !tanggal || !status) {
    return res.status(400).json({ message: 'Data tidak lengkap' });
  }
  const data = JSON.parse(fs.readFileSync(DATA_FILE, 'utf-8'));
  data.push({ nama, tanggal, status });
  fs.writeFileSync(DATA_FILE, JSON.stringify(data, null, 2));
  res.json({ message: 'Absensi berhasil dicatat' });
});

app.listen(PORT, () => {
  console.log(`Server berjalan di http://localhost:${PORT}`);
});
    
├── data/
│   └── absensi.json]
[]
├── public/
│   ├── index.html
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Absensi Siswa</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Aplikasi Absensi Siswa</h1>
  <form id="absensi-form">
    <label>Nama:
      <input type="text" id="nama" required>
    </label>
    <label>Tanggal:
      <input type="date" id="tanggal" required>
    </label>
    <label>Status:
      <select id="status" required>
        <option value="">-- Pilih --</option>
        <option value="Hadir">Hadir</option>
        <option value="Izin">Izin</option>
        <option value="Sakit">Sakit</option>
        <option value="Alpa">Alpa</option>
      </select>
    </label>
    <button type="submit">Simpan</button>
  </form>

  <h2>Rekap Absensi</h2>
  <table>
    <thead>
      <tr>
        <th>Nama</th>
        <th>Tanggal</th>
        <th>Status</th>
      </tr>
    </thead>
    <tbody id="rekap-absensi"></tbody>
  </table>
  <script src="script.js"></script>
</body>
</html>
│   ├── style.css]
body {
  font-family: Arial, sans-serif;
  background: #f6f7fb;
  padding: 40px;
}
h1, h2 {
  color: #1e6091;
}
form {
  margin-bottom: 30px;
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 5px #e0e0e0;
}
label {
  display: block;
  margin-bottom: 10px;
}
input, select, button {
  margin-top: 5px;
  margin-bottom: 10px;
}
table {
  width: 100%;
  border-collapse: collapse;
  background: #fff;
}
th, td {
  border: 1px solid #d8eefe;
  padding: 8px 12px;
}
th {
  background: #1e6091;
  color: #fff;
}
│   └── script.js
async function loadAbsensi() {
  const response = await fetch('/api/absensi');
  const data = await response.json();
  const tbody = document.getElementById('rekap-absensi');
  tbody.innerHTML = "";
  data.forEach(a => {
    const row = document.createElement("tr");
    row.innerHTML = `<td>${a.nama}</td><td>${a.tanggal}</td><td>${a.status}</td>`;
    tbody.appendChild(row);
  });
}

document.getElementById('absensi-form').addEventListener('submit', async function(e) {
  e.preventDefault();
  const nama = document.getElementById('nama').value;
  const tanggal = document.getElementById('tanggal').value;
  const status = document.getElementById('status').value;
  const res = await fetch('/api/absensi', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({ nama, tanggal, status })
  });
  if (res.ok) {
    loadAbsensi();
    this.reset();
    alert("Absensi berhasil disimpan!");
  } else {
    alert("Gagal menyimpan absensi.");
  }
});

window.onload = loadAbsensi;
