BAO CAO CHAN DOAN VA XU LY XUNG DOT CONG MANG (PORT CONFLICT TROUBLESHOOT REPORT)

1. THONG TIN SU CO
- Su co: Address already in use khi khoi chay ung dung Spring Boot tren cong 8082.
- Nguyen nhan: Cong 8082 dang bi chiem dung boi mot tien trinh khac chay ngam truoc do.

2. GIA LAP SU CO CHIEM DUNG CONG
Lenh khoi chay tien trinh gia lap chay ngam:
```bash
python3 -m http.server 8082 > /dev/null 2>&1 &
```

3. QUY TRINH CHAN DOAN VA XAC DINH TIEN TRINH XUNG DOT

3.1. Kiem tra cong mang dang lang nghe (Listening Port)
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

Thong tin tien trinh thu thap duoc:
- Process Name: python3
- Process ID (PID): 14238
- Port bi chiem: 8082/tcp
- User chay tien trinh: root

3.2. Kiem tra chi tiet tien trinh qua PID
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

4.2. Xac nhan cong da duoc giai phong
Lenh thuc hien:
```bash
sudo ss -tlnp | grep 8082
```
Ket qua: Khong con tien trinh python3 lang nghe tren cong 8082.

4.3. Khoi dong lai dich vu Spring Boot Systemd
Lenh thuc hien:
```bash
sudo systemctl restart spring-app.service
```

5. KIEM TRA VA XAC NHAN HE THONG HOAT DONG BINH THUONG

5.1. Kiem tra tien trinh nam giu cong 8082 sau khi fix
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
Ket qua dau ra:
```text
* spring-app.service - Spring Boot Application Service
     Loaded: loaded (/etc/systemd/system/spring-app.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2026-10-05 20:46:00 UTC; 10s ago
   Main PID: 14590 (java)
      Tasks: 28 (limit: 2260)
     Memory: 210.2M
     CGroup: /system.slice/spring-app.service
             `-14590 /usr/bin/java -jar /opt/spring-app/app.jar
```

6. TONG KET CHUOI LENH CHAN DOAN VA XU LY
1. sudo ss -tlnp | grep 8082 : Tim PID tien trinh chiem cong.
2. ps -fp <PID> : Xac dinh lenh va chu so huu cua tien trinh.
3. sudo kill <PID> : Giai phong tai nguyen cong mang.
4. sudo systemctl restart spring-app.service : Khoi dong lai dich vu bi loi.
5. sudo ss -tlnp | grep 8082 : Xac nhan cong da chuyen sang cho tien trinh hop le (java).
