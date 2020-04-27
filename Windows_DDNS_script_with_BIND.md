# Windows DDNS script with BIND

## About this script

This script provides DDNS function to Windows DR cluster with BIND DNS server.

With DDNS function, a client machine can access the active node with a same hostname before and after failover.

## System Requirement

- 2 Windows Server
  - OS: Windows Server
    - Evaluated version: Windows Server 2019
  - EXPRESSCLUSTER has been installed.
- 1 DNS server
  - OS: Linux
    - Evaluated version: CentOS 7.
  - BIND 9 has been installed.

  ```
  e.g. 

  (Internet)
  |
  |      +--------------------------------+
  |      | Cluster node 1 (Windows Server)|
  |------+     EXPRESSCLUSTER             |
  |      |   IP: 192.168.10.1/24          |
  |      +--------------------------------+
  ~
  |      +--------------------------------+
  |      | Cluster node 2 (Windows Server)|
  |------+     EXPRESSCLUSTER             |
  |      |   IP: 192.168.20.1/24          |
  |      +--------------------------------+
  ~
  |      +-----------------------+
  |      | DNS server (Linux)    |
  |------+     BIND              |
  |      |   IP: 192.168.30.1/24 |
  |      +-----------------------+
  |
  ```

## How to use the script

1. Download the zip file required for DDNS function
  
    BIND 9.16.2 was evaluated for the solution.
    - File Name: BIND9.16.2.x64.zip

      https://www.isc.org/download/

2. Unzip the zip file that you downloaded in step 1, and copy the following files to both servers
    
    Required files
    - BIND9.16.2.x64.zip
      - libbind9.dll
      - libcrypto-1-1-x64.dll
      - libdns.dll
      - libirs.dll
      - libisc.dll
      - libisccfg.dll
      - libuv.dll
      - libxml2.dll
      - nsupdate.exe
    - Files available on GitHub
      - nsupdate_add.txt
      - nsupdate_delete.txt

    Destination path
    - C:\Program Files\EXPRESSCLUSTER\bin

3. Edit nsupdate_add.txt in bin folder of **both** servers
    
    You need to edit the following parameteres.
    - DNS_HOSTNAME: Hostname of DNS server
    - ZONE: Zone name
    - VIRTUAL_HOSTNAME: Virtual host name that you want to use for the cluster
    - TTL: TTL of DNS record
    - IP_ADDRESS: IP address of the cluster node

    ```
    e.g. 

    server centos-7-server.my.net
    zone my.net
    update delete cluster.my.net
    update add cluster.my.net 60 IN A 192.168.145.1
    send
    ```

4. Edit nsupdate_delete.txt in bin folder of both servers

    You need to edit the following parameteres.
    - DNS_HOSTNAME: Hostname of DNS server
    - ZONE: Zone name
    - VIRTUAL_HOSTNAME: Virtual host name that you want to use for the cluster

    ```
    e.g. 

    server centos-7-server.my.net
    zone my.net
    update delete cluster.my.net
    send
    ```

5. Create script resource on Cluster WebUI

    - Please replace start.bat and stop.bat
    - Please set appropriate dependency
      - e.g. Dependency with other resources for applications or databases.