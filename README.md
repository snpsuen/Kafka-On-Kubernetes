# Kafka-On-Kubernetes
The repo contains the K8s manifest files required to set up a Kafka cluster in the Kubernetes framework:
* 2 x Zookeeper servers + 2 x Kafka brokers
* Using the latest docker images, https://hub.docker.com/_/zookeepe and https://hub.docker.com/r/confluentinc/cp-kafka/

We hope to provide a gold image for running Kafka on Kubernetes with least complexity or based on lowst common denominator.
<p>
0. Prelim or prep work <br>
&nbsp;&nbsp; 0.1 &nbsp;&nbsp; Set the ownership of /var/tmp/kafka01/data and /var/tmp/kafka02/data recursively to uid 1000, gid 1000 on the K8s nodes. <br>
&nbsp;&nbsp; 0.2 &nbsp;&nbsp; Install MetalLB, https://metallb.universe.tf/installation/
<p>
1. Deploy Zookeeper servers zookeep01 and zookeep02 on K8s <br>
&nbsp;&nbsp; 1.1 &nbsp;&nbsp; kubernetes apply -f zookeeper-deploy.yaml <br>
&nbsp;&nbsp; 1.2 &nbsp;&nbsp; kubernetes apply -f zookeeper-service.yaml <br>
<p>
2. Deploy Kafka brokers kafka01 and kafka02 on K8s <br>
&nbsp;&nbsp; 2.1 &nbsp;&nbsp; kubernetes apply -f kafka-deploy.yaml <br>
&nbsp;&nbsp; 2.2 &nbsp;&nbsp; kubernetes apply -f kafka-service.yaml <br>
<p>
3. Testing with an example topic, mytest <br>
&nbsp;&nbsp; 3.1 &nbsp;&nbsp; kubectl run kafka-console --rm -i --tty --image confluentinc/cp-kafka -- bash <br>
&nbsp;&nbsp; 3.2 &nbsp;&nbsp; [kafka-console] kafka-topics --create --bootstrap-server kafka01:9092 --replication-factor 1 --partitions 1 --topic mytest <br>
&nbsp;&nbsp; 3.3 &nbsp;&nbsp; [kafka-console] kafka-console-producer --bootstrap-server kafka01:9092 --topic mytest <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; > Hello, world <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; > This is a test <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; > Thanks a lot! <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; > Control-C <br>
&nbsp;&nbsp; 3.3 &nbsp;&nbsp; [kafka-console] kafka-console-consumer --bootstrap-server kafka01:9092 --topic mytest --from-beginning <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Hello, world <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; This is a test <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Thanks a lot! <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Control-C <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Processed a total of 3 messages <br>
<br>
Sample output from Katacoda:
<pre>
controlplane $ kubectl run kafka-console --rm -i --tty --image confluentinc/cp-kafka -- bash
If you don't see a command prompt, try pressing enter.
[appuser@kafka-console ~]$ kafka-console-producer --bootstrap-server kafka01:9092 --topic mytest
>Hello, world
>This is my test message
>Thanks a lot!
[appuser@kafka-console ~]$ kafka-console-consumer --bootstrap-server kafka01:9092 --topic mytest --from-beginning
Hello, world
This is my test message
Thanks a lot!
^CProcessed a total of 3 messages
[appuser@kafka-console ~]$
</pre>
   
