![JOAd](https://github.com/user-attachments/assets/9301b0ba-61af-4d91-80b1-851f1a9890a4)



# Wykorzystanie tabletu Visionect Joan 6 jako panelu sterowania Home Assistant

Joan 6 to 6-calowy wyświetlacz E-Ink firmy Visionect, oryginalnie zaprojektowany jako system rezerwacji sal konferencyjnych. Dzięki swojej energooszczędności, dotykowemu ekranowi i przenośności, doskonale nadaje się również do wykorzystania jako panel do zarządzania encjami w systemie Home Assistant.

![image](https://github.com/user-attachments/assets/054cda40-bb31-4192-9b8d-c88860b5e144)
![image](https://github.com/user-attachments/assets/440e108e-4ffa-497a-893c-9be2b7d67f02)

---

## 🧰 Wymagania wstępne

- Serwer z zainstalowanym **Proxmox VE** i dostępem do internetu.
- Uprawnienia administratora (root) do serwera Proxmox.
- Komputer z systemem Windows, Linux lub macOS (do wstępnej konfiguracji tabletu).
- Tablet **Visionect Joan 6**.

> **Ważna uwaga:** Kontener Docker z oprogramowaniem Visionect Software Suite (serwerem zarządzającym tabletem) musi być stale uruchomiony. Jeśli korzystasz już z Home Assistant na Proxmoxie, nie stanowi to dodatkowego obciążenia. Aczkolwiek można osobno HA na innym urządzeniu.

---

## 📦 Krok 1: Instalacja Dockera w maszynie wirtualnej na Proxmox

1.  Zaloguj się do interfejsu webowego **Proxmox VE**.
2.  Wybierz swój serwer i przejdź do konsoli **Shell**.
3.  Wklej i uruchom poniższy skrypt, aby automatycznie utworzyć maszynę wirtualną z Dockerem:
    ```bash
    bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
    ```
4.  Po zakończeniu skryptu nowa maszyna wirtualna z zainstalowanym Dockerem będzie gotowa do użycia.

---

## ✏️ Krok 2: Utworzenie pliku `docker-compose.yml`

1.  Zaloguj się do nowo utworzonej maszyny wirtualnej (Docker VM) przez konsolę w Proxmoxie (docker login: root).
2.  Utwórz nowy plik `docker-compose.yml` za pomocą edytora `nano`:
    ```bash
    nano docker-compose.yml
    ```
3.  Skopiuj zawartość pliku `docker-compose.yml` z tego repozytorium:  
    👉 **[docker-compose.yml](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/docker-compose.yml)**
4.  Wklej skopiowaną treść do terminala (w oknie konsoli zazwyczaj wystarczy kliknąć prawym przyciskiem myszy).
5.  **Dostosuj obraz do architektury procesora.** Znajdź w pliku linię zaczynającą się od `image:`:
    *   **Dla procesorów x86_64 (Intel/AMD):** Upewnij się, że linia wygląda tak (usuń `-arm` z nazwy):
        ```yaml
        image: visionect/visionect-server-v3:7.6.5
        ```
    *   **Dla procesorów ARM (np. Raspberry Pi):** Pozostaw oryginalną linię:
        ```yaml
        image: visionect/visionect-server-v3:7.6.5-arm
        ```
6.  Zapisz zmiany i zamknij edytor:
    *   `Ctrl + O` → `Enter` (aby zapisać plik)
    *   `Ctrl + X` (aby wyjść z edytora)
7.  Upewnij się, że plik został poprawnie utworzony:
    ```bash
    ls -l docker-compose.yml
    ```
jeśli poprawnie zobaczysz coś podobnego (-rw-r--r-- 1 root root 1079 Jul  4 13:45 docker-compose.yml)

---

## 🚀 Krok 3: Uruchomienie kontenerów Dockera

1.  W terminalu maszyny wirtualnej uruchom kontenery w tle za pomocą polecenia:
    ```bash
    docker compose up -d
    ```
2.  Proces może chwilę potrwać, ponieważ Docker musi pobrać wymagane obrazy: `postgres_db`, `redis` oraz `vserver3`.
3.  Sprawdź status uruchomionych kontenerów:
    ```bash
    docker ps
    ```
    Powinieneś zobaczyć trzy działające kontenery.

---

## 🌐 Krok 4: Dostęp do panelu Visionect Software Suite

1.  W przeglądarce internetowej wpisz adres IP swojej maszyny wirtualnej z Dockerem, dodając port `8081`, np.:
    `http://192.168.1.100:8081`
    
    umnie przykładowo jest 192.168.100.132
![image](https://github.com/user-attachments/assets/aa31b162-4421-4518-96b5-767b486c9af0)

    
2.  Powinieneś zobaczyć panel logowania Visionect Server.

3.  Przy pierwszym uruchomieniu zostaniesz poproszony o ustawienie hasła administratora. W kolejnych krokach będziesz logować się, używając nazwy użytkownika `admin` i swojego nowego hasła.


![image](https://github.com/user-attachments/assets/687a153e-e003-4cc3-8436-df750744ebd2)


---

## 📲 Krok 5: Konfiguracja tabletu Visionect

1.  Pobierz i uruchom aplikację **Visionect Configurator** na swoim komputerze:
    *   **Windows:** [VisionectConfigurator.exe](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator.exe)
    *   **Linux:** [VisionectConfigurator_linux.deb](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_linux.deb)
    *   **macOS (Apple Silicon):** [VisionectConfigurator_m1.dmg](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_m1.dmg)
    *   **macOS (Intel):** [VisionectConfigurator_intel.dmg](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator_intel.dmg)
2.  Podłącz tablet do komputera za pomocą kabla USB.
3.  Po wykryciu tabletu przez aplikację:
    *   Wybierz swoją sieć Wi-Fi i wpisz hasło.
    *   Przejdź do zakładki **Advanced Connectivity**.
    *   Wprowadź dane swojego serwera:
        *   **Server IP**: Adres IP Twojej maszyny wirtualnej z Dockerem (np. `192.168.1.100`).
        *   **Port**: `11113`
        *   
    ![image](https://github.com/user-attachments/assets/de30fd1e-9bd3-4f98-ab00-9a3b534f7332)

4.  Kliknij przycisk, aby połączyć tablet z serwerem.
5.  Po chwili tablet powinien pojawić się w panelu **Visionect Software Suite** na liście urządzeń.
    ![image](https://github.com/user-attachments/assets/37a58b07-d292-41dd-bd2d-8c0b84c9ad6b)

---

## ✏️ Krok 6: Tworzenie dashboardu w Home Assistant

Aby wyświetlić interfejs Home Assistant na tablecie, użyjemy dodatku **AppDaemon**.

1.  Zainstaluj dodatek **AppDaemon** w Home Assistant (jeśli jeszcze go nie masz).
2.  Przejdź do katalogu konfiguracyjnego AppDaemon, np. przez dodatek "Samba share" lub "File editor":  
    `\config\appdaemon\dashboards\` ( umnie to wygląda tak \\adres HA\addon_configs\a7c7b154_appdaemon\dashboards\ )
3.  Utwórz w tym katalogu nowy plik z rozszerzeniem `.dash`, np. `joanl.dash`.
4.  Możesz skorzystać z gotowych szablonów dashboardów z tego repozytorium:
    *   [joan1.dash](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/joan1.dash)
    *   [joan2.dash](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant/blob/main/joan2.dash)
5.  **Ważne:** Zmodyfikuj plik `joanl.dash lub joan2.dash`, podmieniając przykładowe encje na własne encje z Home Assistant. Szczegółową dokumentację tworzenia dashboardów znajdziesz na [oficjalnej stronie AppDaemon](https://appdaemon.readthedocs.io/en/latest/DASHBOARD_CREATION.html).
6.  Zrestartuj dodatek AppDaemon, aby załadować nową konfigurację.
7.  Sprawdź, czy Twój dashboard działa, otwierając w przeglądarce adres:  
    `http://<adres_ip_ha>:5050/joanl`  
    (zmień `joanl` na nazwę swojego pliku `.dash` jeśli nie chcesz używać przykładowych polików).
8.  Skopiuj ten adres URL.
9.  Wróć do panelu **Visionect Software Suite**, przejdź do ustawień swojego tabletu i w polu **Default URL** wklej skopiowany adres dashboardu. Zapisz zmiany.
    ![image](https://github.com/user-attachments/assets/00558b5d-ad93-44ab-b4f0-ae8e9b1be20f)
10. Po chwili na ekranie tabletu powinien pojawić się Twój dashboard z Home Assistant.

### Dodatkowe porady
*   Dla każdego tabletu możesz utworzyć osobny plik `.dash` i przypisać mu unikalny adres URL.
*   W panelu Visionect warto również dostosować **częstotliwość odświeżania** (`Refresh rate`), aby zbalansować aktualność danych i zużycie baterii.
    ![image](https://github.com/user-attachments/assets/9f0c1741-76f3-496d-ad44-e316d29621f1)

---

## ⭐ Integracja z Home Assistant (Odczyt stanu tabletu)

Aby odczytywać w Home Assistant informacje o stanie tabletu (np. poziom naładowania baterii, status połączenia itp), możesz skorzystać z niestandardowej integracji HACS dostępnej w moim drugim repozytorium  [Visionect Joan](https://github.com/Adam7411/visionect_joan).

Pozwoli to na tworzenie automatyzacji, np. wysyłania powiadomienia o niskim stanie baterii albo dodanie poziomu baterii na tablecie;)
![image](https://github.com/user-attachments/assets/e7cfc034-8895-49d3-852c-426cfd9e3811)


