<template>
  <div class="horizontal-menu">
    <!-- <nav class="navbar top-navbar col-lg-12 col-12 p-0">
      <div class="nav-top flex-grow-1">
        <div class="container d-flex flex-row h-100 align-items-center">
          <div
            class="text-center navbar-brand-wrapper d-flex align-items-center justify-content-center"
          >
            <a class="navbar-brand brand-logo disabled"
              ><img src="../../../public/img/logo.png"
            /></a>
            <h2 class="navbar-h2">Pengaduan Masyarakat</h2>
          </div>
          <div
            class="navbar-menu-wrapper d-flex align-items-center justify-content-end flex-grow-1"
          >
            <ul class="navbar-nav navbar-nav-right">
              <li class="nav-item nav-profile dropdown">
                <a
                  class="nav-link dropdown-toggle"
                  href="#"
                  data-toggle="dropdown"
                  id="profileDropdown"
                >
                  Hallo, {{ nama }}
                </a>
                <div
                  class="dropdown-menu dropdown-menu-right navbar-dropdown"
                  aria-labelledby="profileDropdown"
                >
                  <span @click="logout" class="dropdown-item">
                    <i class="mdi mdi-logout text-primary"></i>
                    Logout
                  </span>
                </div>
              </li>
            </ul>
            <button
              class="navbar-toggler navbar-toggler-right d-lg-none align-self-center"
              type="button"
              data-toggle="horizontal-menu-toggle"
            >
              <span class="mdi mdi-menu"></span>
            </button>
          </div>
        </div>
      </div>
    </nav> -->

    <div class="wrapper d-flex align-items-stretch">
      <nav id="sidebar">
        <div class="p-4 pt-5">
          <h1 class="judul">TRIPKET</h1>
          <ul class="list-unstyled components mb-5">
            <li class="nav-item">
              <router-link to="/" class="nav-link">
                <i class="mdi mdi-view-dashboard-outline menu-icon"></i>
                <span class="menu-title"> Beranda</span>
              </router-link>
            </li>
            <li class="nav-item">
              <router-link :to="{ name: 'masyarakat' }" class="nav-link">
                <i class="mdi mdi-account-multiple-plus menu-icon"></i>
                <span class="menu-title"> Masyarakat</span>
              </router-link>
            </li>
            <li class="nav-item">
              <router-link :to="{ name: 'kereta' }" class="nav-link">
                <i class="mdi mdi-train menu-icon"></i>
                <span class="menu-title"> Kereta</span>
              </router-link>
            </li>
            <li class="nav-item">
              <router-link :to="{ name: 'pesawat' }" class="nav-link">
                <i class="mdi mdi-airplane menu-icon"></i>
                <span class="menu-title"> Pesawat</span>
              </router-link>
            </li>
            <li class="nav-item">
              <router-link :to="{ name: 'bus' }" class="nav-link">
                <i class="mdi mdi-bus menu-icon"></i>
                <span class="menu-title"> Bus</span>
              </router-link>
            </li>

            <li class="nav-item">
              <router-link :to="{ name: 'riwayat' }" class="nav-link">
                <i class="mdi mdi-history menu-icon"></i>
                <span class="menu-title"> Riwayat Transaksi</span>
              </router-link>
            </li>

            <li>
              <p class="bottom">
                Copyright &copy; All rights reserved | This template is made
                with <i class="icon-heart" aria-hidden="true"></i> by Trip'n
                kets
              </p>
            </li>
          </ul>
        </div>
      </nav>
    </div>

    <div id="content1" class="p-4 p-md-5">
      <nav class="navbar navbar-expand-lg navbar-light">
        <div class="container-fluid">
          <button
            class="btn btn-dark d-inline-block d-lg-none ml-auto"
            type="button"
            data-toggle="collapse"
            data-target="#navbarSupportedContent"
            aria-controls="navbarSupportedContent"
            aria-expanded="false"
            aria-label="Toggle navigation"
          >
            <i class="fa fa-bars"></i>
          </button>

          <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav navbar-nav-left">
              <li class="nav-item nav-profile dropdown">
                <a
                  class="nav-link dropdown-toggle"
                  href="#"
                  data-toggle="dropdown"
                  id="profileDropdown"
                >
                  Hallo, {{ name }}
                </a>
                <div
                  class="dropdown-menu dropdown-menu-right navbar-dropdown"
                  aria-labelledby="profileDropdown"
                >
                  <span @click="logout" class="dropdown-item">
                    <i class="mdi mdi-logout text-primary"></i>
                    Logout
                  </span>
                  <!-- <span @click="updatePassword" class="dropdown-item">
                    <i class="mdi mdi-account-key text-primary"></i>
                    Ubah Password
                  </span> -->
                </div>
              </li>
            </ul>

            <ul class="nav navbar-nav ml-auto">
              <form class="d-flex">
                <input
                  class="form-control me-2"
                  type="search"
                  placeholder="Search"
                  aria-label="Search"
                />
                <button class="btn btn-outline-dark" type="submit">
                  Search
                </button>
              </form>
            </ul>
          </div>
        </div>
      </nav>
    </div>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      name: "",
    };
  },
  // name: "navbar",
  computed: {
    isLoggedIn: function () {
      return this.$store.getters.isLoggedIn;
    },
    email: function () {
      return this.$store.getters.userDetail.name;
    },
    role: function () {
      return this.$store.getters.userDetail.role;
    },
  },
  methods: {
    logout: function () {
      let conf = {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("Authorization"),
        },
      };
      this.axios
        .post("/logout", conf)
        .then((response) => {
          if (response.data.status === false) {
            this.$store.commit("logout");
            localStorage.removeItem("Authorization");
            this.$router.push("/login");
          }
        })
        .catch((error) => {
          this.$store.commit(error, "logout");
        });
    },
  },
  mounted() {
    this.name = localStorage.getItem("name");
  },
};
</script>
