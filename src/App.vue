
<template >
  <div id="app">
    <!-- Ketika disubmit akan memanggil method add -->
    <form @submit.prevent="add">
      <input type="hidden" v-model="form.id" />
      <input type="text" v-model="form.name" />
      <!-- Jika kondisi sekarang tidak sedang update maka tombol update ini tidak muncul -->
      <button type="submit" v-show="!updateSubmit">Add</button>
      <!-- jika tombol edit di klik maka tombol add akan berubah menjadi update -->
      <button type="button" v-show="updateSubmit" @click="update(form)">
        Update
      </button>
    </form>
    <table class="table" border="1">
      <thead>
        <tr>
          <th>Nama</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody v-for="user in users" :key="user.id">
        <tr>
          <!--  Untuk menampilkan nama -->
          <td>
            <span>{{ user.name }}</span>
          </td>
          <td>
            <!-- Tombol edit dan hapus -->
            <button @click="edit(user)">Edit</button>
            <button @click="del(user)">Delete</button>
          </td>
        </tr>
        <tr></tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// Import axios untuk fetch data API
import axios from "axios";
export default {
  // Data
  data() {
    return {
      // Mengembalikan nilai
      form: {
        id: "", // Deklarasi id dan nama sebagai kosong
        name: "",
      },
      users: "",
      updateSubmit: false,
    };
  },
  mounted() {
    // Dieksekusi oleh vue setelah template dirender
    this.load(); // memuat load
  },
  methods: {
    load() {
      // Mendeklarasikan apa funsgi method load
      // Mengambil data dari url menggunakan axios dengan http method Get
      axios
        .get("http://localhost:3000/users")
        .then((res) => {
          //respon dari rest api dimasukan ke users
          this.users = res.data;
          // Menangkap jika program error
        })
        .catch((err) => {
          // Akan di tampilkan pada console
          console.log(err);
        });
    },
    // Methods button add
    add() {
      /**
       * Di fungsi add() itu kita melakukan tambah data
       * dengan method POST, dan memasukan form yg kita input
       * nanti ke dalam data REST API. lalu saat melakukan
       * request yg tadi data nya akan muncul di panggil
       * oleh this.load() dan saat data dimasukan text dari form input akan hilang.
       */
      axios.post("http://localhost:3000/users/", this.form).then(() => {
        this.load();
        this.form.name = "";
      });
    },
    // Method edit dan update data
    edit(user) {
      /**
       * Di fungsi edit(user) kita menambahkan parameter user
       * dari tombol edit(user), ketika tombol edit itu
       * di klik maka tombol add akan hilang dan berganti menjadi
       * update dan list user di tampilkan di form input.
       */
      this.updateSubmit = true; // Mengenable tombol update
      this.form.id = user.id; // Mengisi input bernama id dengan id data itu sendiri
      this.form.name = user.name; // Mengisi input bernama name dengan name data itu sendiri
    },
    // Methods update
    update(form) {
      /**
       * Di fungsi update(form) kita menambahkan parameter
       * form dari tombol update(form), ketika tombol update
       * di klik inputan dari form.id dan inputan dari form.name
       * akan dimasukan ke object name lalu di tampilkan dengan
       * this.load() dan inputan dari form.name akan hilang
       * bersama dengan tombl update akan menjadi tombol add.
       */
      // Memasukan data yg setelah di update dengan metode put dengan id
      // yang diambil dari form.id dan body put ini adalah name
      // yang diambil dari this.form.name
      return axios
        .put("http://localhost:3000/users/" + form.id, {
          name: this.form.name,
        })
        .then(() => {
          this.load(); // Memuat semua data kembali setelah terjadinya update data
          this.form.id = ""; // Set id pada form menjadi kosong
          this.form.name = ""; // Set name pada form menjadi kosong
          this.updateSubmit = false; // Menghilangkan tombol update
        });
    },
    // Method delete
    del(user) {
      /**
       * Di fungsi del(user) kita menambahkan parameter user
       * dari tombol del(user) ini mengarah ke list bernama user,
       * lalu ditambah dengan object id dari API dan menampilkan list
       * yang ada dengan this.load().
       */
      axios.delete("http://localhost:3000/users/" + user.id).then(() => {
        this.load(); // Memuat semua data kembali setelah terjadinya update data
      });
    },
  },
};
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  padding: 20px;
}

input {
  padding: 8px;
}
button {
  cursor: pointer;
  margin: 0 4px;
  padding: 4px 10px;
  border-radius: 4px;
  color: white;
}
button:nth-child(1) {
  background-color: transparent;
  border: 2px solid green;
  color: green;
}
button:nth-child(1):hover {
  background-color: green;
  border: 2px solid green;
  color: white;
}
button:nth-child(2):hover {
  background-color: red;
  border: 2px solid red;
  color: white;
}
button:nth-child(2) {
  background-color: transparent;
  border: 2px solid red;
  color: red;
}
button:nth-child(3),
button:nth-child(4) {
  background-color: green;
  padding: 8px 18px;
  border: 2px solid green;
}
table {
  text-align: center;
  margin: 20px auto;
  border-radius: 4px;
}
th {
  background: #35a9d3;
  color: white;
}
th,
td {
  padding: 8px 20px;
  border: 1px solid #999;
}
</style>
