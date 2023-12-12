# Volatility Estimates

For calculating volatility, the following approach is taken:

First of all we don't use standard dev or variance, we simply find the biggest price swing given a period

We do that so we can find the worst case scenario that has actually happened and offer a suggestion based on that

The second step is to backtest said suggestion against the realized prices

The downside of such an approach is that we:
- Cannot predict future volatility
- Are not adapting to current volatility

The goal of the estimate is not to find an optimal answer, but rather to find the answer that would not have died given past volatility, as from my pov I value resilience over anything else

A different methodology could have been followed, yielding different results.


## Observations

Given the data set I scraped directly from:
- Coingecko API (historical daily prices of ETH and BTC)
- CL Feeds on Optism

The elaboration of the data is the following


## BTC Historical (CG) -
```ts
formattedData.length 3846
pricesWithinTimePeriod 3846
mean 12375.450348571932
STANDARD DEVIATION 15619.672321718544
as percent of mean 126.21498112608872
Highest Price in period 67617.0155448617
Lowest Price in period 67.809
TIME_DAY Swings {
  max: 4614.707974813398,
  min: -2792.5315811522287,
  pointsOfBiggestNegativeSwing: [
    { date: 1583971200, price: 7935.522040170545 },
    { date: 1584057600, price: 5142.990459018316 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1512518400, price: 13876.4735794148 },
    { date: 1512604800, price: 18491.1815542282 }
  ],
  maxPercentUp: 33.25562469746734,
  minPercentDown: 35.19026936118513
}
TIME_4_HOURS Swings {
  max: 0,
  min: -187.38500000000022,
  pointsOfBiggestNegativeSwing: [
    { date: 1496791795.701, price: 2903.4438 },
    { date: 1496793600, price: 2716.0588 }
  ],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 6.453887621313704
}
TIME_1_HOUR Swings {
  max: 0,
  min: -187.38500000000022,
  pointsOfBiggestNegativeSwing: [
    { date: 1496791795.701, price: 2903.4438 },
    { date: 1496793600, price: 2716.0588 }
  ],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 6.453887621313704
}
```

## wBTC Feed on OP
```ts
formattedData.length 1520
Highest Price in period 3590613372370
Lowest Price in period 3406351323004
TIME_DAY Swings {
  max: 171010093918,
  min: -155208022370,
  pointsOfBiggestNegativeSwing: [
    { date: 1698893439, price: 3590613372370 },
    { date: 1698942695, price: 3435405350000 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1698850575, price: 3419603278452 },
    { date: 1698893439, price: 3590613372370 }
  ],
  maxPercentUp: 5.000875247593445,
  minPercentDown: 4.3226046993623894
}
TIME_4_HOURS Swings {
  max: 122694211618,
  min: -106517515142,
  pointsOfBiggestNegativeSwing: [
    { date: 1698929221, price: 3541922865142 },
    { date: 1698942695, price: 3435405350000 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1699376639, price: 3462385697778 },
    { date: 1699390657, price: 3585079909396 }
  ],
  maxPercentUp: 3.5436321174945795,
  minPercentDown: 3.00733582287455
}
TIME_1_HOUR Swings {
  max: 76940296544,
  min: -59798948192,
  pointsOfBiggestNegativeSwing: [
    { date: 1698844961, price: 3506185871694 },
    { date: 1698846523, price: 3446386923502 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1698868975, price: 3460980394300 },
    { date: 1698872477, price: 3537920690844 }
  ],
  maxPercentUp: 2.223078081306541,
  minPercentDown: 1.7055270422132063
}
```

## ETH Historical (CG)
```ts
formattedData.length 3016
Highest Price in period 4815.004634322234
Lowest Price in period 0.4329785990312485
TIME_DAY Swings {
  max: 0.3797931703461326,
  min: -1.50087,
  pointsOfBiggestNegativeSwing: [
    { date: 1438905600, price: 2.83162 },
    { date: 1438992000, price: 1.33075 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1439164800, price: 0.6875857090838133 },
    { date: 1439251200, price: 1.067378879429946 }
  ],
  maxPercentUp: 55.235756841455476,
  minPercentDown: 53.00393414370572
}
TIME_4_HOURS Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
TIME_1_HOUR Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
```

## ETH Feed on OP - NEW
```ts
formattedData.length 1120
Highest Price in period 191420744869
Lowest Price in period 177993789079
TIME_DAY Swings {
  max: 7611125069,
  min: -9391210921,
  pointsOfBiggestNegativeSwing: [
    { date: 1698895233, price: 187385000000 },
    { date: 1698974331, price: 177993789079 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1698859553, price: 179773874931 },
    { date: 1698895233, price: 187385000000 }
  ],
  maxPercentUp: 4.233721430280828,
  minPercentDown: 5.011719679269952
}
TIME_4_HOURS Swings {
  max: 6002125069,
  min: -5263373028,
  pointsOfBiggestNegativeSwing: [
    { date: 1698929367, price: 184230000000 },
    { date: 1698942673, price: 178966626972 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1698859553, price: 179773874931 },
    { date: 1698872697, price: 185776000000 }
  ],
  maxPercentUp: 3.338708180654007,
  minPercentDown: 2.8569576225370463
}
TIME_1_HOUR Swings {
  max: 3485000000,
  min: -2714498401,
  pointsOfBiggestNegativeSwing: [
    { date: 1699189877, price: 189528052354 },
    { date: 1699192775, price: 186813553953 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1698864545, price: 181285000000 },
    { date: 1698867023, price: 184770000000 }
  ],
  maxPercentUp: 1.9223874010535897,
  minPercentDown: 1.4322409623720858
}
```

## ETH Feed Old Data I had fetched in the past
```ts
formattedData.length 5400
Highest Price in period 197554390000
Lowest Price in period 162836375198
TIME_DAY Swings {
  max: 15789020000,
  min: -13235255000,
  pointsOfBiggestNegativeSwing: [
    { date: 1686316867, price: 185431255000 },
    { date: 1686384179, price: 172196000000 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1687278705, price: 173860980000 },
    { date: 1687365017, price: 189650000000 }
  ],
  maxPercentUp: 9.081405154854183,
  minPercentDown: 7.1375534830953935
}
TIME_4_HOURS Swings {
  max: 11080300000,
  min: -11146552467,
  pointsOfBiggestNegativeSwing: [
    { date: 1686765639, price: 174452150000 },
    { date: 1686776369, price: 163305597533 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1688132885, price: 183303000000 },
    { date: 1688147099, price: 194383300000 }
  ],
  maxPercentUp: 6.044800139659471,
  minPercentDown: 6.389461217302281
}
TIME_1_HOUR Swings {
  max: 7155490000,
  min: -9359353646,
  pointsOfBiggestNegativeSwing: [
    { date: 1686772833, price: 172664951179 },
    { date: 1686776369, price: 163305597533 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1688142429, price: 186313000000 },
    { date: 1688146009, price: 193468490000 }
  ],
  maxPercentUp: 3.840574731768583,
  minPercentDown: 5.42052893890275
}
```


### Price Feed OP-rETH-ETH
```ts
formattedData.length 120
Highest Price in period 1088674613563376100
Lowest Price in period 1076632436768685300
TIME_DAY Swings {
  max: 84395720653184,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [
    { date: 1692141293, price: 1080335279804405500 },
    { date: 1692209181, price: 1080419675525058700 }
  ],
  maxPercentUp: 0.007811993390465211,
  minPercentDown: 0
}
TIME_4_HOURS Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
TIME_1_HOUR Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
```

## OP-sfrxETH-frxETH
```ts
formattedData.length 40
Highest Price in period 1063912837004105700
Lowest Price in period 1059494049456483100
TIME_DAY Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
TIME_4_HOURS Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
TIME_1_HOUR Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
```

## OP-wstETH-stETH
```ts
formattedData.length 400
Highest Price in period 1145813167107669100
Lowest Price in period 1089340375381018100
TIME_DAY Swings {
  max: 415330629984256,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [
    { date: 1668006208, price: 1094362060619180400 },
    { date: 1668092599, price: 1094777391249164700 }
  ],
  maxPercentUp: 0.037951848380897414,
  minPercentDown: 0
}
TIME_4_HOURS Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
TIME_1_HOUR Swings {
  max: 0,
  min: 0,
  pointsOfBiggestNegativeSwing: [],
  pointsOfBiggestPositiveSwing: [],
  maxPercentUp: 0,
  minPercentDown: 0
}
```

## OP-stETH-USD
```ts
formattedData.length 3840
Highest Price in period 212737000000
Lowest Price in period 106503037247
TIME_DAY Swings {
  max: 21676299217,
  min: -19936828390,
  pointsOfBiggestNegativeSwing: [
    { date: 1692233133, price: 179166708390 },
    { date: 1692309105, price: 159229880000 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1678635383, price: 146556137600 },
    { date: 1678720198, price: 168232436817 }
  ],
  maxPercentUp: 14.790441104665138,
  minPercentDown: 11.127529533334192
}
TIME_4_HOURS Swings {
  max: 13475898435,
  min: -14284410200,
  pointsOfBiggestNegativeSwing: [
    { date: 1692297279, price: 173514290200 },
    { date: 1692309105, price: 159229880000 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1673644983, price: 141683932160 },
    { date: 1673657095, price: 155159830595 }
  ],
  maxPercentUp: 9.511239721792883,
  minPercentDown: 8.232411395934697
}
TIME_1_HOUR Swings {
  max: 9739923872,
  min: -12493587500,
  pointsOfBiggestNegativeSwing: [
    { date: 1692305713, price: 171723467500 },
    { date: 1692309105, price: 159229880000 }
  ],
  pointsOfBiggestPositiveSwing: [
    { date: 1673656116, price: 145419906723 },
    { date: 1673657095, price: 155159830595 }
  ],
  maxPercentUp: 6.697792682918499,
  minPercentDown: 7.27541068316711
}
```