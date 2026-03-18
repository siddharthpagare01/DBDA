

# 🧪 Full SDN Setup: Mininet + OpenDaylight

## 🖥️ 0. Requirements

* Ubuntu 20.04 / 22.04 (VM or native)
* RAM: ≥ 4GB (important)
* Internet connection

---

# ⚙️ 1. Install Java (Required for ODL)

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
java -version
```

👉 Should show Java 11 ✔️

---

# ⚙️ 2. Install Mininet

```bash
sudo apt install mininet -y
```

Test:

```bash
sudo mn --test pingall
```

👉 If 0% loss → Mininet OK ✔️

---

# ⚙️ 3. Download OpenDaylight

```bash
cd ~
wget https://nexus.opendaylight.org/content/repositories/public/org/opendaylight/integration/karaf/0.12.3/karaf-0.12.3.tar.gz
tar -xvzf karaf-0.12.3.tar.gz
mv karaf-0.12.3 opendaylight
```

---

# ⚙️ 4. Start OpenDaylight

```bash
cd ~/opendaylight/bin
./karaf
```

⏳ Wait ~1–2 min

You should see:

```
opendaylight-user@root>
```

---

# ⚙️ 5. Install ODL Features (VERY IMPORTANT)

Inside karaf console:

```bash
feature:install odl-restconf
feature:install odl-l2switch-switch
feature:install odl-mdsal-apidocs
```
or 
```
feature:install odl-restconf
feature:install odl-openflowplugin-all
feature:install odl-openflowplugin-app-forwardingrules-manager
feature:install odl-openflowplugin-app-topology-lldp-discovery
feature:install odl-openflowplugin-southbound
feature:install odl-openflowplugin-flow-services
feature:install odl-openflowplugin-app-topology
```
👉 This enables:

* REST API
* Layer 2 switching
* Controller APIs

---

# ⚙️ 6. Verify Controller Port

Open new terminal:

```bash
netstat -tulnp | grep 6653
```

✔ Output should show port 6653 listening

---

# ⚙️ 7. Run Mininet with Remote Controller

```bash
sudo mn --topo single,2 --controller=remote,ip=127.0.0.1,port=6653
```

👉 Creates:

* 1 switch (s1)
* 2 hosts (h1, h2)

---

# ⚙️ 8. Test Network

Inside Mininet:

```bash
pingall
```

✔ Expected:

```
0% packet loss
```

---

# 🔍 9. Verify SDN Connection (VERY IMPORTANT)

```bash
sh ovs-vsctl show
```

👉 Look for:

```
Controller "tcp:127.0.0.1:6653"
is_connected: true
```

✔ This = REAL SDN working 🎯

---

# 🌐 10. Access OpenDaylight GUI

Open browser:

```
http://localhost:8181/index.html
```

Login:

```
username: admin
password: admin
```

👉 You can see:

* Topology
* Nodes
* Flow stats

---

# 🧠 What You Just Built

![Image](https://www.researchgate.net/publication/319354127/figure/fig1/AS%3A11431281112333430%401673384543203/Three-common-topologies-in-Mininet.jpg)

![Image](https://www.researchgate.net/publication/323925422/figure/fig1/AS%3A829365029986305%401574747440642/Simplified-SDN-Architecture-1.ppm)

![Image](https://www.researchgate.net/publication/339708791/figure/fig2/AS%3A865547906449413%401583374110900/A-software-defined-network-hosts-and-OpenFlow-switches-connected-in-a-linear-topology.png)

![Image](https://www.researchgate.net/publication/302595330/figure/fig3/AS%3A361026856996875%401463086918543/OpenFlow-based-single-topology-having-16-hosts.png)

👉 Flow:

* h1 → packet → switch
* switch → asks controller
* controller → installs flow
* communication happens

---

# ⚠️ Troubleshooting (Most Important)

## ❌ Error: Unable to contact controller

✔ Fix:

* Start ODL first
* Check port 6653

---

## ❌ is_connected: false

✔ Fix:

```bash
sudo mn -c
```

Restart everything

---

## ❌ GUI not opening

✔ Check:

* ODL running
* Port 8181 open

---

If something breaks during setup, just paste error — we’ll debug it step-by-step 👍
