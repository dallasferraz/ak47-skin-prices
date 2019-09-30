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

Now focusing on **timestamp** column, it seems like the information in it can be split even further. There is obviously a record of the year, month and day the transaction happened, but there is also some information regarding at what time it occurred. It is interesting to notice that, despite belonging to the same dataset, the data content changes abruptly in the 2079th row: before that, every single entry corresponded to a specific day and the timestamp is only meaningful regarding the date, but the time is irrelevant. After the 2080th entry, each row corresponds to an hour of the day (which is recorder 24 times, accordingly). It also shows on the column **quantity**, that was registering the cumulative sum of all trades of the day and its values fluctuated around 100 or more units. After the 2080th entry, it registers mostly less than 20 per transaction (and this really contributes to the theory that it stopped being cumulative and started recording each transaction):

![pic7](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic7.png)

Before dealing with this problem, I separated the data from **timestamp** into two other columns: **record_date** and **record_time**. It is wise avoiding naming your variables and columns `time` or `date` because you might end up using reserved words (even though you're sure this is not a reserved word in the language you're working in, you can never be sure people won't migrate your code to other languages):

```{r}
# since the number of characters of the date is fixed, I separated them at the 12th charater

ak47 <- ak47 %>% separate(timestamp,c("record_date","record_time"),sep=12)
```

![pic8](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic8.png)

Even though there is some fixing to do in the **record_time** column, I'll leave it for now and focus on **record_date**. I want to format it as in the `YYYY-MM-DD` format, so I will need some extra steps. First, I'll create 3 other columns, for years, months and days. Then, I united them in one column again:

```{r}
ak47 <- ak47 %>% separate(record_date,c("month","day","year")," ")

for(i in 1:2802){
  if(ak47[i,1]=="Jan") ak47[i,1] <- "01"
  if(ak47[i,1]=="Feb") ak47[i,1] <- "02"
  if(ak47[i,1]=="Mar") ak47[i,1] <- "03"
  if(ak47[i,1]=="Apr") ak47[i,1] <- "04"
  if(ak47[i,1]=="May") ak47[i,1] <- "05"
  if(ak47[i,1]=="Jun") ak47[i,1] <- "06"
  if(ak47[i,1]=="Jul") ak47[i,1] <- "07"
  if(ak47[i,1]=="Aug") ak47[i,1] <- "08"
  if(ak47[i,1]=="Sep") ak47[i,1] <- "09"
  if(ak47[i,1]=="Oct") ak47[i,1] <- "10"
  if(ak47[i,1]=="Nov") ak47[i,1] <- "11"
  if(ak47[i,1]=="Dec") ak47[i,1] <- "12"
}

ak47 <- ak47 %>% unite(date_correct_format,c("year","month","day"),sep="-")
```

![pic9](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic9.png)