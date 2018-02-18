---
layout: post
title: Airbnb Listings in Berlin
tags: tableau all
---


I am going to look on data made available by Airbnb for their Listings in Berlin ([link](http://insideairbnb.com/get-the-data.html)). I will be looking at this dataset from an owner’s perspective. Say I want to buy a property and list it on Airbnb, but I’m not sure what price, in which neighbourhood would be appropriate, don’t even know yet what type of room I could offer for short term rent. So I have prepared a dashboard with an overview of situation.

I added a filter that excluded outliers - prices above 2000 EUR (can be found in the sheet Outliers once you download the workbook from Tableau Public). Finding outliers with 1.5 IQR calculation would point to excluding listings with price above 127.5 EUR, over 1000 of data values. I decided to leave the limit at 2000 EUR, to show the diversity that is very characteristic to real estate prices, especially in the context of location.

<style>
.responsive-wrap iframe{ width: 700px; height: 600px}
</style>
<div class="responsive-wrap">
  <iframe src="https://public.tableau.com/views/AirbnblistingsinBerlin2/AirbnbListingsinBerlin?:embed=y&:display_count=yes"></iframe>
</div>

[Link to Tableau Public](https://public.tableau.com/profile/dorota5520#!/vizhome/AirbnblistingsinBerlin2/AirbnbListingsinBerlin)

There are 17 810 of hosts in Berlin that together list 20 572 places where you can leave your stuff and spend the night in Trezor. Alternatively sleep there too. 

At first glance we can see that you’re most likely to find a Private Room or an Entire home/apartment. Shared rooms are just a fraction of all Listings.

The Listings are clustering around centre of the city. Looking at the map we can see that in general the cheapest places can be found just outside the city centre, the ones with a mid-range price are located in the heart of the city, and lastly the most expensive places are rather away from the centre. The average price for a night in Berlin stays at a 64.82 EUR.

Filtering by Room Type (just click on the part of a pie chart) allows to see more details. Entire home/apt is significantly more expensive, by 20 EUR than the general average, while the price difference between Private and Shared room is, surprisingly, only 5,13 EUR. One way to explain is the fact that Shared rooms are located in city centre only, charging a premium for a top spot, while Private rooms are visibly more scattered around whole city, pushing the average price down.

Let’s check if this data supports such conclusion and look at the most expensive Neighbourhood for Shared rooms - it is Neukoeln and prices reach the level of 51,98 EUR. On the map we clearly see that those few places are located closely to the centre, whereas for Private rooms this Neighbourhood is filled starting from the centre and reaching as far as to the borders of the city, The average price is also much lower - 32,83 EUR. I have to point to an extreme value here in this Neighbourhood, a Shared room with a price of 349 (!) EUR per night, that strongly impacts the average price. Still, a similar relation is sustained for next most expensive Neighbourhood for Shared rooms - Tempelhof/Schoeneberg. 

The last chart is what seems very interesting and immediately a question arises: Why so many listings have little or no reviews? First explanation that comes to mind is that they are probably too recent and simply didn’t manage to receive any guests yet. The data I’m using was published on 8th of May 2017, I think it makes sense now to filter out Hosts that first logged in in 2017.


<style>
.responsive-wrap iframe{ width: 700px; height: 600px}
</style>
<div class="responsive-wrap">
  <iframe src="https://public.tableau.com/views/Listingsvs_Reviews-AirbnbBerlin/Listingsvs_Reviews-AirbnbBerlin?:embed=y&:display_count=yes"></iframe>
</div>

[Link to Tableau Public](https://public.tableau.com/profile/dorota5520#!/vizhome/Listingsvs_Reviews-AirbnbBerlin/Listingsvs_Reviews-AirbnbBerlin)

The relation is clear - the higher the Number of reviews, the lower the percentage of all Listings. The biggest group of Listings with no reviews are those set up in 2015. Not surprisingly, as this was the year when new Listings recorded their peak and almost 5k new Hosts logged in. The second biggest group are Listings from 2016 and then 2014, this structure is more or less maintained regardless of Number of reviews. This does not explain why the “no reveiew” Listings are so numerous.

I tried to find some characteristics that would differentiate listings with high and low number of reviews. In most cases the structure was similar just as in the case described above. There were two measures however that did allow to divide the Listings in such two groups - average Host Total Listing Count and average Security deposit.

In general, Host Total Listing Count of 2 and above matches with Listings that have 12 reviews and higher. Such conclusion could be easily justified: 
A host that has more than one Listing takes Airbnb more seriously as it has potential of bringing more money.
Additionally, detailed and professionally prepared offers bring more visitors as well.

Another observation regards security deposit. Listings with the lowesr Number of reviews have significantly higher required security deposit - over 300 EUR. As this value decreases, Number of reviews is rising.
It seems as if it could be a major disqualifying factor for many visitors. On the other hand, host with no Airbnb experience may find it hard to trust such service and calculate this uncertainty as security deposit. Possibly after few successful transactions they are ready to accept lower financial protection. 
Interestingly, relation between price and Number of reviews doesn’t have such clear and visible pattern.




