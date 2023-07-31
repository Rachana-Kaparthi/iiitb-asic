Day0
Yosys
I installed Yosys using following commands
 git clone https://github.com/YosysHQ/yosys.git
$ cd yosys-master 
$ sudo apt install make (If make is not installed please install it) 
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make config-gcc
$ make 
$ sudo make install
Below are the screenshots of successful launch
![Screenshot from 2023-07-31 09-44-43](https://github.com/Rachanaka/iiitb-asic/assets/140998470/2c6eaf8d-c891-4e21-a99d-d10c3e7bdc7e)

