<template>
  <div class="container my-5">
    <div class="row align-items-center mb-4">
      <div class="col text-center">
        <h2 class="mb-0">DATA PEMBAYARAN PRODUK</h2>
      </div>
    </div>
    <div class="row justify-content-between align-items-center">
  <!-- Download Data Button -->
  <div class="col-auto">
    <input
      type="date"
      v-model="selectedDate"
      @change="getTransactions"
      style="height: 3rem; background-color: #D9D9D9;"
      class="form-control rounded-3 my-3"
    />
  </div>

  <!-- Date Input -->
  <div class="col-auto">
    <button @click="downloadExcel" class="btn d-flex align-items-center" type="button" style="height: 3rem; background-color: #3dff13;">
      <!-- SVG for Download Icon -->
      <svg xmlns="http://www.w3.org/2000/svg" width="10" height="10" fill="currentColor" class="bi bi-file-earmark-arrow-down" viewBox="0 0 16 16" style="margin-right: 5px;">
        <path d="M8.5 6.5a.5.5 0 0 0-1 0v3.793L6.354 9.146a.5.5 0 1 0-.708.708l2 2a.5.5 0 0 0 .708 0l2-2a.5.5 0 0 0-.708-.708L8.5 10.293z"/>
        <path d="M14 14V4.5L9.5 0H4a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2M9.5 3A1.5 1.5 0 0 0 11 4.5h2V14a1 1 0 0 1-1 1H4a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1h5.5z"/>
      </svg> 
      Download Data
    </button>
  </div>

    
    <table class="table table-bordered border-dark text-center">
      <thead>
        <tr>
          <th scope="col">NO</th>
          <th scope="col">Penanggung Jawab</th>
          <th scope="col">NAMA BARANG</th>
          <th scope="col">Terjual</th>
          <th scope="col">Sisa</th>
          <th scope="col">Jumlah Pendapatan</th>
          <th scope="col">Uang Yang Di Bayarkan</th>       
        </tr>  
      </thead>
      <tbody>
        <tr v-for="(transaction, i) in transactions" :key="i">
          <td>{{ i + 1 }}</td>
          <td>{{ transaction.nama }}</td>
          <td>{{ transaction.nama_barang }}</td>
          <td>{{ transaction.terjual }}</td>
          <td>{{ transaction.sisa }}</td>
          <td>{{ transaction.pendapatan }}</td>
          <td>{{ transaction.uang_dibayarkan }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
</template>

<script setup>
import * as XLSX from 'xlsx';
import {saveAs} from 'file-saver';

// mendownload data Excel
const downloadExcel = () => {

// Buat worksheet dari data yang ada di visitors
const worksheet = XLSX.utils.json_to_sheet(transactions.value);

// Buat workbook dan tambahkan worksheet ke dalamnya
const workbook = XLSX.utils.book_new();
XLSX.utils.book_append_sheet(workbook, worksheet, 'data');

// Hasilkan file Excel dalam format array buffer
const excelBuffer = XLSX.write(workbook, { bookType: 'xlsx', type: 'array' });

// Buat Blob dan download file 
const blob = new Blob([excelBuffer], { type: 'application/octet-stream' });
saveAs(blob, 'data-pembayaran_produk.xlsx');
};

definePageMeta({
  middleware: 'auth',
  layout: 'produk',
})

const supabase = useSupabaseClient()
const transactions = ref([])
const selectedDate = ref('');

// const date = new Date().toISOString().split('T')[0];  // Format tanggal 'YYYY-MM-DD'
// const { data, error } = await supabase
//   .from('d_pembayaran')
//   .select('*')
//   .eq('tgl', date);  // sekarang tanggal dikirim sebagai string

const getTransactions = async () => {
  if (selectedDate.value) {
    const { data, error } = await supabase
      .from('d_pembayaran')  // Gantilah 'd_pembayaran' dengan nama view Anda
      .select('*')
      .eq('tgl', selectedDate.value);  // Filter berdasarkan tanggal yang dipilih

    if (data) {
      transactions.value = data;  // Simpan data yang diterima
    } else if (error) {
      console.error('Error fetching data:', error);
    }
  } else {
    console.log('Tanggal belum dipilih.');
  }
};

// const getTransactions = async () => {
//   // const { data, error } = await supabase.from('transaksi').select('*, produk(*)');
//   const { data, error } = await supabase.from('d_pembayaran').select('*')
//   .eq('tgl',new Date().toISOString().split('T')[0]);
//   if (data) transactions.value=data
//   if (error) {
//     console.error('Error fetching transactions:', error);
//     return;
//   }

//   if (data) {
//     transactions.value = data
//   }
// };


// Panggil fungsi untuk mengambil data saat komponen dimuat
// onMounted(() => {
//   getTransactions();
// });

onMounted(() => {
  // Mengambil data untuk tanggal default (misalnya, hari ini)
  selectedDate.value = new Date().toISOString().split('T')[0];  // Format YYYY-MM-DD
  getTransactions();  // Memanggil fungsi untuk mengambil data pertama kali
});
</script>

<style scoped>
.btn {
  border: none;
  color: white;
  background: #39ff27;
  margin: 0 0.5rem; 
  padding: 10px 15px;
  font-size: 16px;
  text-align: center;
  display: inline-block;
  cursor: pointer;
  margin-bottom: 1rem;
}

.icon-button {
  background: none;
  border: none;
  cursor: pointer;
}
.bi {
  width: 40px;
  height: 40px;
  fill: #FFB085; 
}
</style>
