<template>
  <div class="container">
    <p class="mt-5 text-center mb-5 fw-bold fs-1">LỊCH SỬ MƯỢN SÁCH</p>
    <div v-if="borrowedBooks.length === 0" class="mt-3 text-center">Không có sách được mượn.</div>
    <div v-else class="mt-3">
      <table class="table table-bordered text-center mb-5">
        <thead>
          <tr>
            <th scope="col">Tên sách</th>
            <th scope="col">Ghi chú</th>
            <th scope="col">Ngày mượn</th>
            <th scope="col">Ngày trả</th>
            <th scope="col">Trạng thái</th>
            <th scope="col">Thao tác</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="book in borrowedBooks" :key="book.MASACH">
            <td>{{ book.TENSACH }}</td>
            <td>{{ book.GHICHU }}</td>
            <td>{{ book.NGAYMUON }}</td>
            <td>{{ book.NGAYTRA }}</td>
            <td v-if="book.NGAYTRA">Đã trả <i class="fa-solid fa-check px-1 text-success"></i></td>
            <td v-else>Chưa trả <i class="fa-solid fa-close px-1 text-danger"></i></td>
            <td>
              <!-- Thêm sự kiện click để trả sách -->
              <button @click="returnBook(book)" class="btn btn-primary">Trả sách</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import BorrowBookService from '@/services/borrowBook.service';
import BookService from '@/services/book.service';

export default {
  data() {
    return {
      borrowedBooks: [],
    };
  },
  methods: {
    async fetchBorrowedBooks() {
      try {
        const MADOCGIA = localStorage.getItem('MADOCGIA');
        if (!MADOCGIA) {
          console.error('Không tìm thấy thông tin độc giả');
          return;
        }

        const borrowedBooks = await BorrowBookService.getByMADOCGIA(MADOCGIA);
        for (let book of borrowedBooks) {
          const bookDetails = await BookService.getByMASACH(book.MASACH);
          book.TENSACH = bookDetails[0].TENSACH;
        }

        this.borrowedBooks = borrowedBooks;
      } catch (error) {
        console.error('Lỗi khi lấy thông tin mượn sách:', error);
      }
    },
    async returnBook(book) {
      try {
        // Gọi API hoặc service để đánh dấu sách đã trả
        // Ví dụ:
        // await BorrowBookService.returnBook(book.ID); // Gọi service để đánh dấu sách đã trả

        // Sau khi trả thành công, cập nhật lại trạng thái của sách
        book.NGAYTRA = new Date().toISOString(); // Cập nhật ngày trả hiện tại
        console.log('Trả sách thành công. Ngày trả:', book.NGAYTRA);
      } catch (error) {
        console.error('Lỗi khi trả sách:', error);
      }
    }
  },
  mounted() {
    this.fetchBorrowedBooks();
  },
};
</script>
