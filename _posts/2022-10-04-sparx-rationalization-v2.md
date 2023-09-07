---
title: "Sparx Sharpener: Worth the Money? (Updated for Pure Hockey's price hike!)"
author: Jack Thomas
date: '2022-10-04'
slug: sparx-rationalization-v2
categories:
  - category:
    - math
---

In a [previous post](/sparx-rationalization-v1.html), I ran some numbers to determine whether the [Sparx skate sharpener](https://sparxhockey.com/) was worth the money. One of the key assumptions was that Pure Hockey was charging $7.00 per skate sharpning.

Unfortunately, they recently increased their prices to $10.00 per sharpening. (That's an increase of 43% by the way.) Does that change the value proposition at all here?

Here are my new assumptions:

```r
pub_date <- as.Date("2022-10-04")
total_spend <- sum(c(799.99, 139.99, 44.99, 164.99, 92.00, 35.00))
sharpenings_per_ring <- 50
cost_per_ring <- 64.99
cost_per_sharpening <- 10
sharpenings_per_week <- 1.0
```

In the last post, we wrote two reusable functions to run scenarios just like this!

```r
rationalizeSparxCount <- function(total_spend, sharpenings_per_ring, cost_per_ring, cost_per_sharpening, sharpenings_per_week, pub_date) {
  df_sharp <- data.frame(pub_date + (7 / sharpenings_per_week) * 1:500, rep(cost_per_sharpening, 500), cost_per_sharpening * 1:500)
  names(df_sharp) <- c("Date", "Pure Hockey Cost Per", "Pure Hockey Cumulative")
  df_sharp$`Sparx Cost Per` <- (total_spend + floor(seq.int(nrow(df_sharp)) / sharpenings_per_ring) * 64.99) / 1:500
  df_sharp$`Sparx Cumulative` <- total_spend + floor(seq.int(nrow(df_sharp)) / sharpenings_per_ring) * 64.99
  break_even_sharpenings <- min(which(df_sharp$`Sparx Cumulative` < df_sharp$`Pure Hockey Cumulative`))
  return(break_even_sharpenings)
}
rationalizeSparxDate <- function(total_spend, sharpenings_per_ring, cost_per_ring, cost_per_sharpening, sharpenings_per_week, pub_date) {
  return(pub_date + rationalizeSparxCount(
    total_spend = total_spend,
    sharpenings_per_ring = sharpenings_per_ring,
    cost_per_ring = cost_per_ring,
    cost_per_sharpening = cost_per_sharpening,
    sharpenings_per_week = sharpenings_per_week,
    pub_date = pub_date
  ) * (7 / sharpenings_per_week))
}
```

So let's use them!

```r
> rationalizeSparxCount(
+     total_spend = sum(c(799.99, 139.99, 44.99, 164.99, 92.00, 35.00)),
+     sharpenings_per_ring = 50,
+     cost_per_ring = 64.99,
+     cost_per_sharpening = 10,
+     sharpenings_per_week = 1.0,
+     pub_date = as.Date("2022-10-04")
+ )
[1] 141
> 
> rationalizeSparxDate(
+     total_spend = sum(c(799.99, 139.99, 44.99, 164.99, 92.00, 35.00)),
+     sharpenings_per_ring = 50,
+     cost_per_ring = 64.99,
+     cost_per_sharpening = 10,
+     sharpenings_per_week = 1.0,
+     pub_date = as.Date("2022-10-04")
+ )
[1] "2025-06-17"
```

In summary:

- Old Break Even: April 19, 2026 (after 220 sharpenings)
  - And that's assuming I bought the sharpener on January 30, 2022!!
- New Break Even: June 6, 2025 (after 141 sharpenings)
