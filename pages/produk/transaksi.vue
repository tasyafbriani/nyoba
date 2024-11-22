<template>
  <div class="container my-5">
    <div class="row align-items-center mb-4">
      <div class="col text-center">
        <h2 class="mb-0">TRANSAKSI PRODUK</h2>
      </div>
    </div>
    <div class="row justify-content-end ">
      <div class="col-1">
        <button class="btn1" @click="saveTransactions" style="background-color: #FFB085; border-radius: 8px;">Kirim</button>
      </div>
    </div>
    
    <table class="table table-bordered border-dark text-center">
      <thead>
        <tr>
          <th>NO</th>
          <th>NAMA</th>
          <th>KELAS</th>
          <th>NAMA BARANG</th>
          <th>HARGA JUAL</th>
          <th>BANYAK BARANG</th>
          <th>TRANSAKSI</th>
          <th>TOTAL</th>
          <th>edit/hapus</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(visitor, i) in visitors" :key="i">
          <td>{{ i + 1 }}.</td>
          <td>{{ visitor.nama }}</td>
          <td>{{ visitor.kelas.nama }}</td>
          <td>{{ visitor.nama_barang }}</td>
          <td>{{ visitor.harga }}</td>
          <td>{{ visitor.jumlah }}</td>
          <td>
            <div class="d-flex justify-content-center align-items-center">
              <input
                type="number"
                v-model.number="visitor.transaksi"
                class="form-control form-control-sm mx-2 text-center"
                style="width: 70px;"
                min="0"
                @change="updateTotal(visitor); updateTerjual(visitor)"
              />
              <button
            class="btn1 btn-outline-secondary btn-sm"
            type="button"
            @click="increment(visitor)"
            :disabled="visitor.transaksi >= visitor.jumlah" 
          >
            +
          </button>
          <button
  class="btn1 btn-outline-danger btn-sm"
  type="button"
  @click="resetTransaction(visitor)"
  style="border-radius: 8px;" 
>
  Batalkan
</button>

            </div>
          </td>
          <td>{{ visitor.total }}</td>
          <td>
  <button @click="editData(visitor)" class="btn btn-primary btn-outline-secondary btn-sm" type="button" style="color:white;">
    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-pencil-square" viewBox="0 0 16 16">
      <path d="M15.502 1.94a.5.5 0 0 1 0 .706L14.459 3.69l-2-2L13.502.646a.5.5 0 0 1 .707 0l1.293 1.293zm-1.75 2.456-2-2L4.939 9.21a.5.5 0 0 0-.121.196l-.805 2.414a.25.25 0 0 0 .316.316l2.414-.805a.5.5 0 0 0 .196-.12l6.813-6.814z"/>
      <path fill-rule="evenodd" d="M1 13.5A1.5 1.5 0 0 0 2.5 15h11a1.5 1.5 0 0 0 1.5-1.5v-6a.5.5 0 0 0-1 0v6a.5.5 0 0 1-.5.5h-11a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 .5-.5H9a.5.5 0 0 0 0-1H2.5A1.5 1.5 0 0 0 1 2.5z"/>
    </svg> 
    Edit
  </button>
  <button @click="confirmDelete(visitor)" class="btn btn-danger btn-outline-secondary btn-sm" type="button" style="color:white;">
    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash" viewBox="0 0 16 16">
      <path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0z"/>
      <path d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4zM2.5 3h11V2h-11z"/>
    </svg> 
    Hapus
  </button>
</td>

        </tr>
      </tbody>
      
    </table>

    <!-- Popup Edit -->
     
    <div v-if="isEditing" class="fixed inset-0 bg-opacity-50 flex justify-center items-center"
     style="background-color: #fff6f4; border-radius: 20px; width: 90%; height: 90%; max-width: 800px; max-height: 800px; right: 30%; transform: translateX(30%);">

      <div class="col text-center">
        <h2 class="mb-4">Edit Data</h2>
      </div>


        <!-- Form Edit -->
        <div class="row justify-content-center">
          <div class="col-lg-6 col-md-8">
            <div class="text-center mb-3">
            <input  v-model="selectedVisitor.nama" type="text" class="form-control form-control-lg radius" placeholder="Nama:" />
          </div>
        <div class="text-center mb-3">
            <select  v-model="selectedVisitor.kelas"  class="form-control form-select-lg radius" aria-label="Kelas">
              <option value="" selected>Kelas</option>
              <option v-for="(member, i) in members" :key="i" :value="member.id">{{ member.nama }}</option>
            </select>
          </div>
          <div class="text-center mb-3">
            <input v-model="selectedVisitor.nama_barang" type="text" class="form-control form-control-lg radius" placeholder="Nama Barang:" />
          </div>
          <div class="text-center mb-3">
            <input  v-model="selectedVisitor.harga" type="text" class="form-control form-control-lg radius" placeholder="Harga:" />
          </div>
        <div class="text-center mb-3">
            <input  v-model="selectedVisitor.jumlah" type="number" class="form-control form-control-lg radius" placeholder="Jumlah:" />
          </div>
        </div>
  

        <!-- Tombol Simpan dan Batal -->
        <div class="mb-5 d-flex justify-content-center">
          <button @click="updateData" class="btn btn-primary">Save</button>
          <button @click="isEditing = false" class="btn btn-danger">Cancel</button>
        </div>
      </div>
    </div>
    </div>
    

    <!-- Modal Konfirmasi Hapus -->
    <div v-if="showConfirmModal" class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center">
      <div class="bg-white w-11/12 md:w-96 p-6 rounded-lg shadow-xl">
        <h3 class="text-lg font-semibold mb-4">Konfirmasi Hapus</h3>
        <p>{{ visitorToDelete?.nama }} yakin mau dihapus?</p>
        <div class="flex justify-between mt-4">
          <button @click="hapusData" class="bg-red-500 hover:bg-red-700 text-white py-2 px-4 rounded">Delete</button>
          <button @click="showConfirmModal = false" class="bg-gray-300 hover:bg-gray-400 text-black py-2 px-4 rounded">Cancel</button>
        </div>
      </div>
    </div>
</template>

<script setup>
import Swal from 'sweetalert2';

definePageMeta({
  middleware: 'auth',
  layout: 'produk',
})

const supabase = useSupabaseClient()
const visitors = ref([]);
const userID = ref();
const user = useSupabaseUser()
const members=ref ([]);
const isEditing = ref(false);
const selectedVisitor = ref(null);
const showConfirmModal = ref(false); // Untuk mengontrol tampilan modal konfirmasi
const visitorToDelete = ref(null);


const getkelas = async () => {
    const { data, error } = await supabase.from("kelas").select("*")
    if(data) members.value = data
};
// Fungsi untuk mendapatkan produk dan jumlah terjual
const getproduk = async () => {
  const { data: produkData, error: produkError } = await supabase
    .from('produk')
    .select(`*, kelas(*)`)
    .eq('tgl',new Date().toISOString().split('T')[0])
    .order('id', { ascending: false });
  
  if (produkData) {
    visitors.value = produkData.map(item => ({ ...item, transaksi: 0, total: 0, terjual: 0 }));
    // console.log(produkData)
    // visitors.value = produkData
  }

  // Mengambil data transaksi untuk menghitung jumlah terjual
 
}

// Fungsi untuk mengupdate total berdasarkan quantity
const updateTotal = (visitor) => {
  visitor.total = visitor.transaksi * visitor.harga; // Hitung total
}

// Fungsi untuk menambah transaksi
const increment = (visitor) => {
  if (visitor.transaksi <= visitor.jumlah) {
    visitor.transaksi += 1; // Hanya tambah jika kurang dari jumlah
    updateTotal(visitor); // Update total setelah penambahan
  } else {
    alert("Jumlah transaksi tidak boleh lebih dari jumlah barang tersedia."); // Pesan kesalahan
  }
}

async function getUserLogin() {
  const {data,error } = await supabase
    .from("login") 
    .select() 
    .eq("user_id", user.value.id)
    .single()
  if(data){
    userID.value = data.id
  }
  
}

const resetTransaction = (visitor) => {
  visitor.transaksi = 0; // Kembalikan nilai transaksi ke 0
  visitor.terjual = 0; // Kembalikan nilai terjual ke 0
  updateTotal(visitor); // Perbarui total setelah reset
}
// Fungsi untuk menyimpan semua transaksi ke database
const saveTransactions = async () => {
  const transactionsToSave = visitors.value
    .filter(visitor => visitor.transaksi > 0) // Ambil yang memiliki transaksi
    .map(visitor => ({
      terjual: visitor.terjual + visitor.transaksi, // Jumlah terjual baru
      quantity: visitor.transaksi,
      total: visitor.total,
      produk: visitor.id,
      id_user :  userID.value// Sesuaikan dengan nama kolom di tabel transaksi
    }));

  if (transactionsToSave.length > 0) {
    const { data, error } = await supabase.from('transaksi').insert(transactionsToSave);
    
    if (error) {
      console.error('Error saving transactions:', error);
      alert("Terjadi kesalahan saat menyimpan transaksi."); // Pesan kesalahan
    } else {
      console.log('Transactions saved:', data);
      Swal.fire({
        icon: "success",
        title: "Hore...",
        text: "Transaksi Berhasil Disimpan",
      });// Pesan sukses
      // Reset transaksi setelah disimpan
      visitors.value.forEach(visitor => {
        visitor.transaksi = 0; // Reset transaksi
        // Tidak perlu update terjual di sini, karena sudah diupdate
      });
    }
  } else {
    //const { data, error } = await supabase.from('transaksi').update(transactionsToSave);
    Swal.fire({
  icon: "error",
  title: "Oops...",
  text: "kamu belum melakukan Transaksi",
}); // Pesan kesalahan
  }
}

onMounted(() => {
  getkelas();
  getproduk();
  getUserLogin();
});

const updateData = async () => {
  const { error } = await supabase.from("produk").update({
    nama: selectedVisitor.value.nama,
    kelas: selectedVisitor.value.kelas,
    nama_barang: selectedVisitor.value.nama_barang,
    harga: selectedVisitor.value.harga,
    jumlah: selectedVisitor.value.jumlah
  }).eq('id', selectedVisitor.value.id);

  if (!error) {
    isEditing.value = false;
    getproduk();
  } else {
    console.error("Error updating data:", error);
  }
};

const hapusData = async () => {
  if (visitorToDelete.value) {
    const { error } = await supabase.from("produk").delete().eq("id", visitorToDelete.value.id);
    if (!error) {
      getproduk(); // Refresh data tabel
    } else {
      console.error("Error deleting data:", error);
    }
    showConfirmModal.value = false; // Tutup modal setelah menghapus
    visitorToDelete.value = null; // Reset data setelah menghapus
  }
};


const editData = (visitor) => {
  selectedVisitor.value = { ...visitor };
  isEditing.value = true;
};

const confirmDelete = (visitor) => {
  visitorToDelete.value = visitor; // Simpan data visitor yang akan dihapus
  showConfirmModal.value = true; // Tampilkan modal konfirmasi
};
</script>

<style scoped>
.btn1 {
  border: none;
  color: white;
  background: #FFB085; 
  margin: 4px 5px;
  padding: 10px 15px;
  font-size: 16px;
  text-align: center;
  display: inline-block;
  cursor: pointer;
}
.btn {
  border: none;
  margin: 4px 5px;
  padding: 10px 15px;
  font-size: 16px;
  text-align: center;
  display: inline-block;
  cursor: pointer;
}

.icon-button {
  background:none;
  border: none;
  cursor: pointer;
  padding: 10px; 
  border-radius: 0.375rem;
}


.form-control {
  border-radius: 0.375rem;
}

.text-center {
  text-align: center;
}

</style>
