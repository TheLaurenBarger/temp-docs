In this article, we’ll cover how to configure a Tasmota device for MQTT integration.

Before continuing, configuring MQTT on a Tasmota device will require a MQTT Broker:

[MQTT Configuration](MQTT-Configuration_6946817.html)

Related content.
----------------

*   [home-assistant](/wiki/label/home-assistant)
*   [mqtt](/wiki/label/mqtt)
*   [tasmota](/wiki/label/tasmota)

<!-- Table of Contents -->

MQTT configuration.
===================

In order to begin configuring MQTT, we first need to enable MQTT integration and then begin setting the integration parameters.

Enable MQTT.
------------

Navigate to **Configuration** → **Configuration Other** and set the **MQTT Enable** check box to true:

![](attachments/10846209/11436035.png?width=564)

![](attachments/10846209/11436041.jpeg?width=564)

Ensure that the MQTT Enable checkbox is set, and give your device a name that you can recognise.

Configure MQTT connection properties.
-------------------------------------

After enabling MQTT, navigate to **Configuration** → **Configure MQTT** to set the MQTT connection properties.

For details on how to configure a new user on your MQTT Broker, refer to the following:

[MQTT Configuration](MQTT-Configuration_6946817.html)

Enter in the following information:

*   **Host**: IP Address where your MQTT Broker is running
    
*   **Port**: MQTT Broker port (default: 1883)
    
*   **Client**: Device friendly name
    
*   **User**: Username configured on MQTT Broker for your device
    
*   **Password**: Password for MQTT user
    
*   **Topic**: The topic that the device will listen to.
    

### MQTT topic structure.

The MQTT topic naming convension will follow the following convension:

1.  Topics will be subdivided by forward slashes /
2.  Topics will be organised from macro identifiers to micro from left to right:
    
    1.  /bedroom/lamp/right/
        
    2.  /kitchen/light/
        
    3.  /laundrey/washingmachine/
        
3.  Topics will end with what is called a “prefix” in MQTT, but this prefix identifies the command that we’d like to execute on the device.
    
    1.  cmd: issue commands
        
    2.  stat: report back configuration or status
        
    3.  tele: telemetry info
        

Following these rules, the topic name selected for our test device will be: `/misc/sitbit/%prefix%/`

![](attachments/10846209/11468810.png?width=564)

![](attachments/10846209/11403302.png?width=564)

Test MQTT connection.
=====================

In order to test the MQTT connection properties, we will require a means of publishing to the configured topic and will need to enable logging on the tasmota device in order to view incoming messages.

Configure logging.
------------------

Navigate to **Configuration** → **Configure Logging** to set the logging settings. In order to view incoming messages, we need to set **Web log level** to **DEBUG**.

![](attachments/10846209/11534353.png?width=564)

![](attachments/10846209/11501597.png?width=564)

Publishing a message.
---------------------

For this test we will publish messages to the MQTT topic via home assistant since it is convenient.

For other publishing methods, have a look at:

*   Publishing via MQTT client: [MQTT Configuration](MQTT-Configuration_6946817.html)
    
*   Publishing via Tasmota Device Manager: [Tasmota: Device Manager](11567105.html)
    
*   Publishing via Home Assistant: [Home Assistant: MQTT Broker Integration](11632641.html)
    

For this test, the home assistant publishing settings look as follows:

![](attachments/10846209/11599880.png)

Once the message has been successfully published, you should be able to see the incoming message in the Console of the tasmota device:

![](attachments/10846209/11534359.png)

Conclusion.
===========

In this article, we’ve discussed how to configure MQTT integration on a Tasmota device and test the integration.

References.
===========

1. [MQTT Configuration](MQTT-Configuration_6946817.html)
2. [https://tasmota.github.io/docs/MQTT/](https://tasmota.github.io/docs/MQTT/)