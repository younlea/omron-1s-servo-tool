# Omron Servo EtherCAT Controller

Python and PySOEM based GUI application for controlling Omron 1S Series (R88D-1SN02H-ECT) Servo Drives via EtherCAT.

## 📝 Features

- **EtherCAT Communication:** Real-time control using `pysoem`.
- **CiA402 Drive Profile:** Supports standard state machine handling.
- **Dual Control Modes:**
  - Cyclic Synchronous Velocity (CSV)
  - Cyclic Synchronous Torque (CST)
- **Real-time Monitoring:** High-performance plotting of Actual Velocity and Torque using `pyqtgraph`.
- **Safety Functions:** Emergency Stop, Fault Reset, and Servo On/Off control.

## 🛠 Tech Stack

- **Python 3.8+**
- **PyQt6**: User Interface
- **PySOEM**: EtherCAT Master Stack
- **PyQtGraph**: Real-time plotting

## 🔌 Hardware Requirements

- **PC:** Linux (Ubuntu recommended for real-time performance) or Windows.
- **Network Adapter:** Standard Ethernet port (must be dedicated to EtherCAT).
- **Servo Drive:** Omron 1S Series (R88D-1SN02H-ECT) or compatible CiA402 drives.

## 🚀 Installation

1. **Clone the repository**
   ```bash
   git clone [https://github.com/your-username/omron-ethercat-controller.git](https://github.com/your-username/omron-ethercat-controller.git)
   cd omron-ethercat-controller
   ```

2. **Install Dependencies**
   ```bash
   pip install -r requirements.txt
   ```
   *(Ensure you have `pysoem`, `PyQt6`, `pyqtgraph` installed)*

3. **OS Specific Setup (Linux)**
   PySOEM requires raw socket access. You need to run the script with `sudo` or set capabilities.
   ```bash
   # Option 1: Run with sudo (Easiest for testing)
   sudo python3 main.py

   # Option 2: Set capabilities (Recommended)
   sudo setcap cap_net_raw+ep $(readlink -f $(which python3))
   ```

## 📖 Usage

1. **Connect the Hardware:** Connect the PC Ethernet port to the EtherCAT IN port of the Servo Drive.
2. **Find Interface Name:** Check your network interface name (e.g., `eth0`, `enp3s0`, `Ethernet 2`).
3. **Run the App:**
   ```bash
   sudo python3 main.py
   ```
4. **Operation Steps:**
   - Enter the **Interface Name** and click **Connect**.
   - Click **Servo ON** (Status should change to 'Operation Enabled').
   - Select **Mode** (Velocity or Torque).
   - Use the **Sliders** to control the motor.
   - If an error occurs, click **Fault Reset**.

## 📊 PDO Mapping Strategy

This application uses the following PDO mapping based on Omron 1S User Manual:

| Type | Index | Sub | Name | Data Type |
|------|-------|-----|------|-----------|
| **Rx** | 0x6040 | 00 | Controlword | UINT16 |
| **Rx** | 0x6060 | 00 | Modes of Operation | INT8 |
| **Rx** | 0x6071 | 00 | Target Torque | INT16 |
| **Rx** | 0x60FF | 00 | Target Velocity | INT32 |
| **Tx** | 0x6041 | 00 | Statusword | UINT16 |
| **Tx** | 0x606C | 00 | Velocity Actual Value | INT32 |
| **Tx** | 0x6077 | 00 | Torque Actual Value | INT16 |
| **Tx** | 0x603F | 00 | Error Code | UINT16 |

## ⚠️ Safety Warning

- This software is for **testing and educational purposes**.
- Always ensure the motor is secured before operation.
- Be ready to press the **STOP** button or cut power in case of instability.

## License

MIT License
