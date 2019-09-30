# ak47-skin-prices
Data wrangling with R (tidyverse). Video game assault weapon skin prices throughout time.

After downloading history data of skin prices of a specific assault weapon for the online competitive video game Counter-Strike: Global Offensive, the CSV file looks something like this:

![pic1](https://raw.githubusercontent.com/dallasferraz/ak47-skin-prices/master/pic1.png)

Knowing that the prices vary according to offer and demand fluctuations that look similart o most goods in real life, the players posing as vendors and buyers, what looks chaotic at first make some patterns stand out. For instance, it is easy to see that there is some kind of embedded data inside the square-brackets, which in turn are separated by comma. Inside the square-brackets, it is easy to see that quotation marks are used to separate information even further into smaller chunks that look like date time information and some sort of whole number and float numbers.