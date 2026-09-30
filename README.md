tempfile res
postfile handle quantile coef ll ul using `res', replace
foreach q in 10 20 30 40 50 60 70 80 90 {
    quietly xtqreg $y $x $controls i.year, id(id) quantile(`=`q'/100')
    local b = _b[$x]
    post handle (`q') (`b') (`b'-1.96*_se[$x]) (`b'+1.96*_se[$x])
}
postclose handle

use `res', clear
twoway (rarea ll ul quantile, color(navy%20) lwidth(none)) ///
       (line coef quantile, lcolor(navy) lwidth(medthick)), ///
       yline(0, lcolor(red) lpattern(dash)) ///
       xtitle("Quantile") ytitle("Coefficient") ///
       legend(order(1 "95% CI" 2 "Coefficient")) graphregion(color(white))
