# Mqtt
I have listed the detailed process of implementing mqtt with tls connection.

Installing the MQTT broker
When installing the Mosquitto broker on Ubuntu, you have the option of using either snap or apt package manager.
If you use snap it is good to know that the default location of the mosquitto.conf file is here:

/var/snap/mosquitto/common/mosquitto.conf

I am using apt package manager, so I type the following:

sudo apt-get install mosquitto
sudo apt-get install mosquitto-clients

If user want to run it as a demon, so type the following:

sudo systemctl start mosquitto.service

Open two terminals and type the following in the first terminal:

$ mosquitto_sub -h localhost -t “#” -d

You will see this process in the terminal
Client mosqsub|14677-ps sending CONNECT
Client mosqsub|14677-ps received CONNACK
Client mosqsub|14677-ps sending SUBSCRIBE (Mid: 1, Topic: #, QoS: 0)
Client mosqsub|14677-ps received SUBACK
Subscribed (mid: 1): 0
Client mosqsub|14677-ps received PUBLISH (d0, q0, r0, m0, ‘test’, … (5 bytes))
hello
Client mosqsub|14677-ps sending PINGREQ

And in the other terminal type:

$ mosquitto_pub -h localhost -t "test" -m "hello" -d

You will see this process in the terminal
Client mosqpub|14678-ps sending CONNECT
Client mosqpub|14678-ps received CONNACK
Client mosqpub|14678-ps sending PUBLISH (d0, q0, r0, m1, 'test', ... (5 bytes))
Client mosqpub|14678-ps sending DISCONNECT

Creating self-signed certificates (TLS) for mosquitto :- Use the above script for creating self-signed certificates.

After creating them ,we use the following mosquitto.conf; it is located in /etc/mosquitto/ 
I have attached the config file in the repo.

Restart mosquitto after modifying mosquitto.conf.

sudo systemctl stop mosquitto.service
sudo systemctl start mosquitto.service

Tail the log file in a terminal window.

$ sudo tail -f /var/log/mosquitto/mosquitto.log

Now it is time to use mosquitto_sub again.

Enter your respective ip address

mosquitto_sub --cafile ca.crt -h 192.xx.x.xx -t "#" -p 8883 -d --cert client.crt --key client.key

The terminal window that you tailed the log file will look like this:

$ sudo tail -f /var/log/mosquitto/mosquitto.log 

1556717194: mosquitto version 1.4.15 (build date) starting
1556717194: Config loaded from /etc/mosquitto/mosquitto.conf.
1556717194: Opening ipv4 listen socket on port 8883.
1556717194: Opening ipv6 listen socket on port 8883.
1556717195: New connection from 192.xx.x.xx on port 8883.
1556717195: New client connected from 192.xxx.x.xx as mosqsub|6284-sudi-vm (c1, k60, u’192.xxx.x.xx').
1556717417: New connection from 192.xxx.x.xx on port 8883.

Now it is time to use mosquitto_pub again.

$ mosquitto_pub -h 192.xxx.x.xx -t "test" -m "hello" -p 8883 -d --cert

Just as before we see the "hello world" message in the terminal where we are running mosquitto_sub.

$ mosquitto_sub --cafile ca.crt -h 192.xxx.x.xx -t "#" -p 8883 -d --cert client.crt --key client.key

Client mosqsub|8746-sudi sending CONNECT
Client mosqsub|8746-sudi received CONNACK
Client mosqsub|8746-sudi sending SUBSCRIBE (Mid: 1, Topic: #, QoS: 0)
Client mosqsub|8746-sudi received SUBACK
Subscribed (mid: 1): 0
Client mosqsub|8746-sudi sending PINGREQ
Client mosqsub|8746-sudi received PINGRESP
Client mosqsub|8746-sudi received PUBLISH (d0, q0, r0, m0, 'test', ... (5 bytes))
hello world

Now the communication is encrypted using TLS.

