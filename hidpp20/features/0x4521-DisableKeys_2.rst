*************************
Disable Keys (``0x4521``)
*************************

.. table:: Table 1 - Functions

    == ====================== =======================================================
    ID          Name                               Arguments
    == ====================== =======================================================
     0 GetCapabilities        None
     1 GetDisabledKeys        None
     2 SetDisabledKeys        keysToDisable
    == ====================== =======================================================

Functions
=========

DisableKeys
~~~~~~~~~~~

Define the presence of the keys which the SW allows the user to disable, and allow SW to disable them.
A unifying device containing this feature should adopt HID++ reset policy #3 and implement the 0x0020
configuration change feature.

GetCapabilities
~~~~~~~~~~~~~~~

Summary

    Returns keys which the SW allows the user to disable

Parameters

    None

Returns

    - disableableKeys  [8bits] keys which the SW allows the user to disable

    +---+---+---+---------+--------+------------+---------+----------+
    | 7 | 6 | 5 |     4   |    3   |   2        |   1     |      0   |
    +===+===+===+=========+========+============+=========+==========+
    |   |   |   | Windows | Insert | ScrollLock | NumLock | CAPSLock |
    |   |   |   | (Start) |        |            |         |          |
    +---+---+---+---------+--------+------------+---------+----------+

GetDisabledKeys
~~~~~~~~~~~~~~~

Summary

    Returns list of keys the SW has disabled

Parameters

    None

Returns

    - disabledKeys [8bits] keys which the SW has disabled

    +---+---+---+---------+--------+------------+---------+----------+
    | 7 | 6 | 5 |     4   |    3   |   2        |   1     |      0   |
    +===+===+===+=========+========+============+=========+==========+
    |   |   |   | Windows | Insert | ScrollLock | NumLock | CAPSLock |
    |   |   |   | (Start) |        |            |         |          |
    +---+---+---+---------+--------+------------+---------+----------+


SetDisabledKeys
~~~~~~~~~~~~~~~

Summary

    Selects which keys to disable

Parameters

    - keysToDisable [8bits] keys to disable


    +---+---+---+---------+--------+------------+---------+----------+
    | 7 | 6 | 5 |     4   |    3   |   2        |   1     |      0   |
    +===+===+===+=========+========+============+=========+==========+
    |   |   |   | Windows | Insert | ScrollLock | NumLock | CAPSLock |
    |   |   |   | (Start) |        |            |         |          |
    +---+---+---+---------+--------+------------+---------+----------+


Returns

    - disabledKeys [8bits] echo of the keysToDisable parameter


    +---+---+---+---------+--------+------------+---------+----------+
    | 7 | 6 | 5 |     4   |    3   |   2        |   1     |      0   |
    +===+===+===+=========+========+============+=========+==========+
    |   |   |   | Windows | Insert | ScrollLock | NumLock | CAPSLock |
    |   |   |   | (Start) |        |            |         |          |
    +---+---+---+---------+--------+------------+---------+----------+
