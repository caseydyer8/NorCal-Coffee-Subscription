NorCal Coffee Subscription
================
Casey Dyer
2024-08-05

## Background Information

The purpose of this project is to find the best price (cost per lb for
bulk coffee that is roasted in Northern California, defined by an
imaginary line at the furthest south street in Santa Cruz, CA to the
Oregon border at the North end, outlined by the Pacific Ocean to the
West and the Nevada State Line to the East.) The overall purpose is to
purchase coffee in bulk for my family to drink on a daily basis, but the
coffee needs to be of excellent quality, so we will stick to only local
roasters of the Northern California Area (as defined above). A local
roaster is defined by a roaster that has less than 10 storefronts, all
of which must be contained in Northern California (as outlined above).

A couple of the Roaster’s that I used and collected data for this
project do not fit the criteria but I made an exception for them. They
are Verve and Blue Bottle they both have more than 10 storefronts.

I wanted to do this project for a couple reasons. I wanted to see what
the best price I could get a premium coffee that is local to me was and
I wanted to develop a project for my portfolio. I have drank all of the
coffees that I used in this project and I went and found all the data
myself to then add it to an Excel Spreadsheet. I then started figuring
out what metrics I needed to look at to answer the following questions:

- What is the best price per lb of premium coffee that is from a
  Northern California Roaster?
- What would the overall cost incurred be to subscribe to the
  subscription model that I decide to go with?
- What would the cost be when factored down to each cup brewed in my
  personal Chemex for the subscription model I choose?

## Load The Environment

Notes: setting up my R environment by loading the “readr”, tidyverse”,
“ggplot2”, “forcats”, and “dplyr” packages:

``` r
library(readr)
library(tidyr)
library(ggplot2)
library(forcats)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

## Moving My Data From .csv Or Excel To R

I saved my Excel spreadsheet in a .csv or comma separated value file so
I would then be able to use it in R for data manipulation, data
cleaning, and building visualizations. I renamed my .csv to cheap_coffee
so I wouldn’t need to continually type out the full name. The next step
was to look at the data once I uploaded it into R and see what might
need to be done to answer the questions I originally asked when I
started this project (also listed above in Background Information). I
look at the overall data in a dataframe and then look at the names of
the columns to start figuring out what data I should pull to start
answering the questions I had:

``` r
cheap_coffee <- read_csv("C:/Users/cdyer/OneDrive/Documents/R.Scripts/cheap_coffee1.csv")
```

    ## Rows: 6 Columns: 10
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (9): Coffee Company, Subscription Model, Bag Size, Price/Bag, Price per ...
    ## dbl (1): Cups per Pot
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
View(cheap_coffee)

colnames(cheap_coffee)
```

    ##  [1] "Coffee Company"       "Subscription Model"   "Bag Size"            
    ##  [4] "Price/Bag"            "Price per Oz"         "Price per lb"        
    ##  [7] "Cost for 3 Months"    "Coffee Beans Per Pot" "Cups per Pot"        
    ## [10] "Cost per Cup"

## Cleaning Up The Data and Manipulating It

Here I am combining three columns into one and then naming the new
column Beans. The reason for this is I want the Coffee Company and the
Subscription Model to show as one single line on my plot on the y-axis.
I did this to make the data easier to read and work with once I plotted
it. After mutating the data I wanted to check my work and see what I was
plotting. I did all of this with this code:

``` r
NorCalCoffee <- cheap_coffee %>%
  mutate(Beans = paste(`Coffee Company`, `Subscription Model`, `Bag Size`, sep = " - "))

View(NorCalCoffee)
```

Note: If viewing in Microsoft Word Zoom to 250% on the plots

``` r
ggplot(data = NorCalCoffee)+
  geom_point(mapping = aes(x=`Cost per Cup`, y=`Beans`, color=`Beans`), size=5)
```

<img src="NorCal-Coffee_files/figure-gfm/visual-1.png" width="80%" style="display: block; margin: auto;" />

## More Cleaning and Manuipulation With A Plot

Here I loaded stringr first since I wanted to change 36 oz to 2.25 lbs
so it would match the format of the rest of the Beans. I did this using
a pipe to change from cheap_coffee to NorCalCoffee. I then added a
second pipe to get the change I had made into the label Beans and titled
the change CalCoffee. Then I used CalCoffee to make changes to the
x-axis label and to join both \$ per cup and \$ per pound of coffee.
Last, I plotted my changes to check my work.

``` r
library(stringr)
NorCalCoffee <- cheap_coffee %>%
  mutate(`Bag Size` = str_replace(`Bag Size`, "36 oz", "2.25 lbs"))

CalCoffee <- NorCalCoffee %>%
  mutate(Beans = paste(`Coffee Company`, `Subscription Model`, `Bag Size`, sep = " - "))

CalCoffee <- CalCoffee %>%
  mutate(`$ per Cup/$ per lb` = paste(`Cost per Cup`, `Price per lb`, sep = " / "))

ggplot(data = CalCoffee) +
  geom_point(mapping = aes(x = `$ per Cup/$ per lb`, y = `Beans`, color = `Beans`), size = 5)
```

<img src="NorCal-Coffee_files/figure-gfm/mutate and relabel x-axis-1.png" width="80%" style="display: block; margin: auto;" />

## Reordering Data in Plot

Here I am reordering the data that is in CalCoffee, so it will show in
descending order when plotted. I renamed the data CalCoffee1 so I could
distinguish between the changes I am making to my data. Next, I plotted
to check my work again, I added “stat = identity” to tell ggplot to use
the actual values to plot for the y-axis. I also used “geom_bar” to
change from a scatter plot to a bar graph. So, I added “coord_flip()” to
flip the bars of the bar graph from vertical to horizontal, matching the
labels for the x and y axes.

``` r
CalCoffee1 <- CalCoffee %>%
  mutate(Beans = fct_reorder(`Beans`, `$ per Cup/$ per lb`, .desc = TRUE))

ggplot(data = CalCoffee1) +
  geom_bar(mapping = aes(x = Beans, y = `$ per Cup/$ per lb`, fill = Beans), stat = "identity", size = 5) +
  coord_flip()
```

    ## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
    ## ℹ Please use `linewidth` instead.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
    ## generated.

<img src="NorCal-Coffee_files/figure-gfm/reorder data, change to bar graph-1.png" width="80%" style="display: block; margin: auto;" />

## Adding Annotations to My Plot and Resizing The Final Plot

Here I use Annotations to add the title, subtitle, and the caption. I
also rename the x and y axes for clarity of what is being shared here.
Lastly, I sized the final plot using “ggsave()”, so the plot will all
show when you open it in R Studio.

``` r
ggplot(data = CalCoffee1) +
  geom_col(mapping = aes(x = Beans, y = `$ per Cup/$ per lb`, fill = Beans), size = 5) +
  coord_flip() + labs(x="Coffee Roast", y="$ per cup/$ per lb",
                      title="NorCal Coffee Roasters: Price per Cup of Coffee and per lb for Each Roaster", 
                      subtitle = "Comparing Cost for 5 Different Beans", 
                      caption = "Data collected by Casey Dyer, May 2024")
```

<img src="NorCal-Coffee_files/figure-gfm/final plot-1.png" width="80%" style="display: block; margin: auto;" />

``` r
ggsave("Coffee.final.png",
       width = 16,
       height=8)
```

## Analyze The Data

Now, that we have a solid visual we can very easily start to answer the
questions from above:

#### \* What is the best price per lb of premium coffee that is from a Northern California Roaster?

To answer this question we can look at the plot and see that The
Seasonal Blends Subscription from Equator Coffee in the 2 lb bag offers
the best price per pound at \$22.40/lb.

#### \* What would the overall cost incurred be to subscribe to the subscription model that I decide to go with?

The overall cost could be calculated by taking the price per lb and
multiplying it by 2 (22.4x2=44.8) to get the cost per shipment, next to
get the total cost we need to know that the subscription model is for 3
months and that we would get a 2 lb bag per month. We can calculate the
overall cost of the 3 month subscription by multiplying the cost per
shipment by 3 months (44.8x3=134.4). The total cost comes out to
\$134.40 for 3 shipments of 2 lb bags of the Seasonal Blends
Subscription from Equator Coffee.

#### \* What would the cost be when factored down to each cup brewed in my personal Chemex for the subscription model I choose?

To answer this final question we can again look to the plot and see that
the top bar in pink annotates the cheapest subscription model factored
down to the cup. We see that the Seasonal Blends Subscription from
Equator Coffee in the 2 lb bag equates to \$0.84 when brewed in my
personal Chemex that uses 1.8 oz of beans per pot made. There are 3 cups
of coffee made with each pot so we can then see how we get to
\$0.84/cup.

## Thanks for Reading

Lastly, I collected all of this data on my own in May of 2024 from each
roasters website, I then added it into a spreadsheet. I have rendered
this data in many ways to create visuals to include Excel, Tableau, and
now R. This was fun to do, it answered questions that actually benefit
me and my wife, we get to drink great coffee that is delivered to our
doorstep that saves us some money. I look forward to learning more about
R in the future.

Thanks for checking this out!

- Casey Dyer
