# Substation Voltage Analysis

A set of reports detailing summary statistics for substation voltage using data
extracted from the uCentra MDM.  What is the goal here, all meters? Or do we
just want meters on the substation?

## System Voltage By Year

### Data

Table with the following fields:

- Substation Name (Link to individual substation results)
- Year
- Min
- 1st Qu
- Median 
- Mean
- 3rd Qu
- Max

Explain the all the above variables.

### Plots

#### Univariant Plots

- Histogram of the entire network
- Histogram by Substation

#### Bivariant Plots

- Bar Plot of entire network
- Bar Plot by Substation

Explain the outliers, and how to read a box plot.

#### Multivarient Plots

- Load Shape of entire network
- Load Shape By Substation

#### Exceptions

This report process a years worth of data and we need to understand how we can
do this with less than good server specifications (RAM).

## Substation Voltage By Year
```sql
sSQLcmd <- paste("DECLARE @AMIReadSourceId INT 
                  SET @AMIReadSourceId = (  
                        SELECT ReadSourceId from   
                        mdm.dbo.ReadSource   
                        where ReadSourceDescription = 'ITRON')  
                   SELECT  
                   h.ReadLogDate 'h.ReadDate'
         , d.ReadDate 'i.ReadDate'   
                  , Readvalue Voltage  
                  , s.ScadaSubstationIdentifier Station  
                  , h.meteridentifier  Meter 
                  FROM mdm.dbo.meterreadintervalheader h  
                  INNER JOIN mdm.dbo.meter m  
                    ON m.meteridentifier = h.meteridentifier  
                    AND m.readsourceid = @AMIReadSourceId  
                  INNER JOIN mdm.dbo.electricmeters em  
                    ON em.meteridentifier= m.meteridentifier  
                  INNER JOIN mdm.dbo.MeterReadIntervalDetail d  
                    ON d.meterreadintervalheaderid = h.meterreadintervalheaderid  
                    AND d.ReadQualityCode = '3' --Good Reads Only  
                  INNER JOIN mdm.dbo.location l  
                    ON l.locationid = m.locationid  
                  INNER JOIN mdm.dbo.substation s  
                    ON s.substationid = l.substationid  
                  WHERE uom = 'voltage' AND readlogdate = '",dtReadDayFrom,"' 
                    AND em.MeterVoltage like '240%'  
                    AND ReadValue > 0
                  "
                    ) 
```
