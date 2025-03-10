![Project Logo](https://raw.githubusercontent.com/rpr1972/speedtest-mqtt/main/logo.png)
# Speed test integration with Home Assistant through MQTT

This image provides the ability to run speed tests and send results to Home Assistant via MQTT. It's important to note that this approach <u>does not</u> use **Okla's Speedtest** servers in any way: turns out "speed test" is a broad way of saying "network bandwidth test" and the name of a piece of sotware at the same time. So, this integration uses its own client and server. The downside of this is that you have to host the server yourself. But, considering the broad offer of cloud providers that have free layers of service, it should be no problem to host your own speed test server - in fact, this aproach will give you more accurate results, since (probably) you will be the only one using it.

The server image has two versions: AMD64 and ARM64(v7/v8). The ARM64 was built because there are some cloud providers offering ARM instances in their free layers, while charging for AMD64. Starting in July 2024, there is now an ARM64 version for the client too. This is specially useful for those running Home Assistant in Raspberry Pi. Now, they can run the client in pretty much any RPi3/4 with a 64 bit OS installed (32 bit OS is not supported).

That being said, let's go to the configuration part.

## Server

Configuration is done using environment variables passed to docker engine. They are as follow:

**BIND_ADDR**: The address to bind the server to. Usually is set to "0.0.0.0" to bind to all addresses available.

**BIND_PORT**: The port in which the server will listen for connections.

**TZ**: Your timezone.

## Client

Configuration is done the same way, using these variables:

**HOST_ADDR**: Speed test server IP address.

**HOST_PORT**: Speed test server TCP port.

**LAYER2**: Defines the method used to calculate the transfer rate. New in 1.11. True if and only if it's set to "true"; otherwise it would be false.

**MQTT_ADDR**: MQTT server IP address.

**MQTT_PREFIX**: Prefix used on MQTT topics. New in 1.9. If empty, defaults to "speedtest_mqtt".

**TZ**: Your timezone.

Once the client is started, it will create all necessary entities in Home Assistant: download speed, upload speed and latency. They are sensors, bound to a "virtual" device that exists only to group them together. Using the **MQTT_PREFIX** variable allows one to create multiple containers pointing to the same MQTT server without mixing the results. This is useful to test the speed of multiple links (e.g. fiber, cable, starlink etc). In this case, each container must have an unique prefix, like "speedtest_fiber" or "speedtest_starlink", for example.

The variable **LAYER2** defines the method used to calculate the transfer rate: if it's true, then the Ethernet frame headers will be included in the calculation; if it's false, only the IP and TCP headers will be used. This is usefull for having a more realistic value for the link capacity. Usually, ISPs set the bandwidth limits at the layer 2, and that makes any bandwidth test using layer 3 protocols report results below what the user expects. For example: if your bandwidth limits are 100 Mbps up/50 Mbps down, usually you'll get results below that for any bandwidth test (except if you ISP sets the limit a little bit higher to compensate the difference between layer 2 and 3 values OR if it has some kind of "burst" feature that give the user a little bit more than what is expected for a few seconds). So now the user can choose between layer 2 or 3 tests. Layer 2 will give you values a little bit higher, but usually matches what you are paying for.

## Running the tests

After setting both the client and the server, all interaction is done through the client, using the MQTT topic called "**speedtest_mqtt/control/set**". The payload will inform about the tests you want to run. There are three kinds of tests: "**download**", "**upload**" and "**rtt**". The payload format should be like this:

**TEST_NAME:NUMBER_OF_WORKERS:TEST_DURATION**

As an example, the payload could be:

**download:64:20,upload:8:20,rtt**

to run all three tests: the download test using 64 parallel workers for 20 seconds; the upload test using 8 parallel workers for 20 seconds and the rtt test (that has no parameters). Note that tests are separated by commas. It's possible to run only one or two tests. The number of workers has an artificial limit (just to avoid excessive use of memory and CPU) of 300. The test duration is in seconds and also has an artificial limit of 3600 (1 hour). As written above, the rtt test does not use any of these parameters. If no parameter is informed, the chosen test will use default values, which are 4 workers for 10 seconds.

So, using an MQTT topic to control the tests is useful in Home Assistant, since you can create an automation to publish an MQTT message to that topic.

## Logging

Both images create a log file in the container's "/log" directory. To have access to the logs, you will need to map this directory to the local filesystem.

## Docker image

To get the image, please go to https://hub.docker.com/repository/docker/robrosa/speedtest


