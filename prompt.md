# Project: Omron Servo EtherCAT Controller App

## 1. Project Overview
Python을 사용하여 Omron R88D-1SN02H-ECT 서보 드라이브를 제어하고 모니터링하는 GUI 애플리케이션을 개발합니다.
EtherCAT 통신을 통해 모터의 속도(Velocity)와 토크(Torque)를 제어하며, 실시간 데이터를 그래프로 시각화해야 합니다.

## 2. Tech Stack
- **Language:** Python 3.x
- **EtherCAT Master Library:** `pysoem` (Simple Open EtherCAT Master for Python)
- **GUI Framework:** `PyQt6` (or PySide6)
- **Plotting Library:** `pyqtgraph` (Must use this for high-performance real-time plotting)
- **OS Target:** Linux (Ubuntu) preferred, but code should be OS-agnostic regarding path handling.

## 3. Core Requirements

### A. EtherCAT Communication (Backend)
1.  **Thread Separation:** creating a separate `QThread` or `threading.Thread` for the EtherCAT cyclic loop (1ms or 2ms cycle). The GUI must not freeze during communication.
2.  **PDO Mapping (Based on Omron 1S Manual):**
    -   **RxPDO (Output to Drive):**
        -   `0x6040:00` (Controlword, UINT16)
        -   `0x6060:00` (Modes of Operation, INT8)
        -   `0x6071:00` (Target Torque, INT16)
        -   `0x60FF:00` (Target Velocity, INT32)
    -   **TxPDO (Input from Drive):**
        -   `0x6041:00` (Statusword, UINT16)
        -   `0x6061:00` (Modes of Operation Display, INT8)
        -   `0x606C:00` (Velocity Actual Value, INT32)
        -   `0x6077:00` (Torque Actual Value, INT16)
        -   `0x603F:00` (Error Code, UINT16)
3.  **CiA402 State Machine:** Implement logic to transition the drive state:
    -   Not Ready -> Switch On Disabled -> Ready to Switch On -> Switched On -> Operation Enabled.

### B. GUI Features (Frontend)
The GUI should be organized into the following sections:

1.  **Connection Section:**
    -   Network Interface Selection (Input text or Combo box to select `eth0`, `enp3s0`, etc.).
    -   Connect / Disconnect Button.
2.  **Control Panel (Left Side):**
    -   **Servo State:** Servo ON / Servo OFF Buttons.
    -   **Mode Selection:** Radio Button or ComboBox for 'Velocity Mode (CSV)' vs 'Torque Mode (CST)'.
        -   Write `9` to `0x6060` for CSV.
        -   Write `10` to `0x6060` for CST.
    -   **Safety:**
        -   **STOP (Emergency)** Button: Immediately set Target Velocity/Torque to 0.
        -   **Error Reset** Button: Toggle Bit 7 of Controlword (0 -> 1 -> 0) to reset faults.
3.  **Command Input (Center):**
    -   **Target Velocity:** Slider and SpinBox (Range: -3000 to 3000).
    -   **Target Torque:** Slider and SpinBox (Range: -1000 to 1000, representing 0.1% units).
    -   Input should only be active when the relevant mode is selected.
4.  **Monitoring (Right Side):**
    -   **Real-time Graphs:** Two separate plots using `pyqtgraph` (Velocity vs Time, Torque vs Time).
    -   **Status Indicators:**
        -   Current Statusword (Hex).
        -   Current Error Code (Hex).
        -   "Ready" / "Run" / "Error" LED indicator style.

## 4. Implementation Details
-   **Signals & Slots:** Use PyQt Signals to pass data from the EtherCAT thread to the GUI thread to update graphs safely.
-   **Safety Logic:** Even if the GUI crashes, ensure the `__exit__` or cleanup routine sends a "Stop" command or sets Controlword to `0` to stop the motor safely.
-   **Config:** Define PDO Indexes in a dictionary or constant class for easy maintenance.

## 5. Deliverables
-   `main.py`: Entry point application.
-   `ethercat_worker.py`: Class handling PySOEM logic.
-   `gui_main.py`: Main window layout and logic.
-   `requirements.txt`: Dependencies.

Please write the complete, runnable code structure following these instructions.
