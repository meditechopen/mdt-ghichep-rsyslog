## Một vài ví dụ về Logrotate

### Lý thuyết

- Logrotate làm được những gì?
	- Xoay vòng tập tin log khi đến dung lượng được thiết lập
	- Ghi tiếp thông tin log ra file mới khi đã xoay vòng
	- Có thể nén file đã xoay vòng
	- Tùy chọn kiểu nén file
	- Thêm thông tin ngày tháng vào tên của mỗi file log khi xoay vòng
	- Thực thi một shell script khi xoay vòng log
	- Xóa các file log xoay vòng cũ

### Một vài ví dụ

#### 1. File cấu hình của Logrotate

- Chương trình logrotate:

```
/usr/sbin/logrotate
```

- Script thực thi chạy logrotate hàng ngày

```
/etc/cron.daily/logrotate
```

- File cấu hình của logrotate

```
/etc/logrotate.conf 
```

- Thư mục lưu trữ file cấu hình cho các ứng dụng của Logrotate

```
/etc/logrotate.d 
```

#### 2. Xoay vòng khi file log đạt đến dung lượng xác định

Ví dụ, bạn có một file `/tmp/output.log` và muốn nó xoay vòng khi nó đạt đến dung lượng 1KB:

```
/tmp/output.log {
        size 1k
        create 700 bala bala
        rotate 4
}
```

- **Chú thích**:
	- `size 1k`: Dung lượng của file (Lớn hơn hoặc bằng). Khi file đạt đến mức, nó sẽ sinh ra một file mới.
	- `create 700 bala bala`: File mới được sinh ra với quyền &00 và thuộc sở hữu của user và nhóm `bala`
	- `rotate 4`: Số lượng file xoay vòng là 4.
	
#### 3. Xoay vòng file khi đến một dung lượng và viết tiếp vào file log cũ.

Tùy chọn `copytruncate`, giúp chúng ta làm việc này.

```
/tmp/output.log {
         size 1k
         copytruncate
         rotate 4
}
```

#### 4. Nén file log xoay vòng.

Mặc định, file log sẽ được nén bằng `gzip`.

```
/tmp/output.log {
        size 1k
        copytruncate
        create 700 bala bala
        rotate 4
        compress
}
```

Ở ví dụ này, chúng ta sẽ nén file log đã xoay vòng và file log mới tạo ra thuộc sở hữu của user và group có tên là `bala` với t đa là 4 file.

##### 5. Thêm ngày tháng vào tên file log đã xoay vòng

```
/tmp/output.log {
        size 1k
        copytruncate
        create 700 bala bala
        dateext
        rotate 4
        compress
}
```

Cùng với mục đích ở trên VD4, file log sẽ được nén và tên file sẽ được thêm ngày tháng với tùy chọn `dateext`.

```
$ ls -lrt /tmp/output*
-rw-r--r--  1 bala bala 8980 2010-06-09 22:10 output.log-20100609.gz
-rwxrwxrwx 1 bala bala     0 2010-06-09 22:11 output.log
``` 

#### 6. Tùy chọn xoay vòng theo ngày/tuần/tháng

Chúng ta có thể cài đặt được log xoay vòng định kỳ theo ngày/tuần/tháng/năm

```
/tmp/output.log {
        monthly
        copytruncate
        rotate 4
        compress
}
```

- Chú ý:
	- Chúng ta thay thể `monthly` bằng các tùy chọn:
		- hourly: theo giờ
		- daily: theo ngày
		- weekly: theo tuần/tháng
		- monthly: theo tháng
		- yearly: theo năm

#### 7. Thực thi một script sau khi xoay vòng log

```
/tmp/output.log {
        size 1k
        copytruncate
        rotate 4
        compress
        postrotate
               /home/bala/myscript.sh
        endscript
}
```

Cho phép bạn chạy một script có tên là `myscript.sh` sau khi rotate thực hiện xong.

#### 8. Lưu trữ file xoay vòng trong khoảng thời gian

```
/tmp/output.log {
        size 1k
        copytruncate
        rotate 4
        compress
        maxage 100
}
```

Tùy chọn `maxage` cho phép bạn cấu hình thời gian lưu trữ file log. Ở ví dụ là 100 ngày.

#### 9. Tùy chọn `missingok`

Tùy chọ này cho phép bỏ qua các file log thực hiện xoay vòng khi nó không tồn tại.

```
/tmp/output.log {
        size 1k
        copytruncate
        rotate 4
        compress
        missingok
}
```

#### 10. Tùy chọn kiểu nén file log

Mặc định, Sử dụng GZIP để nén file. Chúng ta có thể thay thế bằng trình nén khác với tùy chọn:
- `compress`: Thực thi việc nén file
- `compresscmd`: Câu lệnh thực thi việc nén file. (Mặc định: GZIP)
- `compressext`: Xác định phần mở rộng của file nén. Trên ví dụ: bz2

```
/tmp/output.log {
        size 1k
        copytruncate
        create
        compress
        compresscmd /bin/bzip2
        compressext .bz2
        rotate 4
}
```

### Tham khảo:

- http://www.thegeekstuff.com/2010/07/logrotate-examples