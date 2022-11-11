# Home Automation, Part X: MQTT

“MQTT is a standard messaging protocol for the Internet of Things (IoT). It is designed as an extremely lightweight publish/subscribe messaging transport that is ideal for connecting remote devices with a small code footprint and minimal network bandwidth.” [https://mqtt.org](https://mqtt.org)

This article will serve as a guide on how to configure MQTT brokers and clients.

<!-- Table of Contents -->

## MQTT Broker

The MQTT broker will act as the hub through which all our messages are routed. I’ll be creating a Mosquitto MQTT Broker via docker compose.

### Mosquitto MQTT Broker Container

Add the following container details to your docker-compose.yaml:

```yaml
  mosquitto:
    image: eclipse-mosquitto
    container_name: mosquitto
    volumes:
      - /opt/mosquitto:/mosquitto
    ports:
      - 1883:1883
      - 9001:9001
    restart: unless-stopped
```

Then run the following to bring up the broker container:

```bash
docker-compose up -d
```

Once your container has been created, navigate to your mounted mosquitto directory (**/opt/mosquitto/**), open the config folder and update the created **mosquitto.conf** file.

We want to setup basic client authentication when communicating to our MQTT broker. Ensure that your mosquitto.conf file is using the **/mosquitto/config/password.txt** file and **allow\_anonymous** is set to false.

```bash
persistence true
persistence_location /mosquitto/data/

log_dest file /mosquitto/log/mosquitto.log
listener 1883

## Authentication ##
allow_anonymous false
password_file /mosquitto/config/password.txt
```

If you get a permission denied error while trying to save the file, run the following command:

```bash
sudo chown <username> -R /opt
```

Restart the docker container for the changes to take affect.

## MQTT Client

Since we’ve enabled basic authentication requirements on our MQTT broker, we need to register new users and their passwords on the MQTT broker.

Open a shell terminal on your Mosquitto MQTT Broker container:

```bash
docker exec -it mosquitto sh
```

The command for creating a new mosquitto basic auth pair differs for the first user to be created on the system. Both commands will create a new entry in the password.txt file with the username and hashed password.

### First user creation

Execute the following command to create a new password file, or if it does exist, overrite it:

```bash
mosquitto_passwd -c /mosquitto/config/password.txt <user_name>
```

You will then be prompted to enter in the password for your new user.

### Secondary user creation

Execute the following command if your password file already exists and has users in it:

```bash
mosquitto_passwd /mosquitto/config/password.txt <user_name>
```

You will then be prompted to enter in the password for your new user.

## Testing client connection

In order to perform a preliminary test to see if your broker and user has been set up correctly, you can download the mosquitto client tools to test publishing and subscribing to your MQTT broker.

1. Download the mosquitto client tools: [Mosquitto Client Tools](https://mosquitto.org/download/).
2. Run the installation.
3. We’ll use the mosquitto commands via terminal. There are two ways to do this.
    1. Navigate to the installation directory (`C:\Program Files\mosquitto`) and open a bash terminal (I religeously use git bash)
    2. Add the installation directory to your path so that you can run the commands from anywhere. This requires a restart of your pc.

Run the following commands to test either publishing or subscribing to messages on your MQTT broker:

**Publish:**

```bash
./mosquitto_pub -h <mosquitto-container-ip> -t <topic-name> -m <message> -u <username> -P <password>
```

```bash
./mosquitto_pub -h 192.168.0.10 -t desktop.test -m "testing..." -u home_assistant -P donvoranadmo
```

**Subscribe:**

```bash
./mosquitto_sub -h <mosquitto-container-ip> -t <topic-name> -u <username> -P <password>
```

```bash
./mosquitto_sub -h 192.168.0.10 -t home-assistant.test -u home_assistant -P donvoranadmo
```

## Conclusion

This article has demonstrated how to configure and test an MQTT broker and client connections.

## References

1. [https://mqtt.org](https://mqtt.org)
2. [https://www.homeautomationguy.io/docker-tips/configuring-the-mosquitto-mqtt-docker-container-for-use-with-home-assistant/](https://www.homeautomationguy.io/docker-tips/configuring-the-mosquitto-mqtt-docker-container-for-use-with-home-assistant/)
3. [https://stackoverflow.com/questions/30172605/how-do-i-get-into-a-docker-containers-shell](https://stackoverflow.com/questions/30172605/how-do-i-get-into-a-docker-containers-shell)
4. [https://mosquitto.org/man/mosquitto\_passwd-1.html](https://mosquitto.org/man/mosquitto_passwd-1.html)
5. [Mosquitto Client Tools](https://mosquitto.org/download/)
6. [http://www.steves-internet-guide.com/mosquitto\_pub-sub-clients/](http://www.steves-internet-guide.com/mosquitto_pub-sub-clients/)
