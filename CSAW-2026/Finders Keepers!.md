## 1. Mô tả đề bài
I wanna steal everything, but I don't know where it is.... maybe this video can help?


https://github.com/user-attachments/assets/b0cedd6f-0808-41f8-a167-73d7383811b7


---

## 2. Phân tích và lời giải  
Phát video, ta thấy một dòng chữ xuất hiện ở giây thứ 4: 
`Y2FudGZpbmRpdA==`.
Nhận thấy đây là text đã được mã hóa bằng BASE64.  
Giải mã ta thu được text:
`cantfindit`  
Không tìm được thông tin nào khác từ video.
Ta nghĩ tới việc điều tra datameta của file mp4.
Mở cmd và chạy lệnh
```
exiftool C:\Users\Admin\Downloads\finderskeepers.mp4
```
Màn hình hiển thị  
<img width="512" height="332" alt="image" src="https://github.com/user-attachments/assets/41bd9967-e2b1-4755-8047-b1bfd4e67ae0" />  
Ta tìm thấy 1 đoạn mã hóa BASE64 ` ZXNucHtkMGNfQHl6QGdsX21uMGpfcG0zejNfZzBfbzAwc30=`.  
Giải mã ta được text `esnp{d0c_@yz@gl_mn0j_pm3z3_g0_o00s}`  
Cần đưa về dạng đúng của flag `csaw{}`. Ta giải mã Vigenere với `key` là `csaw` và input `esnp`.  
Ta được một phần của key đúng là `cant`. Kết hợp với dòng chữ `cantfindit` tìm được trên video ta thử giải mã Vigenere với key `cantfindit` và input `esnp{d0c_@yz@gl_mn0j_pm3z3_g0_o00s}` trên CyberChef ta thu được: `csaw{y0u_@lw@ys_kn0w_wh3r3_t0_l00k}` 
```
csaw{y0u_@lw@ys_kn0w_wh3r3_t0_l00k}
```








----
