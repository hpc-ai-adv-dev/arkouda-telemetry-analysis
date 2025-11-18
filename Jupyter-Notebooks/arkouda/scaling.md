Collecting numbers for scaling data across large data sizes and 100s of nodes

Numbers for the original approach:

```
Setup Time: 0.81 seconds
Runtime: 34.74 seconds
Preprocess Time: 7.74 seconds
Inner Loop Time: 26.54 seconds
  Region Slice Time: 5.70 seconds
  Enter Leave Time: 11.42 seconds
  Durations Time: 2.39 seconds
  Energy Mask Time: 3.80 seconds
  Energy DF Slices Time: 0.30 seconds
  Trapz Vectorized Time: 2.57 seconds
  Misc Time: 0.36 seconds
Print Time: 0.45 seconds
```
```
Setup Time: 0.93 seconds
Runtime: 36.27 seconds
Preprocess Time: 8.43 seconds
Inner Loop Time: 27.37 seconds
  Region Slice Time: 5.98 seconds
  Enter Leave Time: 11.87 seconds
  Durations Time: 2.44 seconds
  Energy Mask Time: 3.83 seconds
  Energy DF Slices Time: 0.31 seconds
  Trapz Vectorized Time: 2.57 seconds
  Misc Time: 0.37 seconds
Print Time: 0.46 seconds
```


Masking only arrays approach and dropping extra cols:

```
Setup Time: 0.82 seconds
Runtime: 19.72 seconds
Preprocess Time: 7.12 seconds
Inner Loop Time: 12.15 seconds
  Region Slice Time: 0.57 seconds
  Enter Leave Time: 1.80 seconds
  Durations Time: 2.35 seconds
  Energy Mask Time: 3.76 seconds
  Energy DF Slices Time: 0.31 seconds
  Trapz Vectorized Time: 2.55 seconds
  Misc Time: 0.81 seconds
Print Time: 0.45 seconds
```
8x data
```
Setup Time: 1.62 seconds
Runtime: 70.13 seconds
Preprocess Time: 49.68 seconds
Inner Loop Time: 20.00 seconds
  Region Slice Time: 0.65 seconds
  Enter Leave Time: 2.56 seconds
  Durations Time: 5.36 seconds
  Energy Mask Time: 6.86 seconds
  Energy DF Slices Time: 0.37 seconds
  Trapz Vectorized Time: 3.34 seconds
  Misc Time: 0.87 seconds
Print Time: 0.45 seconds
```


Converting the columns to categorical for better performance on top of the above approach:
This specific approach doesn't seem to help (which is unexpected)
So we won't use it.

Timings for 8x data
Both region and event:
```
Setup Time: 1.58 seconds
Runtime: 72.59 seconds
Preprocess Time: 50.96 seconds
Inner Loop Time: 21.17 seconds
  Region Slice Time: 1.70 seconds
  Enter Leave Time: 3.59 seconds
  Durations Time: 4.54 seconds
  Energy Mask Time: 6.91 seconds
  Energy DF Slices Time: 0.36 seconds
  Trapz Vectorized Time: 3.20 seconds
  Misc Time: 0.86 seconds
Print Time: 0.45 seconds
```

Only event:
```
Setup Time: 1.63 seconds
Runtime: 71.64 seconds
Preprocess Time: 50.39 seconds
Inner Loop Time: 20.79 seconds
  Region Slice Time: 0.65 seconds
  Enter Leave Time: 3.59 seconds
  Durations Time: 5.09 seconds
  Energy Mask Time: 6.89 seconds
  Energy DF Slices Time: 0.37 seconds
  Trapz Vectorized Time: 3.35 seconds
  Misc Time: 0.85 seconds
Print Time: 0.45 seconds

```

Only region:
```
Setup Time: 1.60 seconds
Runtime: 72.13 seconds
Preprocess Time: 50.84 seconds
Inner Loop Time: 20.84 seconds
  Region Slice Time: 1.70 seconds
  Enter Leave Time: 3.08 seconds
  Durations Time: 4.97 seconds
  Energy Mask Time: 6.64 seconds
  Energy DF Slices Time: 0.36 seconds
  Trapz Vectorized Time: 3.26 seconds
  Misc Time: 0.84 seconds
Print Time: 0.45 seconds
```

Numbers for the sort and slice approach (not used due to bad perf):

```
Setup Time: 0.84 seconds
Runtime: 39.66 seconds
Preprocess Time: 15.10 seconds
Inner Loop Time: 24.10 seconds
  Region Slice Time: 14.31 seconds
  Enter Leave Time: 0.00 seconds
  Durations Time: 2.48 seconds
  Energy Mask Time: 3.98 seconds
  Energy DF Slices Time: 0.32 seconds
  Trapz Vectorized Time: 2.64 seconds
  Misc Time: 0.37 seconds
Print Time: 0.46 seconds

```




----- preprocess scaling issue exploration:

n8x8:
```
Setup Time: 2.20 seconds
Runtime: 58.10 seconds
Preprocess Time: 35.45 seconds
  Preprocess Power data Time: 0.16 seconds
  Approximation Power data Time: 34.81 seconds
  Other Preprocess Time: 0.48 seconds
Inner Loop Time: 22.18 seconds
  Region Slice Time: 0.60 seconds
  Enter Leave Time: 6.13 seconds
  Durations Time: 4.49 seconds
  Energy Mask Time: 5.80 seconds
  Energy DF Slices Time: 0.42 seconds
  Trapz Vectorized Time: 3.93 seconds
  Misc Time: 0.81 seconds
Print Time: 0.47 seconds
```

n2x2
```
Setup Time: 1.94 seconds
Runtime: 21.74 seconds
Preprocess Time: 5.88 seconds
  Preprocess Power data Time: 0.14 seconds
  Approximation Power data Time: 5.34 seconds
  Other Preprocess Time: 0.41 seconds
Inner Loop Time: 15.48 seconds
  Region Slice Time: 0.48 seconds
  Enter Leave Time: 4.94 seconds
  Durations Time: 2.09 seconds
  Energy Mask Time: 3.40 seconds
  Energy DF Slices Time: 0.38 seconds
  Trapz Vectorized Time: 3.46 seconds
  Misc Time: 0.72 seconds
Print Time: 0.38 seconds
```

So I need to fix Approximation Power data Time, since it's not showing scaling
Using xp
```
Time to slice dataframes: 0.0000 seconds
Time to check and insert first power value: 0.0499 seconds
Time to find indices: 34.6210 seconds
Time to get aligned power data: 0.0218 seconds
```
using ak
```
Time to slice dataframes: 0.0000 seconds
Time to check and insert first power value: 0.0486 seconds
Time for searchsorted: 35.1447 seconds
Time to get aligned power data: 0.0207 seconds
```

So the xp wrapper isn't the issue. searchsorted just doesn't scale well.
We need to address that issue some other time perhaps.

------------
124 nodes, 1x data:

```
Setup Time: 2.10 seconds
Runtime: 85.93 seconds
Preprocess Time: 8.78 seconds
Inner Loop Time: 76.45 seconds
  Region Slice Time: 20.21 seconds
  Enter Leave Time: 39.63 seconds
  Durations Time: 3.90 seconds
  Energy Mask Time: 6.68 seconds
  Energy DF Slices Time: 0.47 seconds
  Trapz Vectorized Time: 5.08 seconds
  Misc Time: 0.48 seconds
Print Time: 0.70 seconds
```