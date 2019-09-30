# ak47-skin-prices
Data wrangling with R (tidyverse). Video game assault weapon skin prices throughout time.

After downloading history data of skin prices of a specific assault weapon for the online competitive video game Counter-Strike: Global Offensive, the CSV file looks something like this:

![pic1](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic1.png)

Knowing that the prices vary according to offer and demand fluctuations that look similar to most goods in real life, the players posing as vendors and buyers, what looks chaotic at first make some patterns stand out. For instance, it is easy to see that there is some kind of embedded data inside the square brackets, which in turn are separated by comma. Inside the square brackets, it is easy to see that quotation marks are used to separate information even further into smaller chunks that look like date time information and some sort of whole number and float numbers.

In this case, even before getting it out in RStudio, I replaced all square brackets with the comma separating them with semicolons in the Notepad itself. Obviously, the first and the last square brackets had to be replaced manually, but this was easy:

![pic2](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic2.png)

And then the data started looking friendlier:

![pic3](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic3.png)

After this step, the data was all set for the RStudio.

```{r}
library(tidyverse)

ak47 <- read.csv("~/ak47_bluelaminate.txt", header=FALSE, sep=";")
```

![pic4](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic4.png)

The problem is that data manipulation for analysis is harder whenever the information is stored in this format, with several columns and few rows. There are some benefits to this shape but none that I can think of, when it comes to producing graphs or extracting insights from this dataset. In this case, I transposed the matrix and all columns became rows.

```{r}
ak47 <- as.data.frame(t(ak47))
```

![pic5](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic5.png)

Now it's time to split the column into at least 3 different columns. It is easy to see that there are composed data inside each row. Since it's about prices throughout time, it is quite easy to see that the first part of the information is a timestamp, whereas the second part is probably related to the price at that moment and the last bit of information concerns the number of items sold in each instance. For this reason, I divided column **V1** into three other columns, namely **timestamp**, **price** and **quantity**.

```{r}
ak47 <- ak47 %>% separate(V1,c("timestamp","price","quantity"),",")
```

![pic6](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic6.png)