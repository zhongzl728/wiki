# TheExec (The Executive) 🚧

> This post was originally written in English.

**TheExec(The Executive)** is one of the highest-level objects, which gives access to test executive related properties.

## Flow

```vbscript
TheExec.Flow
```

### TestLimit

```vbscript
TheExec.Flow.TestLimit(resultVal, lowVal, hiVal, lowCompareSign,
highCompareSign, scaletype, unit, formatStr, TName, compareMode, pinName,
forceVal, forceunit, customUnit, customForceunit, ForceResults, TNum)
```

Parameters most used:

- **resultVal** (required): Which the result value will been written.
- **lowVal**, **hiVal** : The low and high limits. Default is lowVal <= resultVal <= hiVal.
- **unit**: The unit of measurement
  - `unitAmp` `unitVolt` `unitDb` `unitHz` `unitTime` .
- **TName**: A test name to be datalogged. If left blank, test instance's name will be used.
- **pinName**: The pin name to be datalogged.
- **forceVal**, **forceunit**: The test condition value and unit.
- **ForceResults**: Whether to force a pass or fail or to use the limits specified in a flow table.

For an example:

```vbscript
TheExec.Flow.TestLimit  resultVal:=Vout_Measure, _
                        unit:=unitVolt, _
                        Tname:="Output_Voltage", _
                         pinName:=vout_pin, _
                         forceval:=vin_pin_voltage, _
                         forceunit:=unitVolt, _
                         forceresults:=tlForceFlow, _
                         lowval:=VOT_LowLimit, _
                         hival:=VOT_HiLimit
```
