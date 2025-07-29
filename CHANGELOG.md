# CLIENT

## 1.13

* Including code to check for HTTP status of Github servers when looking for updates.

## 1.12

* Changed the way jitter is calculated: now it uses standard deviation.
* Small code refactoring.
* Updated ubuntu docker image.
  
## 1.11

* Introduction of new variable LAYER2, enabling the user to choose between layer2 and layer3 statistics. See documentation on Docker Hub for details.
* Small code refactoring.

## 1.10

* Small code refactoring to optimize string manipulation on the code introduced in version 1.9.
* Changed the algorithm for checking new software version.

## 1.9

* Introduction of new variable MQTT_PREFIX, allowing multiple instances to connect to the same MQTT server, and all the logic behind it.

## 1.8

* Change MQTT QoS priority of messages containing the results, from 0 to 2.

## 1.7

* Corrected small bug in the connection status verification code.

## 1.6

* Code refactoring to eliminate remaining global variables.
* Add check to avoid rare division by zero error.

## 1.5

* Changed some global variables for structs, improving safety.

## 1.4

* Changed the MQTT topic used to announce software updates.
* Changed MQTT client error message to be more informative.

## 1.3

* Added jitter test.
* Added sensor to report jitter results.

## 1.2

* Added routine to check for updates every 6 hours.
* Addes sensor to show software versions - installed and latest available

## 1.1

* Added remote host check for availability, to avoid printing one message error per worker in case of connection errors.
* Allow using host names when setting HOST_ADDR and MQTT_ADDR.
  
## 1.0

* Initial release.

# SERVER

## 1.1

* Improvements in handling connection errors.

## 1.0

* Initial release.
