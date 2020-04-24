******************
IRoot (``0x0000``)
******************

.. table:: Table 1 - Functions

    == ====================== =======================================================
    ID          Name                               Description
    == ====================== =======================================================
     0 ``GetFeature``         Returns the feature index from a desired feature ID
     1 ``GetProtocolVersion`` Returns the protocol version / Replies to a ping action
    == ====================== =======================================================

.. table:: Table 2 - Events

    == =============== =====================================================================================
    ID       Name                                                Description
    == =============== =====================================================================================
     0 ``NoOperation`` This event may be sent by a device to prevent the device from entering low power mode
    == =============== =====================================================================================


Versions:
    - 1
        - Add ``featureVersion`` field to ``GetFeature``


Functions
=========


``GetFeature``
~~~~~~~~~~~~~~

Arguments
    - featureId

    .. table:: Table 3 - ``GetFeature`` argument parameters

        +-------------+-----------------+-----------------+
        |     byte    |        0        |        1        |
        +=============+=================+=================+
        | description | featureId (MSB) | featureId (LSB) |
        +-------------+-----------------+-----------------+

Return
    - featureIndex
    - featureType
        - obsolete
        - hidden
    - featureVersion
        The feature version always starts at 0. New versions may introduce New
        functionallity but must be backwards compatible.

    .. table:: Table 4 - ``GetFeature`` return parameters

        +-------------+--------------+----------------------------+----------------+
        |     byte    |       0      |              1             |        2       +
        +-------------+--------------+----------+--------+--------+----------------+
        |     bit     |              |     7    |    6   | 5 .. 0 |                |
        +=============+==============+==========+========+========+================+
        |             |              | obsolete | hidden |        |                |
        | description | featureIndex +----------+--------+--------+ featureVersion |
        |             |              |         featureType        |                |
        +-------------+--------------+----------------------------+----------------+

    .. table:: Table 5 - `obsolete` value

        ===== =================
        Value    Description
        ===== =================
          0   supported feature
          1    obsolete feature
        ===== =================

    .. table:: Table 6 - `hidden` value

        ===== =================
        Value    Description
        ===== =================
          0   supported feature
          1      hidden feature
        ===== =================


``GetProtocolVersion``
~~~~~~~~~~~~~~~~~~~~~~

Arguments
    - pingData (optional, you can ignore when getting the version)

    .. table:: Table 7 - ``GetProtocolVersion`` argument parameters

        +-------------+---+---+----------+
        |     byte    | 0 | 1 |     2    |
        +=============+===+===+==========+
        | description |   |   | pingData |
        +-------------+---+---+----------+

Return
    - protocolMajor
    - protocolMinor
    - pingData
        ``pingData`` returns the same value as specified in the arguments

    .. table:: Table 8 - ``GetProtocolVersion`` return parameters

        +-------------+---------------+---------------+----------+
        |     byte    |       0       |       1       |     2    |
        +=============+===============+===============+==========+
        | description | protocolMajor | protocolMinor | pingData |
        +-------------+---------------+---------------+----------+

    .. table:: Table 9 - `protocolMajor` value

        ===== ==========================
        Value        Description
        ===== ==========================
         0x8f HID++ 1.0
         0x02 HID++ 2.0 - legacy devices
         0x04 HID++ 2.0
        ===== ==========================


Events
======


``NoOperation``
~~~~~~~~~~~~~~~

This event may be sent by a device, as needed, to prevent the device from
entering low power mode. If sent following a HID response this may allow the
device to respond quicker to the next HID command. This event is a no-op and
contains no data. The host software can safely ignore this event.
