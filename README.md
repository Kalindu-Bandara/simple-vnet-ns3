# 🚗 VANETs Simulation Using NS-3.45, SUMO v1\_24\_0 \& TraCI on WSL2







Simulation framework for Vehicular Ad-hoc Networks (VANETs) using \*\*NS-3.45\*\* for network simulation, \*\*SUMO v1\_24\_0\*\* for realistic traffic modeling, and \*\*OpenStreetMap\*\* for real-world environments. Executed on WSL2 (Ubuntu 24.04) with TraCI integration.



## 🔧 Software Requirements

| Component        | Version           | Notes                          |

|------------------|-------------------|--------------------------------|

| NS-3             | 3.45              | Network simulator              |

| SUMO             | v1\_24\_0           | Traffic simulation             |

| Python           | 3.10+             | For TraCI interface           |

| OS               | WSL2 - Ubuntu 24.04| Windows Subsystem for Linux 2 |

| NetAnim          | 3.108+            | Optional visualization        |



---



## 🚀 Quick Start



### 1. Install Dependencies



# Install SUMO dependencies

sudo apt update \&\& sudo apt install -y g++ python3 cmake ninja-build git \\

libgdal-dev libproj-dev libxerces-c-dev libfox-1.6-dev \\

libgdk-pixbuf2.0-dev libgl2ps-dev python3-pyproj python3-matplotlib



# Install NS-3 dependencies

sudo apt install -y gcc g++ python3 python3-dev python3-setuptools \\

python3-pip qt5-default mercurial



#Install SUMO



git clone --branch v1\_24\_0 https://github.com/eclipse/sumo.git

cd sumo

mkdir build \&\& cd build

cmake ..

make -j$(nproc)

sudo make install



#Install NS-3.45



cd ~

wget https://www.nsnam.org/releases/ns-allinone-3.45.tar.bz2

tar -xjf ns-allinone-3.45.tar.bz2

cd ns-allinone-3.45/ns-3.45

./waf configure --enable-examples --enable-tests

./waf build



##🗺️ Mobility Generation Workflow



###1. Generate Scenario



cd ~/sumo/tools

python3 osmWebWizard.py



\*Select map area in browser interface



\*Choose vehicle types (cars, buses, motorcycles)



\*Click "Generate Scenario"



\*Files generated: osm.net.xml, osm.poly.xml, osm.sumocfg, osm.rou.xml



###Generate NS-2 Mobility Trace



sumo -c osm.sumocfg --fcd-output trace.xml

python3 ~/sumo/tools/traceExporter.py -i trace.xml --ns2mobility-output=mobility.tcl

mv mobility.tcl ~/



##📡Running VANET Simulation



cd ~/ns-allinone-3.45/ns-3.45

./waf --run "scratch/ns2-mobility-trace \\

--traceFile=/home/$USER/mobility.tcl \\

--nodeNum=$(grep 'node\_' ~/mobility.tcl | wc -l) \\

--duration=100.0 \\

--logFile=ns2-mob.log"



## ⚙️ Simulation Parameters



| Parameter     | Description                | Example Value              |

|---------------|----------------------------|---------------------------|

| `--traceFile` | Path to mobility trace file | `/home/user/mobility.tcl` |

| `--nodeNum`   | Number of vehicles          | `237` (auto-detected)     |

| `--duration`  | Simulation time in seconds  | `100.0`                   |

| `--logFile`   | Output log filename         | `ns2-mob.log`             |





## 📊 Visualization (Optional)



### 1. Enable NetAnim in Code



Add to your NS-3 script (`scratch/ns2-mobility-trace.cc`):



```cpp

\#include "ns3/netanim-module.h"



// Before Simulator::Run();

AnimationInterface anim("vehicularmobility.xml");



### 2. Run NetAnim



cd ~/ns-allinone-3.45/netanim-3.108

./NetAnim



Load the generated  vehicularmobility.xml file in NetAnim



## 📂 Project Structure



├── sumo/                     # SUMO installation

├── ns-allinone-3.45/         # NS-3 installation

├── mobility.tcl              # Generated vehicle mobility trace

├── osm.sumocfg               # SUMO configuration file

├── trace.xml                 # Raw mobility trace file from SUMO

├── vehicularmobility.xml     # NetAnim visualization XML file

└── ns2-mob.log               # NS-3 simulation log file



## ⚠️ Important Notes



\- \*\*Node Count Verification:\*\*  

&nbsp; Always verify that the node count in your NS-3 simulation matches the trace file:  

&nbsp; ```bash

&nbsp; grep 'node\_' mobility.tcl | wc -l





&nbsp;  SUMO-NS3 Version Compatibility:

&nbsp;  Tested specifically with SUMO v1\_24\_0 and NS-3.45.



&nbsp;  WSL2 Limitations:

&nbsp;  GUI applications like SUMO-GUI and NetAnim work best if you install an XServer (e.g.,        Xming) on your Windows host.



📚 References

NS-3 Official Documentation



SUMO Documentation



TraCI Interface Guide



NetAnim Visualization Tool





