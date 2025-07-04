# 🖥️ Visionect Server 3 na Proxmox z Docker 

Ten projekt umożliwia szybkie uruchomienie **Visionect Server 3** w środowisku **Docker**, działającym na maszynie wirtualnej w **Proxmox VE**.

---

## 🧰 Wymagania wstępne

- Zainstalowany **Proxmox VE** z dostępem do internetu
- Uprawnienia administratora (root) do serwera
- Komputer z systemem Windows (do konfiguracji tabletu)
- Tablet Visionect Joan 6

---

## 📦 Krok 1: Instalacja Dockera w maszynie wirtualnej Proxmox

1. Zaloguj się do **Proxmox Web UI**
2. Przejdź do **Shell** serwera głównego 
3. Wklej poniższy skrypt i uruchom:

   ```bash
   bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
   ```

4. Skrypt utworzy maszynę wirtualną i zainstaluje Dockera

---

## ✏️ Krok 2: Utworzenie pliku `docker-compose.yml`

1. Zaloguj się do nowo utworzonej maszyny wirtualnej z Dockerem (z Proxmoxa)

2. Otwórz terminal (Shell) tej maszyny

3. Wpisz komendę, aby utworzyć plik:

   ```bash
   nano docker-compose.yml
   ```

4. Skopiuj zawartość z pliku :  

    👉 docker-compose.yml

6. Wklej skopiowaną treść do terminala (prawym przyciskiem myszy > Wklej)

7. Znajdź tę linię w pliku:

jeśli procesor x86_64	Intel/AMD	✅ usuń -arm z image
   ```yaml
   image: visionect/visionect-server-v3:7.6.5-arm
   ```

   i zamień ją na:

   ```yaml
   image: visionect/visionect-server-v3:7.6.5
   ```
jeśli procesor aarch64	ARM (np. RPi)	🔁 zostaw -arm w image

7. Zapisz plik:

   - `Ctrl + O` → zapisz  
   - `Enter` → potwierdź nazwę  
   - `Ctrl + X` → wyjdź

8. Sprawdź, czy plik istnieje:

   ```bash
   ls -l docker-compose.yml
   ```

   Oczekiwany wynik:

   ```bash
   -rw-r--r-- 1 root root 1234 Jul 4 12:34 docker-compose.yml
   ```

---

## 🚀 Krok 3: Uruchomienie kontenerów Dockera

1. W terminalu uruchom kontenery w tle:

   ```bash
   docker compose up -d
   ```

2. Instalacja może chwilę potrwać – Docker pobierze wymagane obrazy:
   - `postgres_db`
   - `redis`
   - `vserver3`

3. Sprawdź działające kontenery:

   ```bash
   docker ps
   ```

---

## 🌐 Krok 4: Wejście do panelu Visionect

1. W przeglądarce wpisz IP maszyny Docker z portem `8081`, np.:

   ```
   http://192.168.1.100:8081
   ```

2. Powinieneś zobaczyć panel logowania Visionect Server

3. Wpisz swoje hasło ( póżniej loging:admin + swoje hasło )

---

## 📲 Krok 5: Konfiguracja tabletu Visionect

1. Pobierz i uruchom aplikację konfiguracyjną:

   For Windows: 👉 https://files.visionect.com/VisionectConfigurator/VisionectConfigurator.exe 

   For Linux: 👉 https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_linux.deb
   For MacOS: 👉 https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_m1.dmg 👉 https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_intel.dmg

3. Podłącz tablet do komputera przez USB

4. Po wykryciu tabletu:
   - Wybierz swoją sieć Wi-Fi i wpisz hasło
   - Przejdź do **Advanced Connectivity**
   - Wprowadź:
     - **Server IP**: `192.168.1.100` (adres IP Dockera)
     - **Port**: `11113`

5. Połącz się z serwerem

6. Tablet powinien być widoczny w panelu Visionect Software Suite

---

## 🧠 Co dalej?


- Zainstaluj **AppDaemon** w Home Assistant i stwórz swój dashboard



---

## 📌 Autor

Instrukcja przygotowana jako szybki start do uruchomienia Visionect Server 3 na Proxmox z użyciem Dockera.  
Możesz swobodnie ją edytować, rozszerzać i integrować z własnym środowiskiem automatyki domowej.
