<template>
  <div id="app">
    <Loader :loading="showLoader" />
    <h2>Wordpress posts</h2>
    <ul class="flex-container">
      <li v-for="item in listItems" :key="item.id" class="flex-item">
        <json-viewer
          :value="item"
          :expand-depth="0"
          copyable
          boxed
          sort
        ></json-viewer>
      </li>
      <li v-if="listItems.length === 0" class="flex-item center">
        No Record Found
      </li>
    </ul>
    <ul class="showItems">
      <li>
        Show Items:
        <select @change="onChangeRecordsPerPage" v-model="recordsPerPage">
          <option :value="5">5</option>
          <option :value="10">10</option>
          <option :value="20">25</option>
          <option :value="30">50</option>
        </select>
      </li>
    </ul>
    <Pagination
      v-if="listItems"
      :total-pages="totalPages"
      :per-page="recordsPerPage"
      :current-page="page"
      @pagechanged="onPageChange"
    />
  </div>
</template>

<script>
import axios from "axios";
import Pagination from "./components/Pagination";
import Loader from "./components/Loader";
import { baseApiURL } from "@/config/env";
import JsonViewer from "vue-json-viewer";

export default {
  components: {
    Pagination,
    Loader,
    JsonViewer,
  },
  data() {
    return {
      showLoader: false,
      listItems: [],
      page: 1,
      totalPages: 0,
      totalRecords: 100, // alternatively you can return total records from api
      recordsPerPage: 10,
    };
  },
  created() {
    this.loadListItem();
  },
  methods: {
    loadListItem() {
      this.showLoader = true;
      axios
        .get(`${baseApiURL}?page=${this.page}&per_page=${this.recordsPerPage}`)
        .then((response) => {
          this.showLoader = false;
          this.listItems = response.data;
          this.totalPages = Math.floor(this.totalRecords / this.recordsPerPage); // totalrecords / recordPerPage
          this.totalRecords = 100;
        });
    },
    onPageChange(page) {
      this.page = page;
      this.loadListItem();
    },
    onChangeRecordsPerPage() {
      this.loadListItem();
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}
h2 {
  text-align: center;
}
ul li {
  list-style-type: none;
}
ul.flex-container {
  padding: 0;
  margin: 0;
  list-style-type: none;
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
  flex-direction: row wrap;
  flex-wrap: wrap;
  justify-content: space-around;
  .flex-item {
    border: 1px solid;
    border-radius: 4px;
    width: calc(100% / 5.5);
    padding: 5px;
    height: auto;
    margin-top: 10px;
    color: white;
    font-weight: bold;
    text-align: left;
  }
}
.showItems {
  display: inline-block;
  margin-left: -35px;
  li {
    list-style-type: none;
    display: inline-block;
    margin-left: 10px;
  }
}
</style>
