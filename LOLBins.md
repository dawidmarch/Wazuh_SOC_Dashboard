# Case Study: Analiza próby nieautoryzowanego dostępu (Brute-Force SMB)

## 1. Cel mojego projektu
Celem mojego projektu było przeprowadzenie symulacji zaawansowanego ataku typu **Living-off-the-land (LOLBins)**. Eksperyment miał na celu zademonstrowanie przeze mnie zdolności detekcyjnych systemu **Wazuh SIEM** oraz sensora **Sysmon** w zakresie wykrywania podejrzanego wykorzystania legalnych narzędzi systemowych (`SecEdit.exe`, `PowerShell`) do celów zwiadowczych,

## 2. MITRE ATT&CK Mapowanie
| Tactic | Technique | ID | Opis |
| :--- | :--- | :--- | :--- |
| **Execution** | PowerShell | T1059.001 | Wykonanie złośliwego kodu przez interpretator PowerShell. |
| **Discovery** | File and Directory Discovery | T1083 | Przeszukiwanie systemu w celu pozyskania informacji. |

*Wyjaśnienie:* Wykorzystałem wbudowane binarki systemowe, co pozwoliło mi na ominięcie klasycznych sygnatur antywirusowych i przetestowanie reakcji systemu monitorującego na "bezzłośliwe" narzędzia użyte w niebezpiecznym kontekście.

## 3. Metodologia mojego ataku
### Faza A: Przygotowanie środowiska (Kali Linux - Atakujący)
Na mojej maszynie atakującego zainicjowałem próbę uzyskania dostępu do zasobów systemu Windows.

**Komendy, których użyłem (próba logowania):**
```bash
# Próba połączenia z zasobem SMB
smbclient //192.168.0.110/C$ -U administrator%haslo123
