************************
Report Rate (``0x8060``)
************************

Feature to change the device report rate

.. table:: Table 1 - Functions
    :widths: auto

    == ====================== =========================================================
    ID          Name                               Description
    == ====================== =========================================================
    0  ``GetReportRateList``  Retrieve the various report rates supported by the device
    1  ``GetReportRate``      Return the current report rate in ms  
    2  ``SetReportRate``      Set the report rate in ms
    == ====================== =========================================================


Functions
=========


``GetReportRateList``
~~~~~~~~~~~~~~~~~~~~~

    Retrieve the various report rates supported by the device.
    Standard report rates are 1, 2, 4 and 8ms.

Arguments
    - None

Returns          
     - reportRateList
                                                                                         
        .. table:: 
            :widths: auto

            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+
            | byte                  |           bit                                |
            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+
            |      | field          | 7   | 6   |  5  |  4  | 3  | 2   | 1   |  0  |   
            +======+================+=====+=====+=====+=====+====+=====+=====+=====+
            | 0    | reportRateList | 8ms | 7ms | 6ms | 5ms |4ms | 3ms | 2ms | 1ms |
            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+

        0 = rate not supported, 1  supported

``GetReportRate``
~~~~~~~~~~~~~~~~~

Arguments
    - None

Returns
    - reportRate
        The current report rate in ms.

        .. table:: 
            :widths: auto

            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+
            | byte                  |           bit                                |
            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+
            |      | field          | 7   | 6   |  5  |  4  | 3  | 2   | 1   |  0  |   
            +======+================+=====+=====+=====+=====+====+=====+=====+=====+
            | 0    | reportRate     |                                              |
            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+


``SetReportRate``
~~~~~~~~~~~~~~~~~

    This function can be called only in host mode
    
Parameters
    - reportRate
        The new report rate in ms     


        .. table:: 
            :widths: auto

            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+
            | byte                  |           bit                                |
            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+
            |      | field          | 7   | 6   |  5  |  4  | 3  | 2   | 1   |  0  |   
            +======+================+=====+=====+=====+=====+====+=====+=====+=====+
            | 0    | reportRate     |                                              |
            +------+----------------+-----+-----+-----+-----+----+-----+-----+-----+

Returns
    - None

Errors
     - InvalidArgument (2) Invalid reportRate, not in host mode

