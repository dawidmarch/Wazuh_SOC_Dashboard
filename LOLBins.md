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
```
<img width="659" height="557" alt="3" src="https://github.com/user-attachments/assets/3b5b9920-53be-4bbb-ac6f-7f5e2a71df7a" />

### Faza B: Wykonanie ataku Brute-Force (Windows 10 - Ofiara)
Z poziomu **Kali Linux** wykonałem sekwencję prób połączenia z zasobem `C$` przy użyciu różnych kombinacji haseł. Każda nieudana próba logowania była monitorowana przez system operacyjny **Windows 10** i przesyłana w czasie rzeczywistym do serwera **Wazuh Manager**.

**Procedura, którą wykonałem:**
1. Użyłem narzędzia `smbclient` w celu wygenerowania serii prób uwierzytelnienia.
2. Każda nieudana próba logowania generowała zdarzenie **Event ID 4625** (*An account failed to log on*) w dzienniku zdarzeń *Security* systemu Windows, co pozwoliło na identyfikację ataku przez agenta Wazuh.

## 4. Moja analiza logów (Wazuh Dashboard)
Po wykryciu aktywności przez system, poddałem alerty analizie wewnątrz dashboardu Wazuh.

<img width="1879" height="761" alt="1" src="https://github.com/user-attachments/assets/883972b6-9ec6-4d90-9487-f4c86bc9c592" />

<img width="1875" height="765" alt="2" src="https://github.com/user-attachments/assets/1a62d88c-4fa1-4382-b40b-8ed5e285465c" />

**Mój proces badawczy:**
- **Weryfikacja licznika:** Zaobserwowałem skok alertów (Count: 41), co było dla mnie sygnałem, że próby połączenia SMB zostały zarejestrowane przez agenta Wazuh.
- **Analiza reguł:** Zidentyfikowałem kluczowe reguły: `Failed password attempt` oraz korelacje zdarzeń `Event ID 4625`.
- **Ekstrakcja danych (JSON):** Przeanalizowałem surowy plik JSON, w którym zidentyfikowałem kluczowe parametry ataku:
    - **Rule Description:** `Multiple failed login attempts`
    - **Authentication Result:** `Failure`
    - **Target User:** `administrator`
    - **EventID:** `4625` (Failed Logon)

## 5. Wnioski techniczne 
- **Krytyczność:** Podczas moich testów potwierdziłem, że system SIEM jest kluczowy dla wykrywania prób łamania haseł. Nawet pojedyncze próby połączenia (Error: NT_STATUS_IO_TIMEOUT) zostawiają ślad, który pozwala analitykowi zidentyfikować źródło ataku (adres IP Kali Linux).
- **Widoczność:** Zrozumiałem, jak ważne jest monitorowanie logów `Security` (EventChannel) na Windowsie. Bez odpowiedniej konfiguracji `ossec.conf` dla tego kanału, ataki typu brute-force pozostałyby niezauważone przez dashboard.
