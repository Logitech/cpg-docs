HID++ 2.0
=========

HID++ 2.0 is a self descriptive protocol built on top of the USB HID
specification. The goal is to be able to interact with the device
without having any knowledge about it beforehand.

Packet Structure
~~~~~~~~~~~~~~~~


.. table:: Table 1 - Packet structure

    +-------------+-----------+--------------+---------------+------------------------+------------+
    |     byte    |     0     |       1      |       2       |            3           |  4 .. End  |
    +-------------+-----------+--------------+---------------+----------+-------------+------------+
    |     bit     |                                          |  15 .. 8 |    7 .. 0   |            |
    +=============+===========+==============+===============+==========+=============+============+
    | description | Report ID | Device Index | Feature Index | Function | Software ID | Parameters |
    +-------------+-----------+--------------+---------------+----------+-------------+------------+


Report ID
    The first byte of the report as defined in the HID specification. Supported
    report types by the device can be found in the HID report descriptor.

    There following report types are supported:

    .. table:: Table 2 - Supported report types

        =========== ======== ========
        Report Type   Value   Lenght
        =========== ======== ========
           Short    ``0x10``  7 bytes
           Long     ``0x11`` 20 bytes
        =========== ======== ========

Device Index
    Target device index. ``0xff`` is reserved for corded devices and receivers.

Feature Index
    Each device has a feature table. It resolves the feature ID (see below) to
    the internal feature index. The IRoot feature is **required** to be present
    and have the index **0**.

    To read the feature table, you can use the ``GetFeature`` function from the
    |IRoot|_ feature.

    .. table:: Table 3 - Example device feature table

        =============================== =====
                   Feature ID           Index
        =============================== =====
        ``0x0000`` **(IRoot)**          **0**
        ``0x0002`` (IFeatureInfo)         1
        ``0x0003`` (Device Information)   2
        ``0x40a0`` (Fn Inversion)         3
        =============================== =====

Function
    Each HID++ feature can define a list of functions. To access a function you
    send a request with the desired feature index and the function ID.

Software ID
    This ID is used in case multiple applications want to interact with the
    device. When you send a request with a software ID, the reponse's software
    ID will have the same value. You can use it to make sure a reply is meant
    for you.

Parameters
    The parameters hold arbitrary data to interact with the functions. For
    requests they hold function arguments, and for responses, they hold return
    values.


.. table:: Table 4 - Short packet structure

    +-------------+------+--------------+---------------+------------------------+----+---+---+
    |     byte    |   0  |       1      |       2       |            3           |  4 | 5 | 6 |
    +-------------+------+--------------+---------------+----------+-------------+----+---+---+
    |     bit     |                                     |  15 .. 8 |    7 .. 0   |            |
    +=============+======+==============+===============+==========+=============+============+
    | description | 0x10 | Device Index | Feature Index | Function | Software ID | Parameters |
    +-------------+------+--------------+---------------+----------+-------------+------------+


.. table:: Table 5 - Long packet structure

    +-------------+------+--------------+---------------+------------------------+---+---+---+---+---+---+----+----+----+----+----+----+----+----+----+----+
    |     byte    |   0  |       1      |       2       |            3           | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 |
    +-------------+------+--------------+---------------+----------+-------------+---+---+---+---+---+---+----+----+----+----+----+----+----+----+----+----+
    |     bit     |                                     |  15 .. 8 |    7 .. 0   |                                                                         |
    +=============+======+==============+===============+==========+=============+=========================================================================+
    | description | 0x11 | Device Index | Feature Index | Function | Software ID |                                Parameters                               |
    +-------------+------+--------------+---------------+----------+-------------+-------------------------------------------------------------------------+


Feature List
~~~~~~~~~~~~

00 - Important
    - ``0x0000`` IRoot
    - ``0x0001`` IFeatureSet
    - ``0x0002`` IFeatureInfo

01 - Common
    - ``0x0003`` Device Information
    - ``0x0004`` Unit ID
    - ``0x0005`` Device Name and Type
    - ``0x0006`` Device Groups
    - ``0x0007`` Device Friendly Name
    - ``0x0008`` Keep-Alive
    - ``0x0020`` Config Change
    - ``0x0021`` 32-byte Unique Random Identifier
    - ``0x0030`` Target Software
    - ``0x0080`` Wireless Signal Strength
    - ``0x00c0`` DFU Control Legacy
    - ``0x00c1`` DFU Control Unsigned
    - ``0x00c2`` DFU Control Signed
    - ``0x00d0`` DFU
    - ``0x1000`` Battery Unified Level Status
    - ``0x1001`` Battery Voltage
    - ``0x1010`` Charging Control
    - ``0x1814`` Change Host
    - ``0x1981`` Backlight 1
    - ``0x1982`` Backlight 2
    - ``0x1a00`` PresenterControl
    - ``0x1b00`` Keyboard reprogrammable keys and Mouse buttons 1
    - ``0x1b01`` Keyboard reprogrammable Keys and Mouse buttons 2
    - ``0x1b02`` Keyboard reprogrammable Keys and Mouse buttons 3
    - ``0x1b03`` Keyboard reprogrammable Keys and Mouse buttons 4
    - ``0x1b04`` Keyboard reprogrammable Keys and Mouse buttons 5
    - ``0x1bc0`` Report HID Usages
    - ``0x1c00`` Persistent Remappable Action
    - ``0x1d4b`` Wireless Device Status
    - ``0x1df0`` Remaining Pairings

02 - Mouse
    - ``0x2001`` Swap left/right button
    - ``0x2005`` Button Swap Cancel
    - ``0x2006`` Pointer Axes Orientation
    - ``0x2100`` Vertical Scrolling
    - ``0x2110`` SmartShift wheel
    - ``0x2120`` High-Resolution Scrolling
    - ``0x2121`` HiRes Wheel
    - ``0x2130`` Ratchet Wheel
    - ``0x2150`` Thumbwheel
    - ``0x2200`` Mouse Pointer
    - ``0x2201`` Adjustable DPI
    - ``0x2205`` Pointer Motion Scaling
    - ``0x2230`` Sensor angle snapping
    - ``0x2240`` Surface Tuning
    - ``0x2400`` Hybrid Tracking Engine

04 - Keyboard
    - ``0x40a0`` Fn Inversion
    - ``0x40a2`` Fn Inversion, with default state
    - ``0x40a3`` Fn Inversion, for multi-host devices
    - ``0x4100`` Encryption
    - ``0x4220`` Lock Key State
    - ``0x4301`` Solar Keyboard Dashboard Feature
    - ``0x4520`` Keyboard Layout
    - ``0x4521`` Disable Keys
    - ``0x4522`` Disable Keys By Usage
    - ``0x4530`` Dual Platform
    - ``0x4540`` Keyboard International Layouts
    - ``0x4600`` Crown

06 - Touchpad
    - ``0x6010`` Touchpad FW items
    - ``0x6011`` Touchpad SW Items
    - ``0x6012`` Touchpad Win8 FW items
    - ``0x6020`` TAP enable
    - ``0x6021`` TAP enable Extended
    - ``0x6030`` Cursor Ballistic
    - ``0x6040`` Touchpad resolution divider
    - ``0x6100`` TouchPad Raw XY
    - ``0x6110`` TouchMouse Raw TouchPoints
    - ``0x6120`` BT TouchMouse Settings
    - ``0x6500`` Gestures1
    - ``0x6501`` Gestures2

08 - Gaming Devices
    - ``0x8010`` Gaming G-Keys
    - ``0x8020`` Gaming M-keys
    - ``0x8030`` MacroRecord, MR Key
    - ``0x8040`` Brightness control
    - ``0x8060`` Adjustable Report Rate
    - ``0x8070`` Color LED Effects
    - ``0x8071`` RGB Effects
    - ``0x8080`` Per Key Lighting
    - ``0x8090`` Mode status
    - ``0x8100`` Onboard Profiles
    - ``0x8110`` Mouse Button Filter


.. |IRoot| replace:: ``IRoot``
.. _IRoot: features/0x0000-IRoot.md
