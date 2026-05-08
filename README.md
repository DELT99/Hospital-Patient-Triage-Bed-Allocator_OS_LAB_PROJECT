# Hospital Patient Triage & Bed Allocator
**CL2006 Operating Systems Lab | FAST-NUCES CFD | Spring 2026**
#Muhammad Talha 24F0793
#KHIZAR HAYAT 24F0812

## How to Build and Run

```bash
# 1. Build
make all

# 2. Start hospital (Terminal 1)
cd ~/hospital
make run

# 3. Send a patient (Terminal 2)
./scripts/triage.sh "Ali Raza" 28 9 0

# 4. Stress test (20 patients)
make stress

# 5. Stop hospital
make stop
```

## triage.sh Usage
./scripts/triage.sh <name> <age> <severity 1-10> [infectious 0|1]

| Severity | Priority | Category  | Bed Type  |
|----------|----------|-----------|-----------|
| 9–10     | 1        | Critical  | ICU       |
| 7–8      | 2        | Urgent    | ICU       |
| 5–6      | 3        | Moderate  | Isolation |
| 3–4      | 4        | Non-urgent| General   |
| 1–2      | 5        | Minor     | General   |

## Allocation Strategy

```bash
./admissions --strategy best    # Best-Fit  (default)
./admissions --strategy first   # First-Fit
./admissions --strategy worst   # Worst-Fit
```

## Dependencies
- gcc
- Standard Linux POSIX libraries

## Logs
- `logs/memory_log.txt`  — fragmentation after each event
- `logs/schedule_log.txt`— FCFS / Priority / SJF simulation


============================================================
  OS CONCEPTS DEMONSTRATED
============================================================

  fork() + execl()     Each patient spawns a child process
  Anonymous Pipe       PatientRecord passed to child via pipe
  Named FIFO           triage.sh to admissions (triage_fifo)
                       patient_simulator to admissions (discharge_fifo)
  Shared Memory        Bed bitmap at key 0xBEDF00D
  POSIX Threads        Receptionist, Scheduler, 3 Nurse threads
  Mutex                bed_mutex protects bed bitmap
  Condition Variable   bed_freed wakes scheduler on discharge
  Semaphores           sem_icu and sem_iso enforce capacity limits
  Best-Fit Allocator   Minimum waste partition selection
  Coalescing           Adjacent free partitions merged after discharge
  CPU Scheduling Sim   FCFS, Priority, SJF with avg metrics

============================================================
  WARD CONFIGURATION
============================================================

  Bed Type     Count    Care Units Each    Total Units
  ----------   -----    ---------------    -----------
  ICU            4            3                12
  Isolation      4            2                 8
  General       12            1                12
  ----------   -----    ---------------    -----------
  TOTAL         20            -                32
============================================================

## Valgrind - ZERO MEMORY LEAKS

==XXXX== HEAP SUMMARY:
==XXXX==   in use at exit: 0 bytes in 0 blocks
==XXXX==   total heap usage: X allocs, X frees, X bytes allocated
==XXXX==
==XXXX== All heap blocks were freed -- no leaks are possible
==XXXX== ERROR SUMMARY: 0 errors from 0 contexts