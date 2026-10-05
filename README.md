BAI 5: CHAN DOAN VA XU LY XUNG DOT CONG MANG (ADDRESS ALREADY IN USE)

1. MUC TIEU
- Thanh thao ky nang chan doan trang thai cac cong mang dang lang nghe (listening ports) tren he thong Linux.
- Su dung cac lenh quan ly tien trinh (ps, kill) de giai phong tai nguyen bi chiem dung.
- Xu ly su co loi thuong gap Address already in use khi deploy ung dung.

2. BOI CANH VA GIA LAP SU CO
- Boi canh: Khi khoi chay ung dung Spring Boot o cong 8082, he thong bao loi xung dot cong ket noi do co mot tien trinh khac dang chay ngam chiem giu cong nay.
- Gia lap cong 8082 bi chiem dung duoi nen:
```bash
python3 -m http.server 8082 > /dev/null 2>&1 &
```

3. QUY TRINH CHAN DOAN VA XAC DINH TIEN TRINH XUNG DOT

3.1. Kiem tra cong mang dang lang nghe
Lenh thuc hien bang ss:
```bash
sudo ss -tlnp | grep 8082
```
Ket qua dau ra:
```text
LISTEN 0 5 0.0.0.0:8082 0.0.0.0:* users:(("python3",pid=14238,fd=3))
```

Lenh thuc hien bang lsof:
```bash
sudo lsof -i :8082
```
Ket qua dau ra:
```text
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python3 14238 root    3u  IPv4  56789      0t0  TCP *:8082 (LISTEN)
```

3.2. Xac dinh thong tin tien trinh
Lenh thuc hien:
```bash
ps -fp 14238
```
Ket qua dau ra:
```text
UID          PID    PPID  C STIME TTY          TIME CMD
root       14238    1200  0 20:45 pts/0    00:00:00 python3 -m http.server 8082
```

4. QUY TRINH XU LY VA GIAI PHONG CONG MANG

4.1. Cham dut tien trinh chiem dung
Lenh thuc hien:
```bash
sudo kill 14238
```

4.2. Kiem tra lai cong mang
Lenh thuc hien:
```bash
sudo ss -tlnp | grep 8082
```
Ket qua: Cong 8082 da duoc giai phong hoan toan.

4.3. Khoi dong lai dich vu Spring Boot
Lenh thuc hien:
```bash
sudo systemctl restart spring-app.service
```

5. KIEM TRA VA XAC NHAN KET QUA

5.1. Kiem tra tien trinh dang giu cong 8082
Lenh thuc hien:
```bash
sudo ss -tlnp | grep 8082
```
Ket qua dau ra:
```text
LISTEN 0 100 0.0.0.0:8082 0.0.0.0:* users:(("java",pid=14590,fd=14))
```

5.2. Kiem tra trang thai dich vu Systemd
Lenh thuc hien:
```bash
sudo systemctl status spring-app.service
```
Ket qua: Dich vu hoat dong o trang thai active (running).

6. TEP TIN DINH KEM
- troubleshoot_report.md: Bao cao chi tiet qua trinh chan doan va chuoi lenh xu ly su co.
