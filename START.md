# LIS∞ Implementierung - Quick Start Guide

Schneller Einstieg in die Ausführung und Verwendung der LIS∞-Implementierung


## Was Sie erhalten

```
LIS∞ Framework — 3 Module, ~2250 Zeilen C-Code

LISInfinityScheduler.c (29 KB)
  └─ Kern-Framework: Pulsleitung, Register, CPU-Simulation, LIS∞-Algorithmus

LISIfinity.c (29 KB)
  └─ Erweiterte Features: Cache, Model Checking, Subgraph-Algorithmus, Multicore

LISInfinityVerification.c (25 KB)
  └─ Formale Verifikation: State Space Exploration, Deadlock-Detection, Benchmarking

Makefile (4 KB) + START.md + SUMMARY.md (Dokumentation)
```


## 5-Minuten Setup

### 1. Voraussetzungen prüfen

```bash
# Prüfe GCC Installation
gcc --version

# Prüfe Make Installation
make --version
```

Beide sollten verfügbar sein. Falls nicht:
```bash
# Auf Linux (Debian/Ubuntu)
sudo apt-get install gcc make

# Auf macOS
brew install gcc make
```

### 2. Kompiliere alles

```bash
cd /path/to/lis_infinity
make
```

Das erstellt 3 Binaries in `./bin/`:
- `bin/lis_scheduler` — Kern-Framework
- `bin/lis_extended` — Cache & Model Checking
- `bin/lis_verification` — Verifikation & Benchmarking

### 3. Führe Tests aus

```bash
make run
```

Das startet alle 3 Programme nacheinander mit Demos und Tests.


## Schnelle Beispiele

### Beispiel 1: Einfaches Test-Programm ausführen

```bash
./bin/lis_scheduler
```

**Output:**
```
==============================================
LIS∞ SCHEDULER - AUSFÜHRUNG
==============================================

=== TEST 1: LOAD-STORE Operationen ===
Takt 0: PC=0 | LOAD R0, 0x1000
Takt 1: PC=1 | STORE R0, 0x2000
Takt 2: PC=2 | HALT

=== PROGRAMMAUSFÜHRUNG BEENDET ===
Instruktionen ausgeführt: 3
Gesamt-Pulse: 9

=== VERIFIKATION DER LOKALEN KONSISTENZ ===
[OK] Invariante 1 (Register-Exklusivität): ERFÜLLT
[OK] Invariante 2 (Pulsleitung-Serialität): ERFÜLLT
[OK] Invariante 3 (Kausal-Ordnung): ERFÜLLT
[OK] Invariante 4 (Deterministisches Branching): ERFÜLLT

[SUCCESS] ALLE INVARIANTEN ERFÜLLT — LOKALE KONSISTENZ GEWÄHRLEISTET
```

### Beispiel 2: Cache-Hierarchie ansehen

```bash
./bin/lis_extended
```

**Output zeigt:**
- Cache-Hit-Raten auf verschiedenen Layern (L1/L2/L3)
- Puls-Kosten für Zugriffe
- Lokalitäts-Effekt: Sequenzielle Zugriffe ~5x schneller!

### Beispiel 3: State Space & Benchmarks

```bash
./bin/lis_verification
```

**Output zeigt:**
- State Space Exploration (BFS/DFS)
- Deadlock-Detection
- Liveness-Verifikation
- Benchmark-Ergebnisse mit Performance-Metriken


## Häufige Befehle

```bash
# Kompiliere nur Kern-Framework
make lis_scheduler

# Führe nur Verifikation aus
make run_verification

# Führe Benchmarks aus
./bin/lis_verification

# Räume auf (lösche Binaries)
make clean

# Zeige Hilfe
make help
```


## Was Sie sehen werden

### Test 1: Lokale Konsistenz-Verifikation

```
[OK] Invariante 1 (Register-Exklusivität): ERFÜLLT
[OK] Invariante 2 (Pulsleitung-Serialität): ERFÜLLT
[OK] Invariante 3 (Kausal-Ordnung): ERFÜLLT
[OK] Invariante 4 (Deterministisches Branching): ERFÜLLT

[SUCCESS] ALLE INVARIANTEN ERFÜLLT
```

**Bedeutung:** Der LIS∞-Algorithmus garantiert mathematisch, dass:
- Jeder Zustand zu genau einem Nachfolgezustand führt (Determinismus)
- Keine Pulsleitung-Konflikte auftreten (Serialität)
- Kausalität gewahrt bleibt (Ordnung)
- Sprünge eindeutig sind (Branch-Determinismus)

### Test 2: Cache-Performance-Vergleich

```
Szenario 1: Zufällige Zugriffe (schlecht lokalisiert)
Pulse-Kosten: 8500

Szenario 2: Sequenzielle Zugriffe (gut lokalisiert)
Pulse-Kosten: 1700

=== LOKALITÄTSPRINZIP EFFEKT ===
Zufällig / Sequenziell = 5.0x
→ Sequenzielle Zugriffe sind bis zu 5-6x schneller!
```

**Bedeutung:** Das Lokalitätsprinzip wird praktisch demonstriert:
- L1-Zugriff: 1 Puls (Register)
- L2-Zugriff: 3 Pulse
- L3-Zugriff: 10 Pulse
- RAM-Zugriff: 100 Pulse (teuer!)

### Test 3: State Space Exploration

```
=== STATE SPACE EXPLORATION (BFS) ===
Tiefe 0: 1 Zustände
Tiefe 1: 1 Zustände
...
Tiefe 9: 1 Zustände

→ Gesamt erreichbare Zustände: 10/10
→ Terminal-Zustände: 1

=== DEADLOCK-DETECTION ===
→ KEINE Deadlocks gefunden (System ist deadlock-free)

=== LIVENESS-VERIFIKATION ===
Eigenschaft: F (HALT_reached)
→ Von 10/10 Zuständen erreichbar: [OK] ERFÜLLT
```

**Bedeutung:** Beweise dass:
- Alle Zustände vom Start erreichbar sind
- Kein Deadlock (unerwartetes Hängenbleiben) möglich
- Programm terminiert garantiert (Liveness)


## Verständnis der Ausgaben

### Pulsleitung-Architektur

```
CPU ─────┬─────→ Adressbus (16-Bit): Identifiziert Adresse
         ├─────→ Datenbus (8-Bit): Transportiert Daten
         └─────→ Kontrollbus (3 Leitungen): Read/Write/Enable
                    ↓
                SPEICHER/PERIPHERIE
```

**Jeder Puls ist eine atomare Operation** — nicht teilbar, lokal konsistent.

### Instruktions-Pulssequenzen

```
LOAD R0, 0x1000:
  Puls 1: Adresse 0x1000 auf Adressbus
  Puls 2: Read-Signal auf Kontrollbus
  Puls 3: Daten in Register R0 schreiben
  ──────
  Total: 3 Pulse

STORE R0, 0x2000:
  Puls 1: Adresse 0x2000 auf Adressbus
  Puls 2: Daten (aus R0) auf Datenbus
  Puls 3: Write-Signal auf Kontrollbus
  ──────
  Total: 3 Pulse

ALU R2 ← R0 + R1:
  Puls 1: Operanden auslesen
  Puls 2: Addition durchführen
  Puls 3: Ergebnis in R2 speichern
  ──────
  Total: 3 Pulse
```

### Performance-Metriken

```
Durchschn. Puls pro Zugriff = Total Pulse / Total Zugriffe

Gutes System: ~1-3 Pulse (viel in L1)
Schlechtes System: ~50-100 Pulse (viel in RAM)

L1 Hit Rate: > 80% → Gut
L2 Hit Rate: > 50% → Noch okay
L3 Hit Rate: > 30% → Problematisch
RAM Access: < 5% → Ideal
```


## Beispiel: Ihr erstes Programm schreiben

Bearbeite `LISInfinityScheduler.c`, suche die `main()`-Funktion:

```c
int main(void) {
    /* Test-Programm hinzufügen: */
    
    printf("\n==============================================\n");
    printf("MEIN ERSTES PROGRAMM\n");
    printf("==============================================\n");
    
    LISInfinitySystem *sys = lis_init(5);
    
    /* Speicher initialisieren */
    sys->mem->memory[0x1000] = 100;
    
    /* Programm schreiben:
       R0 ← Memory[0x1000]
       R1 ← 50
       R2 ← R0 + R1
       HALT
    */
    
    sys->program[0].type = INSTR_LOAD;
    sys->program[0].operands.load.dest_reg = 0;
    sys->program[0].operands.load.address = 0x1000;
    
    sys->program[1].type = INSTR_ALU;
    sys->program[1].operands.alu.dest_reg = 2;
    sys->program[1].operands.alu.op1_reg = 0;
    sys->program[1].operands.alu.op2_reg = 1;
    sys->program[1].operands.alu.opcode = ALU_ADD;
    
    sys->program[2].type = INSTR_HALT;
    
    /* Ausführen */
    lis_execute(sys);
    
    /* Ergebnis ansehen */
    printf("\nErgebnis: R2 = %d (erwartet: 150)\n", sys->regs->r[2]);
    
    lis_cleanup(sys);
    
    return 0;
}
```

Dann kompilieren und ausführen:
```bash
make clean && make run_scheduler
```


## Weitere Ressourcen

**Im Package enthalten:**
- `SUMMARY.md` — Detaillierte Übersicht aller Komponenten
- Quellcode-Kommentare in den .c Dateien

**Für vertieftes Verständnis:**
1. Lese SUMMARY.md für technische Details
2. Studiere die Quellcode-Kommentare in den .c Dateien
3. Führe die Demo-Programme aus und analysiere den Output


## Troubleshooting

### Problem: `gcc: command not found`
**Lösung:** Installiere GCC
```bash
# Linux
sudo apt-get install build-essential

# macOS
brew install gcc
```

### Problem: `make: command not found`
**Lösung:** Installiere Make
```bash
# Linux
sudo apt-get install make

# macOS
brew install make
```

### Problem: Kompilation schlägt fehl
**Lösung:** Prüfe C99-Unterstützung
```bash
gcc --std=c99 -Wall -Wextra -O2 LISInfinityScheduler.c -o test
```

### Problem: Programme zeigen `Segmentation Fault`
**Lösung:** Mit Debug-Flags kompilieren
```bash
gcc -g -Wall -Wextra LISInfinityScheduler.c -o test
gdb ./test
```


## Erfolgs-Checkliste

Wenn Sie folgendes sehen, funktioniert alles richtig:

- [ ] `make` kompiliert ohne Fehler
- [ ] `make run` startet alle 3 Programme
- [ ] Alle 4 Invarianten sind "ERFÜLLT"
- [ ] Keine Deadlocks gefunden
- [ ] Liveness-Verifikation erfolgreich
- [ ] Cache-Demo zeigt ~5x Speedup
- [ ] Benchmarks geben Zahlen aus
- [ ] Alle Tests "OK" oder "SUCCESS"

Wenn ja, LIS∞ läuft auf Ihrem System!


## Nächste Schritte

1. **Experimentieren:** Ändern Sie die Test-Programme
2. **Verstehen:** Lesen Sie die Quelltexte und Kommentare
3. **Erweitern:** Schreiben Sie eigene Instruktionen oder Optimierungen
4. **Verifizieren:** Nutzen Sie State Space Explorer für Ihre Programme
5. **Messen:** Benchmarken Sie Ihre Implementierungen


## Pädagogischer Wert

Diese Implementierung lehrt Sie:

- **Low-Level CPU-Design** — Wie CPUs wirklich funktionieren
- **Formale Verifikation** — Mathematische Garantien für Korrektheit
- **Cache-Architektur** — Warum Lokalität so wichtig ist
- **Model Checking** — Automatische Zustandsraum-Analyse
- **Determinismus** — Warum Vorhersagbarkeit essentiell ist
- **Pulsleitung-Konzepte** — Physikalische Hardware-Abstraktion