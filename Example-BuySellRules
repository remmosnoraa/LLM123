**Buy Rules**
[Div Paid] LoopSum("DivPS(ctr,qtr)>0",4)=4
[Div Raised] LoopSum("DivPS(ctr,qtr)>=DivPS(ctr+1,qtr)",4)=4
[Sector] SecWeight < 30
[Recent Sell] LastSellDaysLT(120)=false
[Yield] Between(FRank("Yield"),65,80)
[NoMortg] RBICS(REITMORTG) = false
[Quality] Rating("Classic Quality")>75

**Sell Rules**
[Weight] eval(Weight >7.5, .33, 0)
[Yield High] FRank("Yield")>=90
[Yield Low] Eval(NoBars<120,FRank("yield")<60,FRank("Yield")<70)
[Quality] Rating("Classic Quality")<35
[Div cut] DivPS(0,qtr)<DivPS(1,qtr)
