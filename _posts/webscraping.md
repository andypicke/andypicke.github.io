

This post is about web scraping using python, which i’ve been learning about recently. Sometimes we need data from webpages that is not stored as a downloadable file, or easily accessible through an API. In that case, we need to ‘scrape’ the data from the webpage. Note that some websites have regulations about web scraping, so always check first. In general, you’re ok as long as you’re not using the data for commercial purposes or inundating the website with too many requests. 

https://benbernardblog.com/web-scraping-and-crawling-are-perfectly-legal-right/

There are lots of web scraping articles out there, two of which I learned from and used in this exercise:

- https://medium.freecodecamp.com/how-to-scrape-websites-with-python-and-beautifulsoup-5946935d93fe

- https://medium.com/k-folds/scraping-for-data-a-practical-guide-67cc397450b2

Here i’ll go through an example of how to scrape the weather (text) forecast from weather.com . 

Open the url in your browser (i’m using chrome) and right click on ‘inspect element’. This will display the html code that produces the page on the right. Unless you’re a web developer, this will probably seem like gibberish to you. And even if you know html, it is difficult to find the piece of code for the item you are trying to scrape. Luckily the inspect tool lets us hover over or select the part of the page we want, and displays the corresponding html on the right. In my chrome browser, this is done by clicking the little arrow on the top left of the html panel). Once we determine the tag we need, we can write the python code to retrieve it.

[images/weather_scraping_screenshot.png]

The basic workflow is:
- Get the url of the page you want to scrape from
- Use the browser’s inspect tool to get the tags or ids of the parts you want to scrape
- Read the html w/ url open
- Parse it with BeautifulSoup, then extract the parts you identified earlier.