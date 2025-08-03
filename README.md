<!-- omit in toc -->
# 🛸 Setting Up BETAFPV 2.4GHz AIO RX with RadioMaster Pocket (Meteor75 Pro)

This guide walks you through the full process of setting up the BETAFPV 2.4GHz AIO RX (ELRS) with the RadioMaster Pocket transmitter on a Meteor75 Pro drone.

<!-- omit in toc -->
## **📌 Table of Contents**  
- [📦 Hardware \& Firmware Details](#-hardware--firmware-details)
- [**📻 1. Radio Setup (Radiomaster Pocket)**](#-1-radio-setup-radiomaster-pocket)
  - [**Button Configuration for Meteor75 Pro**](#button-configuration-for-meteor75-pro)
  - [**Creating a New Model**](#creating-a-new-model)
- [**📡 2. ExpressLRS Binding**](#-2-expresslrs-binding)
  - [**Drone Setup**](#drone-setup)
  - [**Radio Setup**](#radio-setup)
- [**💻 3. Betaflight Installation (Linux)**](#-3-betaflight-installation-linux)
- [**⚙️ 4. Betaflight Configuration**](#️-4-betaflight-configuration)
- [**🔄 5. Flight Modes Setup**](#-5-flight-modes-setup)
- [**💾 6. CLI Backup \& Button Mapping**](#-6-cli-backup--button-mapping)
  - [**Creating a CLI Dump**](#creating-a-cli-dump)
  - [**Radiomaster Pocket Button Functions**](#radiomaster-pocket-button-functions)
- [**🚁 7. Arming \& Testing**](#-7-arming--testing)
- [**🔧 Troubleshooting**](#-troubleshooting)

---

## 📦 Hardware & Firmware Details

* **Drone Receiver**: BETAFPV 2.4GHz AIO RX

  * Firmware: `Rev. 3.4.3 (41daca) ISM2G4`

* **Transmitter**: RadioMaster Pocket (Internal 2.4GHz TX)

  * Firmware: `Rev. 3.3.1 (e051b8) ISM2G4`

---

## **📻 1. Radio Setup (Radiomaster Pocket)**  
### **Button Configuration for Meteor75 Pro**
- **SA (3-Pos)**: Arming switch (Up = Armed)
- **SB (3-Pos)**: Flight modes (Up=Angle, Mid=Horizon, Down=Acro)
- **SC (3-Pos)**: Beeper/Telemetry
- **SD (2-Pos)**: Turtle Mode (Flip Over Recovery)

### **Creating a New Model**
1. Press **"MDL"** → Select **"Create Model"**
2. Navigate through pages using **"PAGE>"** button
3. **Model Settings**:
   - Rename to "Meteor75Pro"
   - Set Internal RF to **CRSF**
   - Configure **Special Functions** (Page 10):
     - SA↑: Play Track "Armed"
     - SB↑: Play Track "Angle Mode"
     - SB·: Play Track "Horizon Mode"
     - SB↓: Play Track "Acro Mode"

---

## **📡 2. ExpressLRS Binding**  
### **Drone Setup**
1. **Enter WiFi Mode**:
   - Connect battery 3 times (quick unplug first two)
   - On third connection, LED blinks rapidly
   - Connect to "ExpressLRS RX" WiFi (password: `expresslrs`)
   - Access `http://10.0.0.1/` → Set binding phrase (e.g., `654321`)

### **Radio Setup**
1. **SYS** → ExpressLRS → Enable WiFi
2. Connect to "ExpressLRS TX" → Set matching binding phrase
3. Initiate binding with drone powered

---

## **💻 3. Betaflight Installation (Linux)**  
```sh
# Install Betaflight Configurator
wget https://github.com/betaflight/betaflight-configurator/releases/download/10.10.0/betaflight-configurator_10.10.0_amd64.deb
sudo dpkg -i betaflight-configurator_10.10.0_amd64.deb

# Fix dependencies for Ubuntu 24.10+
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gconf/libgconf-2-4_3.2.6-4ubuntu1_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gconf/gconf2-common_3.2.6-4ubuntu1_all.deb
sudo dpkg -i gconf2-common_3.2.6-4ubuntu1_all.deb
sudo dpkg -i libgconf-2-4_3.2.6-4ubuntu1_amd64.deb
sudo apt-get -f install

# Grant serial access
sudo usermod -aG dialout ${USER}
```

---

## **⚙️ 4. Betaflight Configuration**  
1. Connect via USB → Open Betaflight Configurator
2. **Receiver Tab**: Verify stick movements
3. **Modes Tab**:
   - **Arm**: SA↑ (1900-2000µs)
   - **Flight Modes**:
     - Angle: SB↑ (1900-2000µs)
     - Horizon: SB· (1400-1600µs)
     - Acro: SB↓ (1000-1100µs)
   - **Turtle Mode**: SD↑ (separate from flight modes)

---

## **🔄 5. Flight Modes Setup**  
| **Switch** | **Position** | **Function** | **Betaflight Range** |
|------------|--------------|--------------|----------------------|
| **SA**     | ↑            | Armed        | 1900-2000µs          |
| **SB**     | ↑            | Angle Mode   | 1900-2000µs          |
| **SB**     | ·            | Horizon Mode | 1400-1600µs          |
| **SB**     | ↓            | Acro Mode    | 1000-1100µs          |
| **SD**     | ↑            | Turtle Mode  | 1900-2000µs          |

---

## **💾 6. CLI Backup & Button Mapping**  
### **Creating a CLI Dump**
1. Connect to Betaflight → **CLI Tab**
2. Enter:
   ```
   dump
   ```
3. Save output to file:
   ```
   dump > meteor75pro_backup.txt
   ```
4. **Restore if needed**:
   ```
   paste entire dump
   save
   ```

### **Radiomaster Pocket Button Functions**
| **Button** | **Function**          | **Channel** |
|------------|-----------------------|-------------|
| SA         | Arm/Disarm            | CH5         |
| SB         | Flight Mode Selection | CH6         |
| SC         | Beeper/Telemetry      | CH7         |
| SD         | Turtle Mode           | CH8         |

---

## **🚁 7. Arming & Testing**  
1. Power drone with battery
2. **Arming Sequence**:
   - Throttle down (0%)
   - SA↑ → "Armed" voice confirmation
3. **Flight Mode Verification**:
   - SB↑: Self-leveling (Angle)
   - SB·: Limited acro (Horizon)
   - SB↓: Full manual (Acro)
4. **Emergency Features**:
   - SD↑: Activates Turtle Mode
   - SC: Triggers beeper for location

---

## **🔧 Troubleshooting**  
- **No Arming**: Check throttle position and failsafe settings
- **Mode Conflicts**: Ensure no overlapping µs ranges
- **Motor Issues**: Verify ESC calibration in BLHeli/Bluejay

**📢 Pro Tip:** Always perform a bench test before flying!