Plot using csv, line:
```
set datafile separator ","
set terminal png size 1920,1080
set output 'output.png'
set xrange [0:100]
set yrange [0:100]
set title 'Referrals comparison in terms of accumulative percentage of referrers'
set xlabel 'Number of referrals per referrer'
set ylabel 'Accumulative percentage of referrers (%)'
plot 'accum_comp1.txt' using 1:2 title 'China user' with lines, \
     'accum_comp1.txt' using 1:3 title 'Non-China user' with lines
```

Plot using csv, bar:
```
set datafile separator ","
set terminal png size 1920,1080
set output 'output.png'
set title 'China User Traveled Referral Histogram'
set xlabel 'Traveled Referral count'
set ylabel 'Referrer count'
set boxwidth 0.6
set style fill solid
plot 'histogram_china_referrals_traveled.csv' using 0:2:xtic(1) with boxes
```
