# Wazuh_SOC_Dashboard

Projekt przedstawia autorski dashboard stworzony w środowisku Wazuh/Kibana, mający na celu scentralizowane monitorowanie bezpieczeństwa stacji roboczej z systemem Windows 10. Celem prac było wyeliminowanie szumu informacyjnego i stworzenie przejrzystego panelu operacyjnego dla analityka bezpieczeństwa.

## Hardware & Host OS 

* **System operacyjny hosta:** Windows 10 Pro (64-bit)
* **Procesor (CPU):** Intel(R) Core(TM) i7-2600 CPU @ 3.40GHz
* **Pamięć RAM:** 32,0 GB
* **Dysk:** SSD Patriot P210 512GB

## Software Stack
* **Hiperwzorzec:** Oracle VirtualBox (wersja 7.2.10) – posłużył do stworzenia izolowanej sieci, w której maszyny komunikują się w obrębie podsieci `192.168.0.0/24`, zapewniając stabilne środowisko laboratoryjne.
* **SIEM / XDR:** Wazuh Manager (Virtual Appliance oparty na Ubuntu) – `192.168.0.115` (Centralny punkt zbierania i analizy logów).
* **Endpoint (Ofiara):** Windows 10 Pro – `192.168.0.110` (Zainstalowany agent Wazuh oraz sensor Microsoft Sysmon z konfiguracją SwiftOnSecurity).
* **System Atakującego:** Kali Linux – adresacja w tej samej podsieci `192.168.0.109` (Platforma do generowania ruchu i symulacji ataków).

## Główne Funkcjonalności Dashboardu
Zaprojektowany przeze mnie dashboard zawiera moduły kluczowe dla szybkiej oceny stanu bezpieczeństwa hosta:

### 1. Panel Procedur Reagowania (Playbook)
Wykorzystałem moduł `Markdown`, aby umieścić na pulpicie podręczną instrukcję postępowania (tzw. SOC Playbook). Dzięki temu, w przypadku wykrycia alertu o poziomie krytycznym (Level 12+), analityk ma przed oczami konkretne kroki, co skraca czas reakcji (MTTR - Mean Time To Respond).

### 2. Monitoring Stanu i Zagrożeń
**Alert Level Gauge:** Wskaźnik typu `Gauge`, który wizualizuje skalę zagrożenia na podstawie zliczonych alertów. Pozwala na błyskawiczne określenie, czy system znajduje się w stanie "bezpiecznym", czy wymaga interwencji.
**Metric Counter:** Licznik prezentujący zagregowaną liczbę alertów w czasie rzeczywistym, służący jako główne "powiadomienie" o incydentach.

### 3. Analiza Trendów
**Line Chart (Activity):** Wykres liniowy prezentujący natężenie zdarzeń w czasie. Pozwala zidentyfikować anomalie, takie jak nagłe skoki aktywności, które mogą wskazywać na skanowanie portów lub próby przejęcia uprawnień.
**Pie Chart (Severity Distribution):** Wizualizacja rozkładu ważności zdarzeń, która pomaga priorytetyzować pracę analityka.

### 4. Szczegółowy Rejestr Zdarzeń
Dolna sekcja dashboardu to konfigurowalna tabela, która filtruje logi według:
* Nazwy reguły (`rule.description`).
* Poziomu ważności (`rule.level`).
* Nazwy agenta (`agent.name`).

## Wdrożenie techniczne

Aby Dashboard działał poprawnie na wybranym agencie, zastosowałem globalny filtr w środowisku Kibana:
`agent.ip: "192.168.0.110"`
Pozwoliło to na odizolowanie logów ofiary od logów systemowych serwera, co drastycznie podniosło czytelność danych. Wizualizacje zostały oparte na domyślnych indeksach `wazuh-alerts-*` z wykorzystaniem agregacji `Terms` oraz `Date Histogram` dla wykresów czasowych.

## Podgląd Systemu
