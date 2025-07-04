Joan 6 wyświetlacz E-Ink firmy Visionect służy jako system rezerwacji sal ale nada się jako energooszczędny przenośny dotykowy tablet do zarządzana encjami z Home Assistant.


![image](https://github.com/user-attachments/assets/054cda40-bb31-4192-9b8d-c88860b5e144)

![image](https://github.com/user-attachments/assets/440e108e-4ffa-497a-893c-9be2b7d67f02)


---

## 🧰 Wymagania wstępne

- Zainstalowany **Proxmox VE** z dostępem do internetu
- Uprawnienia administratora (root) do serwera
- Komputer z systemem Windows (do konfiguracji tabletu)
- Tablet Visionect Joan 6
- 
P.S Docker z panelem sterowania Visionect Software Suite (muzgiem tabletu) cały czas mają być włączone na Proxmox ale to nieproblem jeśli ktoś ma HA na Proxmoxie (oczywiście można też osobno)
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

1. Zaloguj się do nowo utworzonej maszyny wirtualnej Docker VM (z Proxmoxa)

2. W Dockerze VM otwórz terminal (Shell) 

3. Wpisz komendę, aby utworzyć plik:

   ```bash
   nano docker-compose.yml
   ```

4. Skopiuj zawartość z pliku docker-compose.yml:  

    👉 https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/docker-compose.yml

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
________________________________________________

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

   For MacOS: 👉 https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_m1.dmg
   👉 https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_intel.dmg

3. Podłącz tablet do komputera przez USB

4. Po wykryciu tabletu:
   - Wybierz swoją sieć Wi-Fi i wpisz hasło
   - Przejdź do **Advanced Connectivity**
   - Wprowadź:
     - **Server IP**: `192.168.1.100` (adres IP Dockera)
     - **Port**: `11113`

![image](https://github.com/user-attachments/assets/de30fd1e-9bd3-4f98-ab00-9a3b534f7332)


5. Połącz się z serwerem

6. Tablet powinien być widoczny w panelu Visionect Software Suite

![image](https://github.com/user-attachments/assets/37a58b07-d292-41dd-bd2d-8c0b84c9ad6b)

---



## ✏️ Krok 6: Zainstaluj **AppDaemon** w Home Assistant aby tworzyć dashboard

 

Po instalacji AppDaemon w katalogu dashboards (\\adres lokalny HA\addon_configs\a7d7b994_appdaemon\dashboards\) tworzymy plik dashboard .dash 

(ja mam joan1.dash i joan2.dash) gotowe pliki 👉 https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/joan1.dash i 👉 https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/joan2.dash 
w plikach podmienić na swoje encje z HA lub jak kto woli zapraszam na strone 👉 https://appdaemon.readthedocs.io/en/latest/DASHBOARD_CREATION.html

Gdy mamy gotowy plik z swoim dashboards uruchamiamy ponownie AppDaemon i sprawdzamy czy dashboards działa w przeglądarce wpisujemy http://adres HA:5050/joan1 

Jeśli wyświetli dashboards adres wpisujemy w panelu sterowania Visionect Software Suite jak na zdjęciu i zapisujemy.
![image](https://github.com/user-attachments/assets/00558b5d-ad93-44ab-b4f0-ae8e9b1be20f)

Na wszystkich tabletach wyświetli się wasz dashboards

P.S można każdy kolejny stworzony dashboards przypisać do następnego tabletu Joan 6 warto tam ustawiś czas odświerzania
![image](https://github.com/user-attachments/assets/9f0c1741-76f3-496d-ad44-e316d29621f1)

W innym moim repozytorium niestandardowy dodatek HASC który odczytuje podstawowe informacje z tabletu Jona 6 do Home Assistant (poziom beteri, stan itp) potrzebne do stworzenia automatyzacji głuwnie chodzi o powiadomieniu stanu bateri


---

