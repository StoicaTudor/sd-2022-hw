<template>
  <v-card>
    <v-card-title>
      Books
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        @change="searchBooks"
      ></v-text-field>
      <v-btn @click="addBook" v-if="$store.getters['auth/isAdmin']">Add Book</v-btn>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="books"
      @click:row="editBook"
    ></v-data-table>
    <BookDialog
      :opened="dialogVisible"
      :book="selectedBook"
      :genres="genres"
      @refresh="refreshList"
      v-on:close="closeDialog"
    ></BookDialog>
  </v-card>
</template>

<script>
import api from "../api";
import BookDialog from "../components/BookDialog";

export default {
  name: "BookList",
  components: { BookDialog },
  data() {
    return {
      books: [],
      search: "",
      headers: [
        {
          text: "Title",
          align: "start",
          sortable: false,
          value: "title",
        },
        { text: "Description", value: "description" },
        { text: "Author", value: "author" },
        { text: "Genre", value: "genre" },
        { text: "Price", value: "price" },
        { text: "Quantity", value: "quantity"},
      ],
      dialogVisible: false,
      selectedBook: {},
      genres: [],
    };
  },
  methods: {
    editBook(book) {
      this.selectedBook = book;
      this.dialogVisible = true;
    },
    addBook() {
      this.dialogVisible = true;
    },
    closeDialog() {
      this.refreshList();
    },
    async refreshList() {
      this.dialogVisible = false;
      this.selectedBook = {};
      this.books = await api.books.allBooks();
      this.genres = await api.books.getGenres();
      this.search != "" ? this.books = await api.books.filterBooks(this.search) : {};
    },
    searchBooks() {
      this.refreshList();
    },
  },
  created() {
    this.refreshList();
  },
};
</script>

<style scoped></style>
