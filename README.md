# Modern Web Scraping Section 9: Crawlers Using Scrapy Part 7: Crawler Assessment
## Objective:
Crawl 'http://books.toscrape.com/' to get all the books
Steps include:
Extract all the book links
Open each one
Then within each one, extract:
- the book name 
- the price
Save the data as a JSON or CSV file
# Following are the steps used to create this crawler spider
## Check permissions 
Check the robots.txt
http://books.toscrape.com/robots.txt
404 Not Found so we are good to go
## Setup
Open Anaconda Prompt from Anaconda Navigator in the virtual workspace environment

### Create new database
In Prompt, create a new database: scrapy startproject books
Enter the new database: cd books
### Generate the spider 
Start by typing: scrapy genspider
To specify what template of spider type: -t
And then the name of the target template: crawl
Then the name of the spider, in this case: all_books
Then the web address: books.toscrape.com
We are going to change that later since we are going to use the back in time machine because the website changed since instructor made this video 
So, all together: scrapy genspider -t crawl all_books books.toscrape.com
Open VS Code from Anaconda Navigator in the virtual workspace environment
## Set up the first rule
### Write xpath expression to get all book links
#### Within your browser:
Crtl + shift + i to open developer tools
Select book title because we can see, in this case, that is also the book link
Crtl + f to open the search box
Search the h3 element
Within that, select all the “a” elements 
Looks like this://h3/a
That returns 20 findings which makes sense because the page says books 1-20
Crtl + c to copy
### Within VS Code:
Inside the LinkExtractor object, set restrict_xpath is equal to parentheses, double quotes, paste expression
So it looks like this: rules = (
    Rule(LinkExtractor(restrict_xpaths="//h3/a"), callback='parse_item', follow=True),
    )

### Parse_method
Delete everything
Type: print(response.url)
Crtl + s to save
### Test run
In VS Code, on the horizontal menu, select Terminal > New Terminal 
Type: scrapy crawl all_books
This returns all the book links for the first page, means our crawler is working 
## Pass the response we get from each request sent to a book link
### In developer tools:
#### Select title
The next step is to pass the response we get from each request sent to a book link
To start, in Chrome, on the books webpage click one of the books names, like “A Light in the Attic” That takes us to the page specifically about “A Light in the Attic”: http://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html
Crtl + shift + i to open developer tools 
Get the inspector tool and select the book title then figure out how to select that element
In the search bar, i typed: //div[@class='col-sm-6 product_main']/h1/text()
Copy the XPath expression
### In VS Code:
#### In the def parse_item:
Delete the print(response.url) statement
Type “yield” then curly brackets
Inside the curly brackets, enter what we are looking for:
title : then “response.xpath” and in parentheses, the expression we just copied, then the get method, then add a comma
Lookslike this:     def parse_item(self, response):
        	yield {
            'title' : response.xpath("//h1[@data-testid='hero-title-block__title']/text()").get(),
Duplicate the line once with alt + shift + down arrow
Change the second key to be price
### In Developer tools:
#### Select price:
Crtl + shift + i to open developer tools 
Get the inspector tool and select the book price
In the search bar, i typed: //div[@class='col-sm-6 product_main']/p[@class='price_color']/text()
Copy the XPath expression
### In VS Code:
#### In the def parse_item:
In the second key of “price”, change the xpath expression to what you just copied
Looks like this: 'year' : response.xpath("//ul[@data-testid='hero-title-block__metadata']/li[1]/span/text()").get(),Crtl + s to save
### Test run
In the Terminal, type cls to clear 
Type: scrapy crawl all_books -O books_dataset.csv
This returns all the book titles and prices for the first page, means our crawler is working!
## Follow link pagination
### In browser
 on the first page of http://books.toscrape.com/ has 20 books and a button that says “next”
Open the inspector tools and highlight the “next” link
In the search bar, i typed: //li[@class='next']/a
Copy the XPath expression
### In VS Code
#### Make a new Rule object
Within the rules = parentheses, after the first Rule, start with Rule (with a capital R) and then parentheses
Argument #1
Within the parentheses, type LinkExtractor and then open a new set of parentheses
Within those parentheses, type restrict_xpath=”<paste what we copied from Inspection tools>”
Looks like this: Rule(LinkExtractor(restrict_xpaths="(//a[@class='lister-page-next next-page'])[1]"))
Argument #2
Usually callback method but you don't need it in this rule
Argument #3
Usually follow=true but you don't really need to do this one because we are not going to specify a callback method and the default will be set to true. 
Crtl + S to save 
## Run
In the Terminal, type cls to clear 
Type: scrapy crawl all_books -O books_dataset.csv
This returns all 1000 book titles and prices!
