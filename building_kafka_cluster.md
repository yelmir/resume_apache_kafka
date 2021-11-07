To follow along with this course and practice the concepts we cover, you will need your own Kafka cluster. This lesson will guide
you through the process of setting up your own three-broker Kafka cluster using Confluent Community.

Relevant Documentation

Confluent Manual Install using Systemd on Ubuntu and Debian

Lesson Reference

1. On all three nodes, add the GPG key and package repository, then install Confluent and Java:
```bash
wget -qO - https://packages.confluent.io/deb/5.2/archive.key | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/5.2 stable main"
sudo apt-get update && sudo apt-get install -y openjdk-8-jdk confluent-community-2.12
```
2. On all three nodes, edit the hosts file:
```bash
sudo vi /etc/hosts
```
3. Add the following to the hosts file on all three servers. Use the private IP addresses of your three servers (you can find
them in Cloud Playground):
```bash
<server 1 private IP> zoo1
<server 2 private IP> zoo2
<server 3 private IP> zoo3
```
4. Save and exit the file on each server.

5. On each server, edit the Zookeeper config file:
```bash
sudo vi /etc/kafka/zookeeper.properties
```
6. Delete the contents of the config file, and add the following:
```bash
tickTime=2000
dataDir=/var/lib/zookeeper/
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
autopurge.snapRetainCount=3
autopurge.purgeInterval=24
```
7. Save and exit the file on each server.

8. Set the Zookeeper ID for each server:
```bash
sudo vi /var/lib/zookeeper/myid
```
9. On each server, set the contents of /var/lib/zookeeper/myid to the server's ID:
```bash
On Server 1, enter 1 .
On Server 2, enter 2 .
On Server 3, enter 3 .
```
10. Save and exit the file on each server.

11. On each server, edit the Kafka config file:
```bash
sudo vi /etc/kafka/server.properties
```
Now, we need to edit the broker.id , advertised.listeners , and zookeeper.connect in the config file.

12. Set broker.id to the appropriate ID for each server ( 1 on Server 1, 2 on Server 2, and 3 on Server 3).

13. For advertised.listeners , un-comment the line and change your.host.name to the hostname for each server —
zoo1 , zoo2 , or zoo3 — as appropriate.

14. In each config file, set zookeeper.connect to zoo1:2181 .

15. On each server, start and enable the Zookeeper service:
```bash
sudo systemctl start confluent-zookeeper
sudo systemctl enable confluent-zookeeper
```
16. Wait a few seconds, and then do the same for the Kafka service on each server:
```bash
sudo systemctl start confluent-kafka
sudo systemctl enable confluent-kafka
```
17. Check the services on each server to make sure they are running. Both services should be active (running) on all three
servers:
```bash
sudo systemctl status confluent*
```
They should both be in the active (running) state on all servers.

18. Test your cluster by listing the current topics:
```bash
kafka-topics --list --bootstrap-server localhost:9092
```
Since you have not created any topics yet, you will only see a default topic. The output should look like this:
__confluent.support.metrics
