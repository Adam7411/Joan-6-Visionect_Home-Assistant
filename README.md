Wykorzystanie tabletu Visionect Joan 6 jako panelu sterowania Home Assistant
Joan 6 to 6-calowy wyświetlacz E-Ink firmy Visionect, oryginalnie zaprojektowany jako system rezerwacji sal konferencyjnych. Dzięki swojej energooszczędności, dotykowemu ekranowi i przenośności, doskonale nadaje się również do wykorzystania jako panel do zarządzania encjami w systemie Home Assistant.
![alt text](https://github.com/user-attachments/assets/054cda40-bb31-4192-9b8d-c88860b5e144)

![alt text](https://github.com/user-attachments/assets/440e108e-4ffa-497a-893c-9be2b7d67f02)
🧰 Wymagania wstępne
Serwer z zainstalowanym Proxmox VE i dostępem do internetu.
Uprawnienia administratora (root) do serwera Proxmox.
Komputer z systemem Windows, Linux lub macOS (do wstępnej konfiguracji tabletu).
Tablet Visionect Joan 6.
Ważna uwaga: Kontener Docker z oprogramowaniem Visionect Software Suite (serwerem zarządzającym tabletem) musi być stale uruchomiony. Jeśli korzystasz już z Home Assistant na Proxmoxie, nie stanowi to dodatkowego obciążenia. Serwer można również zainstalować na oddzielnej maszynie.
📦 Krok 1: Instalacja Dockera w maszynie wirtualnej na Proxmox
Zaloguj się do interfejsu webowego Proxmox VE.
Wybierz swój serwer i przejdź do konsoli Shell.
Wklej i uruchom poniższy skrypt, aby automatycznie utworzyć maszynę wirtualną z Dockerem:
Generated bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
Use code with caution.
Bash
Po zakończeniu skryptu nowa maszyna wirtualna z zainstalowanym Dockerem będzie gotowa do użycia.
✏️ Krok 2: Utworzenie pliku docker-compose.yml
Zaloguj się do nowo utworzonej maszyny wirtualnej (Docker VM) przez konsolę w Proxmoxie.
Utwórz nowy plik docker-compose.yml za pomocą edytora nano:
Generated bash
nano docker-compose.yml
Use code with caution.
Bash
Skopiuj zawartość pliku docker-compose.yml z tego repozytorium:
👉 docker-compose.yml
Wklej skopiowaną treść do terminala (w oknie konsoli zazwyczaj wystarczy kliknąć prawym przyciskiem myszy).
Dostosuj obraz do architektury procesora. Znajdź w pliku linię zaczynającą się od image::
Dla procesorów x86_64 (Intel/AMD): Upewnij się, że linia wygląda tak (usuń -arm z nazwy):
Generated yaml
image: visionect/visionect-server-v3:7.6.5
Use code with caution.
Yaml
Dla procesorów ARM (np. Raspberry Pi): Pozostaw oryginalną linię:
Generated yaml
image: visionect/visionect-server-v3:7.6.5-arm
Use code with caution.
Yaml
Zapisz zmiany i zamknij edytor:
Ctrl + O → Enter (aby zapisać plik)
Ctrl + X (aby wyjść z edytora)
Upewnij się, że plik został poprawnie utworzony:
Generated bash
ls -l docker-compose.yml
Use code with caution.
Bash
🚀 Krok 3: Uruchomienie kontenerów Dockera
W terminalu maszyny wirtualnej uruchom kontenery w tle za pomocą polecenia:
Generated bash
docker compose up -d
Use code with caution.
Bash
Proces może chwilę potrwać, ponieważ Docker musi pobrać wymagane obrazy: postgres_db, redis oraz vserver3.
Sprawdź status uruchomionych kontenerów:
Generated bash
docker ps
Use code with caution.
Bash
Powinieneś zobaczyć trzy działające kontenery.
🌐 Krok 4: Dostęp do panelu Visionect Software Suite
W przeglądarce internetowej wpisz adres IP swojej maszyny wirtualnej z Dockerem, dodając port 8081, np.:
http://192.168.1.100:8081
Powinieneś zobaczyć panel logowania Visionect Server.
Przy pierwszym uruchomieniu zostaniesz poproszony o ustawienie hasła administratora. W kolejnych krokach będziesz logować się, używając nazwy użytkownika admin i swojego nowego hasła.
📲 Krok 5: Konfiguracja tabletu Visionect
Pobierz i uruchom aplikację Visionect Configurator na swoim komputerze:
Windows: VisionectConfigurator.exe
Linux: VisionectConfigurator_linux.deb
macOS (Apple Silicon): VisionectConfigurator_m1.dmg
macOS (Intel): VisionectConfigurator_intel.dmg
Podłącz tablet do komputera za pomocą kabla USB.
Po wykryciu tabletu przez aplikację:
Wybierz swoją sieć Wi-Fi i wpisz hasło.
Przejdź do zakładki Advanced Connectivity.
Wprowadź dane swojego serwera:
Server IP: Adres IP Twojej maszyny wirtualnej z Dockerem (np. 192.168.1.100).
Port: 11113
![alt text](https://github.com/user-attachments/assets/de30fd1e-9bd3-4f98-ab00-9a3b534f7332)
Kliknij przycisk, aby połączyć tablet z serwerem.
Po chwili tablet powinien pojawić się w panelu Visionect Software Suite na liście urządzeń.
![alt text](https://github.com/user-attachments/assets/37a58b07-d292-41dd-bd2d-8c0b84c9ad6b)
✏️ Krok 6: Tworzenie dashboardu w Home Assistant
Aby wyświetlić interfejs Home Assistant na tablecie, użyjemy dodatku AppDaemon.
Zainstaluj dodatek AppDaemon w Home Assistant (jeśli jeszcze go nie masz).
Przejdź do katalogu konfiguracyjnego AppDaemon, np. przez dodatek "Samba share" lub "File editor":
\config\appdaemon\dashboards\
Utwórz w tym katalogu nowy plik z rozszerzeniem .dash, np. joan_panel.dash.
Możesz skorzystać z gotowych szablonów dashboardów z tego repozytorium:
joan1.dash
joan2.dash
Ważne: Zmodyfikuj plik .dash, podmieniając przykładowe encje na własne encje z Home Assistant. Szczegółową dokumentację tworzenia dashboardów znajdziesz na oficjalnej stronie AppDaemon.
Zrestartuj dodatek AppDaemon, aby załadować nową konfigurację.
Sprawdź, czy Twój dashboard działa, otwierając w przeglądarce adres:
http://<adres_ip_ha>:5050/joan_panel
(zmień joan_panel na nazwę swojego pliku .dash).
Skopiuj ten adres URL.
Wróć do panelu Visionect Software Suite, przejdź do ustawień swojego tabletu i w polu URL wklej skopiowany adres dashboardu. Zapisz zmiany.
![alt text](https://github.com/user-attachments/assets/00558b5d-ad93-44ab-b4f0-ae8e9b1be20f)
Po chwili na ekranie tabletu powinien pojawić się Twój dashboard z Home Assistant.
Dodatkowe porady
Dla każdego tabletu możesz utworzyć osobny plik .dash i przypisać mu unikalny adres URL.
W panelu Visionect warto również dostosować częstotliwość odświeżania (Refresh rate), aby zbalansować aktualność danych i zużycie baterii.
![alt text](https://github.com/user-attachments/assets/9f0c1741-76f3-496d-ad44-e316d29621f1)
⭐ Integracja z Home Assistant (Odczyt stanu tabletu)
Aby odczytywać w Home Assistant informacje o stanie tabletu (np. poziom naładowania baterii, status połączenia), możesz skorzystać z niestandardowej integracji HACS dostępnej w moim drugim repozytorium. Pozwoli to na tworzenie automatyzacji, np. wysyłania powiadomienia o niskim stanie baterii.
Link do repozytorium z integracją zostanie dodany wkrótce.
58.4s
