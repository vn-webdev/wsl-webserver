# WSL (Windows Subsystem for Linux) 
WSL là một tính năng có trên Windows x64 (từ Windows 10, bản 1607 và trên Windows Server 2019), nó cho phép chạy hệ điều hành Linux (GNU/Linux) trên Windows. 
Với WSL bạn có thể chạy các lệnh, các ứng dụng trực tiếp từ dòng lệnh Windows mà không phải bận tâm về việc tạo / quản lý máy ảo như trước đây. 

# Kiểm tra bật công nghệ ảo hóa (Virtualization Technology)
Công nghệ ảo hóa được cả 2 loại CPU của AMD và Intel hỗ trợ. AMD gọi công nghệ ảo hóa của họ là AMD-V, Intel thì gọi là VT-x hoặc Intel VT. 
Hầu hết thì các CPU hiện nay đều đã được tích hợp công nghệ ảo hóa, ngoại trừ các loại CPU đời cũ.

Kiểm tra Virtualization => Vào Task Manager > Tab Performance. Các bạn sẽ thấy dòng Virtualization
Nếu nó hiển thị Disabled, thì bạn phải vào BIOS bật công nghệ này lên. 
Nếu bạn không thấy dòng ảo hóa này, tức là dòng CPU không hỗ trợ hoặc dòng CPU đã cũ.

Bật Virtualization => vào cài đặt BIOS, thông thường thì như bên dưới, nếu khác thì search cách bật dòng main của máy
-CPU AMD
Overclocking => Advanced CPU configuration => svm mode => chọn Enable
-CPU Intel
Advanced => CPU Configuration => Intel(R) Virtualization Technology => chọn Enable

# Cài đặt WSL
Mở PowerShell hoặc Command Prompt quyền Administrator
Chạy => dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
