<template>
    <div class="container">
      <h1 class="mt-5 text-center fw-bold fs-2">THÔNG TIN ĐỘC GIẢ</h1>
      <div class="row">
        <div class="col-md-2 card bg-white" id="reader-link">
          <div class="navbar-vertical">
            <router-link to="/readerInformation" class="nav-link mb-4 text-primary"><i class="fa-solid fa-user px-2"></i>Thông tin độc giả</router-link>
            <router-link to="/readerInformationEdit" class="nav-link mb-4"><i class="fa-solid fa-pen px-2"></i>Thay đổi thông tin</router-link>
            <router-link to="/borrowBookHistory" class="nav-link mb-4"><i class="fa-solid fa-user-pen px-2"></i>Lịch sử mượn sách</router-link>
            <a href="#" class="nav-link mb-4" @click="changePassword"><i class="fa-solid fa-key px-2"></i>Đổi mật khẩu</a>
          </div>
        </div>
        <div class="col-md-1"></div>
        <div class="col card bg-white" id="reader-info">
          <div class="row">
            <div class="col-md-8">
              <div v-if="reader">
                <p class="pb-1"><strong>Mã độc giả:</strong> {{ reader[0].MADOCGIA }}</p>
                <p class="pb-1"><strong>Tên tài khoản:</strong> {{ reader[0].USERNAME }}</p>
                <p class="pb-1"><strong>Họ và tên:</strong> {{ reader[0].HOLOT }} {{ reader[0].TEN }}</p>
                <p class="pb-1"><strong>Ngày sinh:</strong> {{ reader[0].NGAYSINH }}</p>
                <p class="pb-1"><strong>Giới tính:</strong> {{ reader[0].PHAI }}</p>
                <p class="pb-1"><strong>Địa chỉ:</strong> {{ reader[0].DIACHI }}</p>
                <p class="pb-1"><strong>Điện thoại:</strong> {{ reader[0].DIENTHOAI }}</p>
            </div>
              <div v-else>
                <p>Không tìm thấy thông tin độc giả.</p>
              </div>
            </div>
            <div class="col-md-4">
              <img src="https://inkythuatso.com/uploads/thumbnails/800/2023/03/6-anh-dai-dien-trang-inkythuatso-03-15-26-36.jpg" alt="" style="border-radius: 300px;">       
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import ReaderService from "@/services/reader.service";
  
  export default {
    data() {
      return {
        reader: null
      };
    },
    async created() {
      try {
        const MADOCGIA = localStorage.getItem("MADOCGIA");
        if (!MADOCGIA) {
          console.error("Không tìm thấy mã độc giả");
          return;
        }
        this.reader = await ReaderService.getByMADOCGIA(MADOCGIA);
      } catch (error) {
        console.error("Xảy ra lỗi khi truy xuất thông tin độc giả:", error);
      }
    },
    methods: {
    changePassword() {
      // Hiển thị thông báo khi nhấp vào liên kết "Đổi mật khẩu"
      window.alert("Vui lòng liên hệ trung tâm hỗ trợ để đổi mật khẩu.");
    }
  }

  };
  </script>
  
  
  