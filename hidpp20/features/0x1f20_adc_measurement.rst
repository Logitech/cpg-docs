***********************************
Headset ADC measurement (``x1f20``)
***********************************

Version 3

AdcMeasurement
==============

This feature is an ad-hoc feature for the G533 product.
It provides a function to perform an ADC measurement and return the
result in ADC counts.

Functions
---------

- getAdcMeasurement

Events
------

- statusEvent

Overview
========

The feature provides a function to perform ADC measurements. It returns a 16-bit value in ADC counts. It is assumed the software already knows the resolution and sign of the ADC.

The ``entityIdx`` parameter selects the ADC entity for the ADC measurement to be performed. The ``entityIdx`` is a value that maps to a hardware specific ADC measurement unit.

The feature also gives information on the wireless connection state and of the battery charging state.

Functions
=========

``getAdcMeasurement``
---------------------

``getAdcMeasurement`` (``entityIdx``) **->** ``adcMeasurement`` **,** ``isConnected`` **,** ``isCharging``

The function returns the current ADC measurement for the selected entity. It is assumed the host software is aware of the resolution of the ADC and its sign and the number of available entities and their functionnality.

For example there could be only one entity (entityIdx is 0), it measures the battery voltage and it returns a 12-bit unsigned ADC counts value.

Parameters
~~~~~~~~~~

- ``entityIdx`` The index of the entity to be measured. The range of allowed value is 0-15 (the upper 4-bit are reserved with all bits to 0).

.. table:: Table 1 - ``getAdcMeasurement()`` request packet format

    +----------+-------+-------+-------+-------+-------+-------+-------+-------+
    |byte / bit|   7   |   6   |   5   |   4   |   3   |   2   |   1   |   0   |
    +==========+=======+=======+=======+=======+=======+=======+=======+=======+
    |   0 / 4+ |       ``reserved (4+)``       |         ``entityIdx``         |
    +----------+-------------------------------+-------------------------------+

Returns
~~~~~~~

* ``adcMeasurement`` The ADC measurement in ADC counts. If 'isConnected' is 0, this value must be ignored.

* ``isConnected`` [1-bit] A boolean value that indicates the state of the wireless connection.

  * 0 = not connected
  * 1 = connected

* ``isCharging`` [1-bit] A boolean value that indicates if the battery is charging.

  * 0 = not charging
  * 1 = charging

* ``chargingComplete`` [1-bit] A boolean value that indicates if the charging is complete. Only valid if 'isCharging' is 1.

  * 0 = charging not complete
  * 1 = charging is complete

* ``chargingFault`` [1-bit] A boolean value indicating that charging has stopped due to an exception. Only valid if 'isCharging' is 1.

  * 0 = no fault
  * 1 = fault

The possible states are:

* charger not attached, device is not connected (isConnected = 0, isCharging = 0)

* charger not attached, device is connected (isConnected = 1, isCharging = 0)

* charger attached, device is not connected (isConnected = 0, isCharging = 1)

* charger attached, device is connected (isConnected = 1, isCharging = 1)

* charger attached, not fully charged (chargingFault = 0. chargingComplete = 0, isCharging = 1)

* charger attached, fully charged (chargingFault = 0. chargingComplete = 1, isCharging = 1)

* charger attached, charging fault (chargingFault = 1. chargingComplete = 0, isCharging = 1)

.. table:: Table 2 - ``getAdcMeasurement()`` response packet

    +----------+-------+-------+-------+-------+-------------+----------------+----------+-----------+
    |byte / bit|   7   |   6   |   5   |   4   |      3      |        2       |     1    |     0     |
    +==========+=======+=======+=======+=======+=============+================+==========+===========+
    |   0 / 8+ |                     adcMeasurement (MSB)                                            |
    +----------+-------------------------------------------------------------------------------------+
    |   1 / 8+ |                     adcMeasurement (LSB)                                            |
    +----------+-------------------------------+-------------+----------------+----------+-----------+
    |   2 / 4+ |   reserved                    |chargingFault|chargingComplete|isCharging|isConnected|
    +----------+-------------------------------+-------------+----------------+----------+-----------+


Events
======

``statusEvent``
---------------

``statusEvent`` **->** ``adcMeasurement`` **,** ``isConnected`` **,** ``isCharging``

Sent when the connection or charging state has changed.

* ``adcMeasurement`` The ADC measurement in ADC counts. If 'isConnected' is 0, this value must be ignored.

* ``isConnected`` [1-bit] A boolean value that indicates the state of the wireless connection.

  * 0 = not connected
  * 1 = connected

* ``isCharging`` [1-bit] A boolean value that indicates if the battery is charging.

  * 0 = not charging
  * 1 = charging

* ``chargingComplete`` [1-bit] A boolean value that indicates if the charging is complete. Only valid if 'isCharging' is 1.

  * 0 = charging not complete
  * 1 = charging is complete

* ``chargingFault`` [1-bit] A boolean value indicating that charging has stopped due to an exception. Only valid if 'isCharging' is 1.

  * 0 = no fault
  * 1 = fault

.. table:: Table 3 - ``statusEvent()`` event packet

    +----------+-------+-------+-------+-------+-------------+----------------+----------+-----------+
    |byte / bit|   7   |   6   |   5   |   4   |      3      |        2       |     1    |     0     |
    +==========+=======+=======+=======+=======+=============+================+==========+===========+
    |   0 / 8+ |                     adcMeasurement (MSB)                                            |
    +----------+-------------------------------------------------------------------------------------+
    |   1 / 8+ |                     adcMeasurement (LSB)                                            |
    +----------+-------------------------------+-------------+----------------+----------+-----------+
    |   2 / 4+ |   reserved                    |chargingFault|chargingComplete|isCharging|isConnected|
    +----------+-------------------------------+-------------+----------------+----------+-----------+

Changelog
=========

* Version 3: Added 'chargingComplete' and 'chargingFault' states
* Version 1: Added 'isConnected' and 'isCharging' to getAdcMeasurement(). Added event 'statusEvent'
* Version 0: Initial version
