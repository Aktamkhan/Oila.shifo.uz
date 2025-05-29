# Oila.shifo.uz
Oilaviy poliklinika uchun platforma 
<!DOCTYPE html>
<html lang="uz">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>https://Oila.shifo.uz</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f9f9f9;
      margin: 0; padding: 20px;
    }
    h1 {
      text-align: center;
      color: #333;
    }
    form, .search-box, table {
      max-width: 1000px;
      margin: 20px auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 15px rgba(0,0,0,0.1);
    }
    label {
      display: block;
      margin-top: 10px;
      font-weight: bold;
    }
    input, select {
      width: 100%;
      padding: 10px;
      margin-top: 5px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      background: #27ae60;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover { background: #219150; }
    table {
      width: 100%;
      border-collapse: collapse;
    }
    th, td {
      padding: 10px;
      border-bottom: 1px solid #ccc;
      text-align: left;
    }
    th { background: #2980b9; color: white; }
    .actions button {
      margin-right: 5px;
      padding: 5px 10px;
      border: none;
      border-radius: 3px;
      cursor: pointer;
    }
    .edit-btn { background: #f39c12; color: white; }
    .delete-btn { background: #e74c3c; color: white; }
    .export-box {
      max-width: 1000px;
      margin: 0 auto;
      text-align: right;
    }
  </style>
</head>
<body>
<h1>OShP Platforma</h1>

<div class="search-box">
  <input type="text" id="searchInput" placeholder="Qidiruv..." oninput="renderTable()" />
</div>

<form id="oshpForm">
  <input type="hidden" id="editIndex" value="-1">
  <label>Familya, Ism, Sharif</label><input type="text" id="fullName" required>
  <label>Tug‘ilgan sana</label><input type="date" id="birthDate" required>
  <label>Passport/Guvohnoma</label><input type="text" id="passport" required>
  <label>PINFL</label><input type="text" id="pinfl" pattern="\d{14}" required>
  <label>Telefon raqami</label><input type="tel" id="phone" pattern="^\+998\d{9}$" required>
  <label>Tibbiy brigada raqami</label><input type="text" id="brigadaNum" required>
  <label>Ko‘chani tanlang</label>
  <select id="kocha" required>
    <option value="">Tanlang</option>
    <option value="Ko‘cha 1">Ko‘cha 1</option>
    <option value="Ko‘cha 2">Ko‘cha 2</option>
    <option value="Ko‘cha 3">Ko‘cha 3</option>
    <option value="Ko‘cha 4">Ko‘cha 4</option>
    <option value="Ko‘cha 5">Ko‘cha 5</option>
    <option value="Ko‘cha 6">Ko‘cha 6</option>
    <option value="Ko‘cha 7">Ko‘cha 7</option>
    <option value="Ko‘cha 8">Ko‘cha 8</option>
    <option value="Ko‘cha 9">Ko‘cha 9</option>
    <option value="Ko‘cha 10">Ko‘cha 10</option>
  </select>
  <label>Ovqatlanish turi</label>
  <select id="foodType" required>
    <option value="">Tanlang</option>
    <option value="to‘g‘ri">To‘g‘ri</option>
    <option value="noto‘g‘ri">Noto‘g‘ri</option>
  </select>
  <label>Jismoniy faollik</label>
  <select id="physicalActivity" required>
    <option value="">Tanlang</option>
    <option value="faol">Faol</option>
    <option value="no faol">No faol</option>
  </select>
  <label>Zararli odatlari</label>
  <select id="badHabits" required>
    <option value="">Tanlang</option>
    <option value="mavjud">Mavjud</option>
    <option value="mavjud emas">Mavjud emas</option>
  </select>
  <label>Vazn (kg)</label><input type="number" id="weight" required>
  <label>Bo‘yi (sm)</label><input type="number" id="height" required>
  <label>TVI</label><input type="number" id="tvi" required step="0.1" min="0">
  <label>Tashxisi</label><input type="text" id="diagnosis" required>
  <button type="submit">Saqlash</button>
</form>

<div class="export-box">
  <button onclick="downloadCSV()">CSV yuklab olish</button>
</div>

<table>
  <thead>
    <tr>
      <th>T/R</th>
      <th>F.I.Sh.</th>
      <th>PINFL</th>
      <th>Tel</th>
      <th>Ko‘cha</th>
      <th>Amallar</th>
    </tr>
  </thead>
  <tbody id="recordsBody"></tbody>
</table>

<script>
  const STORAGE_KEY = 'oshp_data';
  const TEMP_KEY = 'oshp_temp';

  function getRecords() {
    return JSON.parse(localStorage.getItem(STORAGE_KEY)) || [];
  }

  function saveRecords(data) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(data));
  }

  function renderTable() {
    const records = getRecords();
    const tbody = document.getElementById('recordsBody');
    const search = document.getElementById('searchInput').value.toLowerCase();
    tbody.innerHTML = '';
    records.forEach((rec, i) => {
      const fulltext = `${rec.fullName} ${rec.pinfl} ${rec.phone} ${rec.kocha} ${rec.diagnosis}`.toLowerCase();
      if (fulltext.includes(search)) {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${i + 1}</td>
          <td>${rec.fullName}</td>
          <td>${rec.pinfl}</td>
          <td>${rec.phone}</td>
          <td>${rec.kocha}</td>
          <td class="actions">
            <button class="edit-btn" onclick="editRecord(${i})">Tahrirlash</button>
            <button class="delete-btn" onclick="deleteRecord(${i})">O‘chirish</button>
          </td>
        `;
        tbody.appendChild(tr);
      }
    });
  }

  function clearForm() {
    document.getElementById('oshpForm').reset();
    document.getElementById('editIndex').value = -1;
    localStorage.removeItem(TEMP_KEY);
  }

  function editRecord(index) {
    const record = getRecords()[index];
    for (let key in record) {
      const el = document.getElementById(key);
      if (el) el.value = record[key];
    }
    document.getElementById('editIndex').value = index;
    window.scrollTo({ top: 0, behavior: 'smooth' });
  }

  function deleteRecord(index) {
    if (confirm("Ma'lumotni o‘chirmoqchimisiz?")) {
      const records = getRecords();
      records.splice(index, 1);
      saveRecords(records);
      renderTable();
    }
  }

  document.getElementById('oshpForm').addEventListener('submit', function (e) {
    e.preventDefault();
    const formData = {};
    this.querySelectorAll('input, select').forEach(input => formData[input.id] = input.value);
    const index = parseInt(document.getElementById('editIndex').value);
    const records = getRecords();
    if (index >= 0) {
      records[index] = formData;
    } else {
      records.push(formData);
    }
    saveRecords(records);
    renderTable();
    clearForm();
  });

  document.querySelectorAll('#oshpForm input, #oshpForm select').forEach(input => {
    input.addEventListener('input', () => {
      const tempData = {};
      document.querySelectorAll('#oshpForm input, #oshpForm select').forEach(i => tempData[i.id] = i.value);
      localStorage.setItem(TEMP_KEY, JSON.stringify(tempData));
    });
  });

  window.addEventListener('DOMContentLoaded', () => {
    renderTable();
    const saved = JSON.parse(localStorage.getItem(TEMP_KEY));
    if (saved) {
      Object.keys(saved).forEach(key => {
        const el = document.getElementById(key);
        if (el) el.value = saved[key];
      });
    }
  });

  function downloadCSV() {
    const records = getRecords();
    if (!records.length) return alert("Hech qanday ma'lumot yo'q");
    const headers = Object.keys(records[0]);
    const csvRows = [
      headers.join(','),
      ...records.map(rec => headers.map(h => `"${rec[h]}"`).join(','))
    ];
    const blob = new Blob([csvRows.join('\n')], { type: 'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'oshp_ma\'lumotlar.csv';
    a.click();
    URL.revokeObjectURL(url);
  }
</script>
</body>
</html>