# Visionect Server 3 na Proxmox z Docker

## 🧰 Wymagania

- Proxmox VE z dostępem do internetu
- Uprawnienia roota na serwerze
- Podstawowa znajomość terminala
- Tablet Visionect i komputer z Windows (do konfiguracji tabletu)

---

## 🚀 Instalacja Dockera w maszynie wirtualnej Proxmox

1. Zaloguj się do Proxmox WebUI
2. Otwórz **Shell** serwera głównego (nie VM)
3. Uruchom skrypt instalacyjny:

   ```bash
   bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/docker-vm.sh)"
