<template>
  <div class="container pt-5">
    <div class="row justify-content-center">
      <div class="col">
        <div id="carouselExampleIndicators" class="carousel slide" data-bs-ride="true">
          <div class="carousel-indicators">
            <button type="button" data-bs-target="#carouselExampleIndicators" data-bs-slide-to="0" class="active"
              aria-current="true" aria-label="Slide 1"></button>
            <button type="button" data-bs-target="#carouselExampleIndicators" data-bs-slide-to="1"
              aria-label="Slide 2"></button>
            <button type="button" data-bs-target="#carouselExampleIndicators" data-bs-slide-to="2"
              aria-label="Slide 3"></button>
          </div>
          <div class="carousel-inner rounded-5">
            <div class="carousel-item active">
              <img src="/assets/img/1.png" class="d-block w-100" alt="...">
            </div>
            <div class="carousel-item">
              <img src="/assets/img/2.png" class="d-block w-100" alt="...">
            </div>
            <div class="carousel-item">
              <img src="/assets/img/3.png" class="d-block w-100" alt="...">
            </div>
          </div>
          <button class="carousel-control-prev" type="button" data-bs-target="#carouselExampleIndicators"
            data-bs-slide="prev">
            <span class="carousel-control-prev-icon rounded-5 bg-secondary" aria-hidden="true"></span>
            <span class="visually-hidden">Previous</span>
          </button>
          <button class="carousel-control-next" type="button" data-bs-target="#carouselExampleIndicators"
            data-bs-slide="next">
            <span class="carousel-control-next-icon rounded-5 bg-secondary" aria-hidden="true"></span>
            <span class="visually-hidden">Next</span>
          </button>
        </div>
      </div>
    </div>
    <div class="row pt-3">
      <div class="col">
        <h1>Product Hari Ini</h1>
      </div>
    </div>
    <div class="row justify-content-evenly">
      <div v-for="(produk, i) in produks" :key="i" class="col-6 col-lg-2 d-flex">
        <div class="card flex-fill mb-3 shadow-lg">
          <div class="card-body">
            <img :src="produk.foto" class="cover" :alt="produk.id"
              src="https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.svgrepo.com%2Fsvg%2F508699%2Flandscape-placeholder&psig=AOvVaw2-SWmfk33NzXubPfqn0P16&ust=1714794757874000&source=images&cd=vfe&opi=89978449&ved=0CBAQjRxqFwoTCNjln7nK8IUDFQAAAAAdAAAAABAE://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.svgrepo.com%2Fsvg%2F508699%2Flandscape-placeholder&psig=AOvVaw2-SWmfk33NzXubPfqn0P16&ust=1714794757874000&source=images&cd=vfe&opi=89978449&ved=0CBAQjRxqFwoTCNjln7nK8IUDFQAAAAAdAAAAABAE" /><br>
            <div class="card-body">
              <h3 class="card-text">{{ produk.nama_barang }}</h3>
              <h5 class="card-title">Rp.{{ produk.harga }}</h5>
              <h5 class="card-title">tersedia: {{ produk.jumlah }}</h5>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
const supabase = useSupabaseClient();

const keyword = useState('keyword')
const { data: produks } = useAsyncData('produk', async () => {
  let query = supabase
    .from("produk")
    .select(`*, kelas(*)`)
    .eq('tgl',new Date().toISOString().split('T')[0]);
  if (keyword.value) query = query.ilike("nama_barang", `%${keyword.value}%`)
  const { data, error } = await query
  if (error) throw error
  return data
})
</script>
<style scoped>
@media (max-width:1024px){
  
}
@media (max-width:768px){

}
@media (max-width:480px){

}
.card:hover {
  transform: scale(1.05);
  box-shadow: 4px 4px 20px #2e2e2eae !important;
}
.card {
  transition: all 0.2s ease-in-out;
}
.cover {
  width: 100%;
}
</style>