<template>
  <main> 
    <div class="container">  
      <div id="main-page">
        <div class="banner">
          <img src="@/assets/images/bannerlibrary.png" alt="" class="img-fluid" width="40%">
        </div>
        <div class="row">
          <div class="col-md-6" v-for="book in books" :key="book._id">
            <div class="card mb-4 bg-white">
              <div class="row g-0">
                <!-- Column for Book Image -->
                <div class="col-md-4">
                  <router-link :to="{ name: 'book.detail', params: { id: book._id } }" class="text-black">
                    <img :src="book.HINHANH" class="card-img-top img-fluid" alt="Book Image">
                  </router-link>
                </div>
                <!-- Column for Book Details -->
                <div class="col-md-8">
                  <div class="card-body">
                    <h5 class="card-title text-truncate">{{ book.TENSACH }}</h5>
                    <p class="card-text">Tác giả: {{ book.TACGIA }}</p>
                    <p class="card-text">Thể loại: {{ book.THELOAI }}</p>
                    <div class="borrow-button">
                      <button class="btn btn-primary" @click="openModal(book)">Mượn</button>
                    </div>
                  </div>
                </div>
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
        this.books = await BookService.getAll();
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
          this.showAlert("Vui lòng chọn ngày mượn", "warning");
          return;
        }
        if (this.selectedBook.SOQUYEN <= 0) {
          this.showAlert("Sách không còn trống để mượn", "warning");
          return;
        }
        const existingRecord = await BorrowBookService.getByMADOCGIA(localStorage.getItem('MADOCGIA'));
        const bookRecord = existingRecord.find(record => record.MASACH === this.selectedBook.MASACH);
        if (bookRecord) {
          this.showAlert("Bạn đã mượn sách này rồi", "warning");
          return;
        }
        const response = await BorrowBookService.create({
          MADOCGIA: localStorage.getItem('MADOCGIA'),
          MASACH: this.selectedBook.MASACH,
          GHICHU: this.note,
          NGAYMUON: this.borrowDate
        });
        await BookService.update(this.selectedBook._id, { SOQUYEN: this.selectedBook.SOQUYEN - 1 });
        this.showAlert("Mượn sách thành công", "success");
        this.closeModal();
      } catch (error) {
        console.error("Lỗi khi mượn sách:", error);
        this.showAlert("Đã xảy ra lỗi, vui lòng thử lại sau", "error");
      }
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
