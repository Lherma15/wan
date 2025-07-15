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
│   └── absensi.json
├── public/
│   ├── index.html
│   ├── style.css
│   └── script.js
