****************************************
Persistent Remappable Action (``x1C00``)
****************************************

This feature describes user actions available on a device and allows them to be mapped to a behaviour that will be
persistent in the device. The defined behaviour can be the sending of a single HID report or an internal function (e.g.
show battery status).

.. table:: Table 1 - Functions
    :widths: auto

    == ======================= =======================================================
    ID          Name                               Description
    == ======================= =======================================================
    0  getFeatureInfo          get supported capabilities
    1  getCount                return number of items in control ID table
    2  getCidInfo              return a row of the control ID table
    3  getPersistentAction     return action for control ID and host
    4  setPersistentAction     set action for control ID and host
    5  resetPersistentAction   reset action for control ID and host to default
    6  resetToFactorySettings  reset one or more hosts to factory settings
    == ======================= =======================================================

Overview
========

Functions are provided to enumerate controls on the device which are interesting to software. This includes any
control which can be reprogrammed or should be handled by the software in some special way. A control can be a
physical input (e.g. key press, button) or any user input (e.g. long press, gesture, voice, etc.).

A particular control ID shall not appear more than once in the enumerated list of control IDs. If duplicate control IDs
are desired a new control ID should be created to identify the secondary instance of the control.

Once enumerated, controls can be configured to perform actions different from their default. Controls are identified by
control ID. The control IDs defined in this feature are identical to the control IDs defined in the x1b04 feature. The list
of Control IDs can differ from one host to the other.

The control ID is used by software to determine the possible list of options the user may assign to the control.
The last 3 functions of the API reflect the persistence aspect of this feature with which the SW can remap a control to
another behaviour that will be persistent in the device.

Note: If the device has both features x1b04 and x1c00, and the "divert" flag on feature
0x1b04 is set for a given CtrlId, then feature x1b04 takes precedence over
feature x1c00, and the x1b04 hidpp "diverted" event is sent instead of the
persistenly remaped action on the x1c00.

Note: The make and break mechanism when a modifier key is pressed along with
another key should be: on press: make(modifier) + make(key),
on release: break(key) + break(modifier).
For example: Win + Q should be:
on press: make(Win) + make(Q),
on release: break(Q) + break(Win).
Code and modifier are sent within a single report.

Functions
=========

``getFeatureInfo``
~~~~~~~~~~~~~~~~~~

Parameters
    - none

Returns
    - flags
        16-bit unsigned representing the capabilities of the device.

        .. table::

            +-----------------+----------------------------------------------------------------------------------+
            | kbd report      | (0)/1 -> x1c00 is (not) capable of sending keyboard keys                         |
            +-----------------+----------------------------------------------------------------------------------+
            | mouse buttons   | (0)/1 -> x1c00 is (not) capable of sending mouse buttons                         |
            +-----------------+----------------------------------------------------------------------------------+
            | X disp          | (0)/1 -> x1c00 is (not) capable of sending mouse X displacement                  |
            +-----------------+----------------------------------------------------------------------------------+
            | Y disp          | (0)/1 -> x1c00 is (not) capable of sending mouse Y displacement                  |
            +-----------------+----------------------------------------------------------------------------------+
            | vert roller     | (0)/1 -> x1c00 is (not) capable of sending vertical roller increments            |
            +-----------------+----------------------------------------------------------------------------------+
            | horz roller     | (0)/1 -> x1c00 is (not) capable of sending horizontal roller increments (AC PAN) |
            +-----------------+----------------------------------------------------------------------------------+
            | consumer ctrl   | (0)/1 -> x1c00 is (not) capable of sending consumer control codes                |
            +-----------------+----------------------------------------------------------------------------------+
            | internal        | (0)/1 -> x1c00 is (not) capable of executing internal functions                  |
            +-----------------+----------------------------------------------------------------------------------+
            | power key       | (0)/1 -> x1c00 is (not) capable of sending power keys                            |
            +-----------------+----------------------------------------------------------------------------------+

    .. table:: Table 1. getFeatureInfo response packet
        :widths: auto

        +------+-----------+----------+---------------+-------------+-------------+--------+--------+---------------+------------+
        | byte		   |           bit                                                                                       +
        +------+-----------+----------+---------------+-------------+-------------+--------+--------+---------------+------------+
        |      | field     | 7        | 6             |   5         |   4         |   3    |   2    |   1           |   0        |   
        +======+===========+==========+===============+=============+=============+========+========+===============+============+
        | 0    | flags MSB | --       | --            |   --        |   --        |   --   |   --   |   --          | power key  |   
        +------+-----------+----------+---------------+-------------+-------------+--------+--------+---------------+------------+
        | 1    + flags LSB | internal | consumer ctrl | horz roller | vert roller | Y disp | X disp | mouse buttons | Kbd report |
        +------+-----------+----------+---------------+-------------+-------------+--------+--------+---------------+------------+


``getCount``
~~~~~~~~~~~~

Returns the number of items in the control ID table. These are sources (user stimuli) like controls (hot keys) and extra
native functions (e.g. a given gesture) that a specific action can be mapped to.

Parameters
    - none

Returns
    - count
        The number of items in the control ID table.
    - numberOfHosts
        The number of hosts supported by the device.

    .. table:: Table 2. getCount response packet format
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |   
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    | count             |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    | numberOfHosts     |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

``getCidInfo``
~~~~~~~~~~~~~~

Returns a row from the control ID table. This table information describes capabilities and desired software handling
for physical controls in the device. All control IDs will be the exact same per host.

Parameters
    - index
        The zero based row index to retrieve.
    - hostIndex
        8-bit unsigned representing the host number where the control ID is remmapped (0 based index). When setting
        hostIndex to 0xff, the device shall select the current host.

    .. table:: Table 3. getCidInfo request packet
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    | index             |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    | hostIndex         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

Returns
    - cid
        16-bit unsigned representing the control ID of the control (physical button or user action). Each control ID is unique
        in this list.

    .. table:: Table 4. getCidInfo() response packet format
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    + cid MSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    + cid LSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+


``getPersistentAction``
~~~~~~~~~~~~~~~~~~~~~~~

For a given control ID and hostIndex supported by the device, returns the corresponding actionId, hidUsage and
modifierMask from the non-volatile memory in the device.

Parameters
    - cid
        16-bit unsigned representing the control ID of the control being requested (physical button or user action).
    - hostIndex
        8-bit unsigned representing the host number where the control ID is remmapped (0 based index). When setting
        hostIndex to 0xff, the device shall select the current host.

    .. table:: Table 5. getPersistentAction request packet
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    + cid MSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    + cid LSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 2    + hostIndex         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

Returns
    - cid
        16-bit unsigned representing the control ID of the control being addressed (physical button or user action).
    - hostIndex
        8-bit unsigned representing the host number where the control ID has to be remmapped (0 based index). When
        setting hostIndex to 0xff, the device shall select the current host.
    - actionId
        8-bit unsigned representing the action that has to be performed by the device.

        .. table:: 
            :widths: auto

            +------+-------------------------------------------------------------------------+
            | 0x01 | send keyboard/keypad report (usage page 7)                              |
            +------+-------------------------------------------------------------------------+
            | 0x02 | send mouse button report (usage page 9)                                 |
            +------+-------------------------------------------------------------------------+
            | 0x03 | send X displacement (usage page 1, code 0x30)                           |
            +------+-------------------------------------------------------------------------+
            | 0x04 | send Y displacement (usage page 1, code 0x31)                           |
            +------+-------------------------------------------------------------------------+
            | 0x05 | send vertical roller/wheel displacement (usage page 1, code 0x38)       |
            +------+-------------------------------------------------------------------------+
            | 0x06 | send horizontal roller/AC pan displacement (usage page 12, code 0x0238) |
            +------+-------------------------------------------------------------------------+
            | 0x07 | send consumer control report (usage page 12)                            |
            +------+-------------------------------------------------------------------------+
            | 0x08 | execute internal funtion (use value parameter as function index)        |
            +------+-------------------------------------------------------------------------+
            | 0x09 | send power key report (usage page 1)                                    |
            +------+-------------------------------------------------------------------------+
            | All other values are reserved for internal FW use.                             |
            +------+-------------------------------------------------------------------------+

    - value
        16-bit unsigned standard HID code sent when control ID is triggered by the user. It is an unsigned HID usage code for
        keyboard and consumer control keys. A HID usage code or a bit-map (TBD) for mouse buttons. A signed value for all
        displacements. An index to the internal function within the device(e.g. show battery status).
    - modifierMask
        8-bit unsigned, this is the HID usage value for standard modifier keys like Win, Left Shift,Right Shift on Windows,
        corresponding to the hid usage page above.

        Note: Modifiers will only work for keyboard reports.

    - cidStatus
        8-bit-field that contains the remmapped bit (equals 0 if default behaviour is mapped to this control ID)


    .. table:: Table 6. getPersistentAction() response packet format
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    + cid MSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    + cid LSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 2    + hostIndex         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 2    + actionId          |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 4    + value MSB         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 5    + value LSB         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 6    | modifierMask      | R-GUI | R-Alt     | R-Shift     | R-Ctrl      | L-GUI  | L-Alt  | L-Shift       | L-Crtl     |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 7    | cidStatus         | --    | --        | --          | --          | --     | --     | --            | remapped   |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

``setPersistentAction``
~~~~~~~~~~~~~~~~~~~~~~~

For a given control ID and hostIndex supported by the device, sets the corresponding actionId, value and modifierMask
to the non-volatile memory in the device.

Parameters
    - cid
        16-bit unsigned representing the control ID of the control being addressed (physical button or user action).
    - hostIndex
        8-bit unsigned representing the host number where the control ID has to be remmapped (0 based index). When
        setting hostIndex to 0xff, the device shall select the current host.
    - actionId
        8-bit unsigned representing the action that has to be performed by the device.

        .. table:: 
            :widths: auto

            +------+-------------------------------------------------------------------------+
            | 0x01 | send keyboard/keypad report (usage page 7)                              |
            +------+-------------------------------------------------------------------------+
            | 0x02 | send mouse button report (usage page 9)                                 |
            +------+-------------------------------------------------------------------------+
            | 0x03 | send X displacement (usage page 1, code 0x30)                           |
            +------+-------------------------------------------------------------------------+
            | 0x04 | send Y displacement (usage page 1, code 0x31)                           |
            +------+-------------------------------------------------------------------------+
            | 0x05 | send vertical roller/wheel displacement (usage page 1, code 0x38)       |
            +------+-------------------------------------------------------------------------+
            | 0x06 | send horizontal roller/AC pan displacement (usage page 12, code 0x0238) |
            +------+-------------------------------------------------------------------------+
            | 0x07 | send consumer control report (usage page 12)                            |
            +------+-------------------------------------------------------------------------+
            | 0x08 | execute internal funtion (use value parameter as function index)        |
            +------+-------------------------------------------------------------------------+
            | 0x09 | send power key report (usage page 1)                                    |
            +------+-------------------------------------------------------------------------+
            | All other values are reserved for internal FW use.                             |
            +------+-------------------------------------------------------------------------+
        
    - value
        16-bit unsigned standard HID code to be sent when control ID is triggered by the user. It is an unsigned HID usage
        code for keyboard and consumer control keys. A HID usage code or a bit-map (TBD) for mouse buttons. A signed
        value for all displacements. An index to the internal function within the device(e.g. show battery status).
    - modifierMask
        8-bit unsigned, this is the HID usage value for standard modifier keys like Win, Left Shift,Right Shift on Windows,
        corresponding to the hid usage page above.

        Note: Modifiers will only work for keyboard reports.


.. table:: Table 7. setPersistentAction request packet
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    + cid MSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    + cid LSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 2    + hostIndex         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 3    + actionId          |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 4    + value MSB         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 5    + value LSB         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 6    + modifierMask      | R-GUI | R-Alt     | R-Shift     | R-Ctrl      | L-GUI  | L-Alt  | L-Shift       | L-Ctrl     |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

Returns
   - none

resetPersistentAction
~~~~~~~~~~~~~~~~~~~~~

For a given control ID and hostIndex supported by the device, resets the persistent action to the default factory settings
on the device.

Parameters
    - cid
        16-bit unsigned representing the control ID of the control being addressed (physical button or user action).
    - hostIndex
        8-bit unsigned representing the host number where the control ID has to be reset (0 based index). When setting
        hostIndex to 0xff, the device shall select the current host.

.. table:: Table 8. resetPersistentAction request packet
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    + cid MSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 1    + cid LSB           |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | 2    + hostIndex         |                                                                                              |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

Returns
    - none

resetToFactorySettings
~~~~~~~~~~~~~~~~~~~~~~

For a given hostIdMask supported by the device, resets the persistent action to the default factory settings on the device
for all control IDs.

Parameters
    - hostIdMask
        8-bit-filed representing the hosts where the control IDs has to be reset to default factory settings.

.. table:: Table 9. resetToFactorySettings request packet
        :widths: auto

        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        | byte		           |    bit                                                                                       +
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+
        |      | field             | 7     | 6         |   5         |   4         |   3    |   2    |   1           |   0        |
        +======+===================+=======+===========+=============+=============+========+========+===============+============+
        | 0    + hostIdMask        | --    | --        | --          | --          | --     | host3  | host2         | host1      |
        +------+-------------------+-------+-----------+-------------+-------------+--------+--------+---------------+------------+

Returns
   - none


ChangeLog
=========

Version 0: Initial version
