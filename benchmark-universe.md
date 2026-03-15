Starting Universe is "United States (Incl. Foreign Primary)" - 9000+ stocks in the US plus foreign companies with ADRs.

**Rules**

RBICS(3010, 3025)=false // Excludes Banking and Real Estate Subsectors
!IsMLP // no MLPs
!InSet(ExchangeCode, 13, 19) // Excludes OTC bulletin board and pink sheet stocks
Price>10
MedianDailyTot(125)>35000
AvgDailyTot(50)>35000
DaysLate < 40
ConsEstCnt(#EPSY, 1, 0) > 2
ConsEstCnt(#EPSQ, 1, 0) > 1
ConsEstCnt(#SALEY, 1, 0) > 2
ConsEstCnt(#SALEQ, 1, 0) > 1
SalesGr%TTM != NA
FOrder("MktCap", #Previous) <= 1000