<template>
  <div id="app">
    <h1>Candy Products</h1>
    <form id="candy_products_form" action="javascript:void(0)">
      <input id="candy_product_name" type="text" placeholder="Name" />
      <input id="candy_product_desc" type="text" placeholder="Description" />
      <input id="candy_product_img" type="text" placeholder="Image URL" />
      <input
        id="candy_product_weight"
        type="text"
        placeholder="Weight(in grams)"
      />
      <input
        id="candy_product_price"
        type="text"
        placeholder="Price (in dollars)"
      />
      <input @click="post_candy_product" type="submit" value="Submit" />
    </form>
    <h3>{{ post_status }}</h3>
    <div id="candy_product_container">
      <div
        v-for="candy_product in candy_products"
        :key="candy_product[3]"
        class="candy_product_card"
      >
        <img :src="candy_product[2]" :alt="candy_product[1]" />
        <h3 class="candy_product_title">{{ candy_product[0] }}</h3>
        <p class="candy_product_desc">{{ candy_product[1] }}</p>
        <p class="candy_product_weight">{{ candy_product[3] }}</p>
        <p class="candy_product_price">{{ candy_product[4] }}</p>
        <input
          type="submit"
          class="candy_product_delete_button"
          value="delete_candy_product"
          @click="candy_product(candy_product[3])"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      candy_products: [],
      post_status: "",
    };
  },
  methods: {
    delete_candy_product(id) {
      this.post_status = "Deleting Candy Product!";
      axios
        .request({
          url: `${process.env.VUE_APP_BASE_DOMAIN}/api/candy_product`,
          method: "DELETE",
          data: {
            id: id,
          },
        })
        .then((response) => {
          console.log(response);
          this.candy_products = this.candy_products.filter(
            (candy_product) => candy_product[3] != id
          );
          this.post_status = "Villain Deleted";
        })
        .catch((error) => {
          console.log(error);
          this.post_status = "Error Deleting Villain, Sorry!";
        });
    },
    post_candy_product() {
      this.post_status = "Posting New Candy Product!";
      axios
        .request({
          url: `${process.env.VUE_APP_BASE_DOMAIN}/api/candy_product`,
          method: "POST",
          data: {
            name: document.getElementById("candy_product_name").value,
            desc: document.getElementById("candy_product_desc").value,
            img: document.getElementById("candy_product_img").value,
            weight: document.getElementById("candy_product_weight").value,
            price: document.getElementById("candy_product_price").value,
          },
        })
        .then((response) => {
          console.log(response);
          document.getElementById("candy_product_form").reset();
          this.candy_products.push(response.data);
          this.post_status = "Candy Post Created!";
        })
        .catch((error) => {
          console.log(error);
          this.post_status = "Errpr Posting New Candy Product, Sorry!";
        });
    },
  },
  mounted() {
    this.post_status = "Loading Candy Products...";
    axios
      .request({
        url: `${process.env.VUE_APP_BASE_DOMAIN}/api/candy_product`,
        method: "GET",
      })
      .then((response) => {
        console.log(response);
        this.candy_products = response.data;
        this.post_status = "";
      })
      .catch((error) => {
        console.log(error);
        this.post_status = "Error Loading Candy Products, Sorry!";
      });
  },
};
</script>

<style>
img {
  max-width: 100%;
  object-fit: cover;
}

input[type="text"],
input[type="text"]:focus,
input[type="text"]:active {
  border: none;
  border-bottom: 2px solid black;
  margin: 10px;
  outline: none;
}

input[type="submit"]:hover {
  cursor: pointer;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

form {
  display: grid;
  place-items: center;
  width: 50%;
}

#candy_product_container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  column-gap: 50px;
  row-gap: 50px;
  width: 90%;
  margin-left: 5%;
  place-items: center;
  margin-top: 50px;
}

.candy_product_title {
  text-align: left;
  padding-left: 15px;
}

.candy_product_desc {
  text-align: left;
  padding-left: 15px;
}

#candy_product_container img {
  width: 100%;
  border-radius: 15px;
}

.candy_product_card {
  box-shadow: 3px 3px 6px grey;
  border-radius: 15px;
  transition: all 0.25s ease-in-out;
  max-width: 400px;
}

.candy_product_card:hover {
  box-shadow: 6px 6px 9px grey;
}

.candy_product_card:focus,
.candy_product_card:active {
  box-shadow: 1px 1px 2px grey;
}

.candy_product_delete_button {
  margin: 10px;
}
</style>
