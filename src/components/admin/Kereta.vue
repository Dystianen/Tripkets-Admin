<template>
  <div class="container mt-3">
    <div class="row">
      <div class="col-lg-12 grid-margin stretch-card">
        <div class="card">
          <div class="card-body">
            <p class="card-title float-left"><b>JADWAL KERETA</b></p>
            <p class="card-description float-right">
              <b-button variant="info" v-b-modal.modal v-on:click="Add"
                ><i class="mdi mdi-plus btn-icon-prepend"></i> Add</b-button
              >
            </p>
            <div class="table-responsive">
              <b-table striped hover :items="transportations" :fields="fields">
                <template v-slot:cell(action)="data">
                  <b-button
                    size="sm"
                    variant="success"
                    v-on:click="Edit(data.item)"
                    v-b-modal.modal
                    ><i class="mdi mdi-pencil"></i> Edit</b-button
                  >
                  <b-button
                    size="sm"
                    variant="danger"
                    v-on:click="Drop(data.item.id_transportation)"
                    ><i class="mdi mdi-delete"></i> Delete</b-button
                  >
                </template>
                <template v-slot:cell(transportation_type)="data">
                  {{ data.item.category.transportation_type }}
                </template>
                <template v-slot:cell(train_image)="data">
                  <img
                    style="width: 200px; height: 100px; border-radius: 5%"
                    :src="'http://localhost:8000/uploads/' + data.item.image"
                  />
                </template>
              </b-table>
              <b-pagination
                v-model="currentPage"
                :per-page="perPage"
                :total-rows="rows"
                align="center"
                v-on:input="pagination"
                class="mt-3"
              >
              </b-pagination>

              <b-toast id="loadingToast" title="Processing Data" no-auto-hide>
                <b-spinner label="Spinning" variant="secondary"></b-spinner>
                <strong class="text-secondary"> Loading...</strong>
              </b-toast>

              <!-- toast untuk tampilan message box -->
              <b-toast id="message" title="Message">
                <strong class="text-success">{{ message }}</strong>
              </b-toast>
            </div>
          </div>
        </div>
      </div>
    </div>
    <b-modal id="modal" @ok="Save">
      <template v-slot:modal-title> Form Input </template>
      <form ref="form">
        <div class="form-group">
          <label for="transportation_name" class="col-form-label"
            >Transportation Name</label
          >
          <input
            type="text"
            name="transportation_name"
            class="form-control"
            id="transportation_name"
            placeholder="name transportation"
            v-model="transportation_name"
          />
        </div>
        <div class="form-group">
          <label for="id_category" class="col-form-label">Id Category</label>
          <select
            class="form-control"
            name="id_category"
            id="id_category"
            v-model="id_category"
            aria-placeholder=""
          >
            <option value="1">Bus</option>
            <option value="2">Kereta</option>
            <option value="3">Pesawat</option>
          </select>
        </div>
        <div class="form-group">
          <label for="p_depart" class="col-form-label"
            >Place of Departure</label
          >
          <input
            name="p_depart"
            class="form-control"
            id="p_depart"
            v-model="p_depart"
            placeholder="Place of Departure"
          />
        </div>
        <div class="form-group">
          <label for="p_till" class="col-form-label">Place of Till</label>
          <input
            name="p_till"
            class="form-control"
            id="p_till"
            v-model="p_till"
            placeholder="Place of Till"
          />
        </div>
        <div class="form-group">
          <label for="price" class="col-form-label">Price</label>
          <input
            type="numeric"
            name="price"
            class="form-control"
            id="price"
            placeholder="price"
            v-model="price"
          />
        </div>
        <div class="form-group">
          <label for="departure" class="col-form-label">Departure</label>
          <input
            type="datetime-local"
            name="departure"
            class="form-control"
            id="departure"
            placeholder=""
            v-model="departure"
          />
        </div>
        <div class="form-group">
          <label for="till" class="col-form-label">till</label>
          <input
            type="datetime-local"
            name="till"
            class="form-control"
            id="till"
            placeholder=""
            v-model="till"
          />
        </div>
        <div class="form-group">
          <label for="image" class="col-form-label">File</label>
          <input
            type="file"
            name="image"
            class="form-control"
            id="image"
            placeholder="upload"
          />
        </div>
      </form>
    </b-modal>
  </div>
</template>

<script>
module.exports = {
  data: function () {
    return {
      search: "",
      id_transportation: "",
      id_category: "",
      transportation_type: "",
      transportation_name: "",
      p_depart: "",
      p_till: "",
      price: "",
      departure: "",
      till: "",
      image: "",
      action: "",
      message: "",
      pagination: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      key: "",
      category: "",
      transportations: [],
      user: "",
      fields: [
        // "id_transportation",
        "train_image",
        "transportation_name",
        "p_depart",
        "p_till",
        "price",
        "departure",
        "till",
        "action",
      ],
    };
  },

  methods: {
    getData: function () {
      let conf = { headers: { Authorization: "Bearer " + this.key } };
      // let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      this.axios
        .get("/train", conf)
        .then((response) => {
          if (response.data.status) {
            this.$bvToast.hide("loadingToast");
            this.transportations = response.data.data.transportations;
            this.category = response.data.data.transportations.category;
            this.rows = response.data.data.count;
          } else {
            this.$bvToast.hide("loadingToast");
            this.message = "Gagal menampilkan data petugas.";
            this.$bvToast.show("message");
            this.$router.push({ name: "login" });
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },

    Add: function () {
      this.action = "insert";
      this.id_transportation = "";
      this.id_category = "";
      this.transportation_name = "";
      this.price = "";
      this.p_depart = "";
      this.p_till = "";
      this.departure = "";
      this.till = "";
      this.image = "";
      document.getElementById("image").value = null;
    },

    Edit: function (item) {
      this.action = "update";
      this.id_transportation = item.id_transportation;
      this.id_category = item.id_category;
      this.transportation_name = item.transportation_name;
      this.p_depart = item.p_depart;
      this.p_till = item.p_till;
      this.price = item.price;
      this.departure = item.departure;
      this.till = item.till;
    },

    Save: function () {
      let conf = { headers: { Authorization: "Bearer " + this.key } };
      this.$bvToast.show("loadingToast");
      if (this.action === "insert") {
        let form = new FormData();
        // form.append("id", this.id);
        form.append("id_transportation", this.id_transportation);
        form.append("id_category", this.id_category);
        form.append("transportation_name", this.transportation_name);
        form.append("p_depart", this.p_depart);
        form.append("p_till", this.p_till);
        form.append("price", this.price);
        form.append("departure", this.departure);
        form.append("till", this.till);
        form.append("image", document.getElementById("image").files[0]);

        this.axios
          .post("/train", form, conf)
          .then((response) => {
            this.$bvToast.hide("loadingToast");
            this.$router.push("/kereta");
            this.message = response.data.message;
            this.$bvToast.show("message");
            this.getData();
            // console.log(this.id_transportation);
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        let form = {
          id_transportation: this.id_transportation,
          id_category: this.id_category,
          transportation_name: this.transportation_name,
          p_depart: this.p_depart,
          p_till: this.p_till,
          price: this.price,
          departure: this.departure,
          till: this.till,
        };
        this.axios
          .put("/train/" + this.id_transportation, form, conf)
          .then((response) => {
            this.$bvToast.hide("loadingToast");
            if (this.search == "") {
              this.getData();
            } else {
              this.searching();
            }
            this.message = response.data.message;
            this.$bvToast.show("message");
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
    Drop: function (id_transportation) {
      let conf = { headers: { Authorization: "Bearer " + this.key } };
      if (confirm("Apakah anda yakin ingin menghapus data ini?")) {
        this.$bvToast.show("loadingToast");
        this.axios
          .delete("/train/" + id_transportation, conf)
          .then((response) => {
            this.getData();
            this.$bvToast.hide("loadingToast");
            this.message = response.data.message;
            this.$bvToast.show("message");
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
  },
  mounted() {
    this.key = localStorage.getItem("Authorization");
    this.getData();
  },
};
</script>