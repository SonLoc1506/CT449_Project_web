<template>
    <main>
      <div class="banner">
        <img src="@/assets/images/Library.jpeg" alt="" class="img-fluid" width="40%">
      </div>
      <div class="container">
       
        <!-- Tìm kiếm sách -->
        <div class="input-group mt-5 search-input">
            <input type="text" v-model="searchKeyword" class="form-control" placeholder="Nhập tên sách" aria-label="Tìm kiếm sách" aria-describedby="basic-addon2" id="searchInput" name="name" @keydown.enter="search">
            <button @click="search" class="input-group-text" type="submit"><span><i class="fa-solid fa-magnifying-glass"></i></span></button>
            <button @click="resetSearch" class="input-group-text" type="button"><span><i class="fa-solid fa-redo"></i></span></button>
        </div>

        <div id="main-page">
          <div class="row">
            <div class="col-md-3 col-sm-6" v-for="book in books" :key="book._id">
              <div class="card mb-4 bg-white">
                <router-link :to="{ name: 'book.detail', params: { id: book._id } }" class="text-black">
                  <img  :src="book.HINHANH" class="card-img-top img-fluid" alt="Book Image">
                  <div class="card-body">
                    <h5 class="card-title text-truncate">{{ book.TENSACH }}</h5>
                  </div>
                </router-link>
                <div class="borrow-button">
                  <button class="btn btn-primary" @click="openModal(book)">Mượn</button>
                </div>
              </div>
            </div>
          </div>
        </div>
  
        <div class="modal" :class="{ 'show': showModal }">
          <div class="modal-dialog">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title">Mượn sách: {{ selectedBook.TENSACH }}</h5>
                <button type="button" class="btn-close" aria-label="Close" @click="closeModal"></button>
              </div>
              <div class="modal-body">
                <div class="mb-3">
                  <label for="note" class="form-label">Ghi chú:</label>
                  <textarea class="form-control" id="note" v-model="note"></textarea>
                </div>
                <div class="mb-3">
                  <label for="borrowDate" class="form-label">Ngày mượn:</label>
                  <input type="date" class="form-control" id="borrowDate" v-model="borrowDate">
                </div>
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" @click="closeModal">Đóng</button>
                <button type="button" class="btn btn-primary" @click="borrowBook">Mượn sách</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </template>
  
  <script>
import BookService from "@/services/book.service";
import BorrowBookService from "@/services/borrowBook.service";
import AdminHeader from "@/components/admin/AdminHeader.vue";

export default {
  components: {
    AdminHeader, 
  },

  data() {
    return {
      books: [],
      showModal: false,
      selectedBook: '',
      note: '',
      borrowDate: '',
      searchKeyword: ''
    };
  },

  async created() {
    try {
      await this.fetchBooks();
    } catch (error) {
      console.error("Error fetching books:", error);
    }
  },

  methods: {
    async fetchBooks() {
      try {
        this.books = await BookService.getByName(this.searchKeyword);
      } catch (error) {
        console.error("Error fetching books:", error);
      }
    },

    openModal(book) {
      if (!localStorage.getItem('MADOCGIA')) {
        alert("Bạn chưa đăng nhập");
      } else {
        this.showModal = true;
        this.selectedBook = book;
      }
    },

    closeModal() {
      this.showModal = false;
      this.selectedBook = '';
      this.note = '';
      this.borrowDate = '';
    },

    async borrowBook() {
      try {
        if (!this.borrowDate) {
          alert("Vui lòng chọn ngày mượn");
          return;
        }
        // Thực hiện logic mượn sách
        this.showAlert("Mượn sách thành công", "success");
        this.closeModal();
      } catch (error) {
        console.error("Lỗi khi mượn sách:", error);
        this.showAlert("Đã xảy ra lỗi, vui lòng thử lại sau", "error");
      }
    },

    async search() {
      try {
        await this.fetchBooks(); 
      } catch (error) {
        console.error("Error searching books:", error);
      }
    },

    resetSearch() {
      this.searchKeyword = '';
      this.fetchBooks(); // Reset và tải lại danh sách sách
    },

    showAlert(message, type) {
      const icon = {
        success: "✅",
        warning: "⚠️",
        error: "❌"
      }[type];

      alert(`${icon} ${message}`);
    }
  }
};
</script>