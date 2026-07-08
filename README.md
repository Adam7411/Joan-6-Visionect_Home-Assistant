***
- [Jeśli chcesz bezpośrednio zainstalować Visionect Software Suite w Home Assistant kliknij --> Visionect-V3-Allinone](https://github.com/Adam7411/visionect-v3-allinone/blob/main/visionect-v3-allinone/README_pl.md)
- [Dodatek Visionect Joan dla Home Assistant](https://github.com/Adam7411/visionect_joan/blob/main/README_pl.md)
- [Dodatek Joan 6/13PRO: AppDaemon Dashboard Generator](https://github.com/Adam7411/joan_generator/)

***


# Visionect Software Suite - Instalacja w Proxmox

--> [English version](https://github.com/Adam7411/Joan-6-Visionect_Home-Assistant_EN)

<img width="841" alt="logojoan" src="https://github.com/user-attachments/assets/36499aed-4e26-404e-b75d-13082303f039" />

<img width="841" alt="aaan" src="https://github.com/user-attachments/assets/8327c8a7-910d-4863-bdf1-94a4e1610f7c" />




<img width="841" alt="aaan" src="https://github.com/user-attachments/assets/a729ed1b-8fb3-4390-ba46-7caa0d1d8223" />
<img width="1484" height="1278" alt="Bez tytułu" src="https://github.com/user-attachments/assets/fecc6864-c626-44f2-b59f-11c90226a86d" />






_______________________________________________________________________________________________

![image](https://github.com/user-attachments/assets/054cda40-bb31-4192-9b8d-c88860b5e144)
![image](https://github.com/user-attachments/assets/440e108e-4ffa-497a-893c-9be2b7d67f02)![image](https://github.com/user-attachments/assets/db4b8a13-7e46-43b6-a745-67962b7457ab)


---

## 🧰 Wymagania wstępne

- Serwer z zainstalowanym **Proxmox VE** i dostępem do internetu. Jeśli chcesz bezpośrednio zainstalować Visionect Software Suite w Home Assistant kliknij --> [Visionect-V3-Allinone](https://github.com/Adam7411/visionect-v3-allinone)
- Uprawnienia administratora (root) do serwera Proxmox.
- Komputer z systemem Windows, Linux lub macOS (do wstępnej konfiguracji tabletu).
- Tablet **Visionect Joan 6/13PRO**. 
- Dodatek HACS [Visionect Joan](https://github.com/Adam7411/visionect_joan)
> **Ważna uwaga:** Kontener Docker z oprogramowaniem Visionect Software Suite (serwerem zarządzającym tabletem) musi być stale uruchomiony. Jeśli korzystasz już z Home Assistant na Proxmoxie, nie stanowi to dodatkowego obciążenia. Aczkolwiek HA może działać na innym urządzeniu.

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
2.  Proces może chwilę potrwać, ponieważ Docker musi pobrać wymagane obrazy: 
`postgres_db`, `redis` oraz `vserver3`.

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
    *   **Windows:** [VisionectConfigurator.exe](https://files.visionect.com/VisionectConfigurator/VisionectConfigurator.exe) lub [joan-configurator-2.1.3-windows.exe](https://configurator.getjoan.com/download/flavor/joan/latest/windows_64) 
    *   **Windows: wersje 1.3.10-->** [VisionectConfigurator1.3.10.exe](https://files.visionect.com/VisionectConfigurator2.exe) lub [VC_1.exe](https://files.visionect.com/VC_1.exe) gdy wyświetlacz e-ink niełączy się z nową (Hardware Revision Second generation Visionect Sign 6) niekiedy trzeba skonfigurować przez [VC_1.exe] albo v1.3.10 cierpliwie pokombinuj.
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
     
Visionect Configurator wersja 2.0  
![image](https://github.com/user-attachments/assets/de30fd1e-9bd3-4f98-ab00-9a3b534f7332)


Visionect Configurator wersja 1.0 

<img width="754" height="501" alt="Bez tytułu" src="https://github.com/user-attachments/assets/a9908c7c-686d-430c-934d-69d95d50e3b6" />


_______________________________________________


4.  Kliknij przycisk, aby połączyć tablet z serwerem.
5.  Po chwili tablet powinien pojawić się w panelu **Visionect Software Suite** na liście urządzeń.


---

## ✏️ Krok 6: Tworzenie dashboardu w Home Assistant 

[Można przez Joan 6/13PRO AppDaemon dashboard Generator](https://github.com/Adam7411/joan_generator/blob/main/README.md)👈️
_____________________________________________________________________________

Dobra lecimy dalej, aby wyświetlić interfejs Home Assistant na tablecie, użyjemy dodatku **AppDaemon**.

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
    `http://<adres_ip_ha>:5050/joan1`  
    (zmień `joanl` na nazwę swojego pliku `.dash` jeśli nie chcesz używać przykładowych plików).
8.  Skopiuj ten adres URL.
9.  Wróć do panelu **Visionect Software Suite**, przejdź do ustawień swojego tabletu i w polu **Default URL** wklej skopiowany adres dashboardu. Zapisz zmiany.
    ![image](https://github.com/user-attachments/assets/00558b5d-ad93-44ab-b4f0-ae8e9b1be20f)
10. Po chwili na ekranie tabletu powinien pojawić się twój dashboard z Home Assistant. Dashboard można też ustawiać z poziomu Home Assistant przez dodatek 👉 [Visionect Joan](https://github.com/Adam7411/visionect_joan) 👈
### Dodatkowe porady
*   Dla każdego tabletu możesz utworzyć osobny plik `.dash` i przypisać mu unikalny adres URL.
*   W panelu Visionect warto również dostosować **częstotliwość odświeżania** (`Refresh rate`), aby zbalansować aktualność danych i zużycie baterii.
    ![image](https://github.com/user-attachments/assets/9f0c1741-76f3-496d-ad44-e316d29621f1)

---

## ⭐ Obowiazkowa integracja Visionect Joan dla Home Assistant (Odczyt stanu tabletu i wysyłanie zdjęc url i własnego tekstu)


Integracja do odczytu w Home Assistant informacje o stanie tabletu Joan (np. poziom naładowania baterii, status połączenia itp) 
Do wysyłania swojego adresu url z poziomu HA na Joan 6/13PRO np. ( https://www.wikipedia.org/ ) lub lokalne zdjęć ( przykład http://adresHA:8123/local/zdjecie_test.png ) 
(P.S plik zdjecie_test.png umieszczamy w katalogu: \192.168.xxx.xxx\config\www\) 
Wysyłanie własnego tekstu z zdjęciem na Joan 6 (powiadomień z HA). **Wsparcie szablonów Jinja2 z HA do dynamicznej treści.**

[Visionect Joan](https://github.com/Adam7411/visionect_joan) 👈️
[Visionect Joan](https://github.com/Adam7411/visionect_joan) 👈️

Pozwoli to na tworzenie automatyzacji n.p:

wysyłania powiadomienia o niskim stanie baterii Joan 6 albo wyświetlenie encji z poziomem baterii na Joan 6

wysyłanie zdjęć do różnych powiadomień poczym spowrotem powrót do dashboardu appdaemon itp.

wysyłanie zrzutu z kamery snapshot.jpg

wysyłanie powiadomień tekstowych z Home Assistant na Joan 6

<img width="510" height="739" alt="3" src="https://github.com/user-attachments/assets/8f8c673d-8447-42ec-9d13-0bd4e9683437" />
<img width="948" height="791" alt="2" src="https://github.com/user-attachments/assets/4a3c054a-e239-49c1-ab9d-037584cd7989" />
<img width="607" height="893" alt="1" src="https://github.com/user-attachments/assets/1321cfe8-905d-44ef-b1b9-29d999559a04" />
<img width="770" height="641" alt="4" src="https://github.com/user-attachments/assets/31e9bca1-d7c6-4245-b32f-4c909251bf2c" />
<img width="433" height="290" alt="vvvvu" src="https://github.com/user-attachments/assets/efcf4b46-19ac-4b98-aeff-7fa63a648c65" />


