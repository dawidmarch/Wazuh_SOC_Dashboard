# Case Study: Analiza próby nieautoryzowanego dostępu (Brute-Force SMB)

## 1. Cel mojego projektu
Celem mojego projektu było przeprowadzenie symulacji ataku typu **Brute-Force** na usługę SMB w izolowanym środowisku laboratoryjnym (Oracle VirtualBox). Eksperyment miał na celu zademonstrowanie zdolności systemu **Wazuh SIEM** oraz **Sysmon** w zakresie wykrywania wielokrotnych prób nieautoryzowanego logowania i naruszeń polityki bezpieczeństwa uwierzytelniania.

## 2. MITRE ATT&CK Mapowanie

| Tactic | Technique | ID | Opis |
| :--- | :--- | :--- | :--- |
| **Credential Access** | Brute Force | T1110 | Wielokrotne próby odgadnięcia hasła w celu uzyskania dostępu. |
| **Initial Access** | Valid Accounts | T1078 | Próba zalogowania na istniejące konto administratora. |

*Wyjaśnienie:* Wykorzystałem narzędzie `smbclient` do masowej próby uwierzytelnienia się do zasobów SMB ofiary, co miało na celu wywołanie zdarzeń typu "Failed Logon" w dziennikach systemu Windows.

## 3. Metodologia mojego ataku

### Faza A: Przygotowanie środowiska (Kali Linux - Atakujący)
Na mojej maszynie atakującego zainicjowałem sesję terminalową, aby przeprowadzić atak na usługę SMB.

**Komendy, których użyłem (próba logowania):**
```bash
# Próba połączenia z zasobem SMB z błędnymi hasłami
smbclient //192.168.0.110/C$ -U administrator%haslo123
