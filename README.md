# Meta Critic Scraping Bot

The purpose of this automatic scraping bot is to use its pre-programmed list of 9 complete urls to return the partial urls of 850+ movies which are found in the 9 individual pages.

This document will explain the purpose, functionality, and difficulties of my Metacritic scraping bot.

## Purpose

The purpose of this bot is to find the
-  Title
- Metascore
- Userscore
- Release date
- Director
- Genre(s)
- Cast

Of each movie listed in the “Movie Releases by Score – Available on Netflix” directory on the Metacritic website. Once the bot has found these 7 attributes for each movie in the directory, the data is written to CSV file for easy viewing and analysis. Given that there 850+ movies in this directory, this CSV file will be useful to those who want rapid basic information of all the movies available on Netflix without having to tediously go through each of those individual movie webpages.


## How it Works

### Box 1

Imports the necessary modules to make the program work.

### Box 2

**url_list**

This variable contains the 9 complete urls of the pages that need to be scraped first.

### Box 3

This section of code is responsible for collecting the 850+ links which are found across the 9 webpages stored in *url_list*

**def get_urls***

The function *get_urls* is responsible for loading each of the 9 webpages (one by one) contained in *url_list* using Selenium.
On each of the 9 webpages, there are 100 relative links, each pertaining to a specific movie. The first part of the function loads each webpage and captures it. BeautifulSoup is then used to collect all the 100 links on each of the 9 directory pages that lead to the individual webpages for each movie.

Within the function, a for loop appends the 100 links found on each webpage to the variable *links* which is outside the function.
Each time the function runs this process is repeated.

The function is called by for loop which will run the function once for each url in *url_list* (9 times total). After the functions runs 9 times, all of the links on each of the 9 webpages will have been appended to the variable *links* (Aprox. 852 links/items)

### Box 4

This box will confirm the progress of the code in Box 3.
The code in this box will print all the links collected in the previous steps and print the total number of links.

### Box 5

The code in Box 5 uses each of the partial links collected in Box 3 and stored in *links* to find the 7 attributes listed above from each movie.

**def get_details**

This function launches the selenium browser for each movie link, collects the necessary attributes, and appends them to a list.

*try except* are used in each instance when trying to collect the attributes from the movie webpage. Given that not all movies have all 7 of the attributes, *try except* allows the program to try to capture the data. If no data is found for that attribute, instead of crashing, the program returns ‘Not Available’ and moves on.

Each collected attribute is stored in a variable named accordingly to its value
Example: `director = James Cameron`

Each variable contains one of the 7 attributes.

Each variable is then appended to list variable *full_movie* so that that variable *full_movie* contains 7 different individual strings of data.

Certain movie attributes such as cast, and genre are stored in variables which are lists. These variables must first be turned into a string before they can be appended to the *full_movie* variable.
Example: `temp = ', '.join(str(e) for e in starring2)`


The function returns the *full_movie* list variable which now has 7 different items in it, each a movie attribute. (Title, Release, Metascore, Userscore, director, starring, and genre)

The function will run once for each individual movie url for a total of approximately 852 times.


**def write_csv**

This function writes the data contained in the variable *full_movie* into a CSV document.
First, the function creates a new CSV file and writer object.
Then, the header row is written.

The for loop in this function is the crucial piece of code that makes everything work together.

1. It concatenates the relative url paths in *links* into the full url which will be passed to *def get_details*
2. Calls the function *get_details* and stores its returned result in a variable named *full_movie*
3. Writes each item in the returned list variable *full_movie* into the CSV file. This process will be repeated for each link in the list variable *links*. Approximately 852 times.

## Unexpected Problems

While writing the code for this scraping bot, I ran into a few unexpected issues.

The first issue was that the cast names on each movie webpage were not written as normal text, instead each name was a link to different webpage. Each name was a separate `<a>` element. Example: `<a href="/person/candace-smith">Candace Smith</a>`

To solve this, I collected all the <a> elements that contained the name of the cast members, I then looped through that list and appended only the text to a new variable (*starring2*).
I later had to turn that list into a string so it could be appended to the variable *full_movie*.

The second issue I ran into was that the CSS classes on each webpage weren’t always the same. For example, the MetaScore and Userscore classes are different on each webpage depending on their color. 3 colors are possible on any webpage for those two attributes.

I solved this issue by creating three different *try except* arguments that would try each CSS class and see if one of them worked. This prevents the program from crashing and allows the bot to collect the data from all the webpages regardless of the color of the score.

The third problem that  I ran into is that sometimes the page fails to load completely. I had to use *try except* in order to prevent the program from shutting down if the page failed to load.

**The CSV file contains multiple instances of the page failing to load.
I ran this program a total of 3 times and sometimes pages would fail to load more often at certain hours of the day compared to others.

**I have included two CSV files. One CSV file is the result of the program running on Thursday. It scraped 855 movies.
The second CSV file is the result of the program running on Friday. Two movies must have been removed because it only scraped 853 links.
As of Friday 3/12, the file named "metacritic_scrape" with 853 movies is the most accurate/up to date.**
