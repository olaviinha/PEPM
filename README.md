# FinnStonks Personal Equity Portfolio Monitor

FinnStonks Personal Equity Portfolio Monitor provides a web-based, customizable, simple, compact, clean textual overview of the current state
of your equity portfolio for daily monitoring.

Made for [my own purposes](#motivation), so it is quite literally a _personal_ equity portfolio monitor. Probably best suited for _European sunday micro-investor hobbyists_ alike, who don't do trading on regular basis with large capitals, but are still interested in keeping an eye on their assets on a daily or weekly basis. 

In short, it takes your stock purchases and sales like this:
```
# BUYS
OR:FP;2020-09-04;5;288.34
SAA1V:FH;2020-02-12;40;12.85
SSH1V:FH;2020-08-04;2000;1.33
TSLA:US;2020-09-01;4;420.00
TL0:GR;2020-09-01;2;442.75
YEINT:FH;2020-06-15;80;14.92

# SELLS
TL0:GR;2020-12-08;2;525.70
```

...and turns them into this:

![Imaginary portfolio viewed on FINNSTONKS PEPM](https://storage.googleapis.com/olaviinha/github/pepm/finnstonks-3.jpg)
Example `stocks.txt` viewed on FINNSTONKS PEPM. Columns: owned shares, EUR invested, EUR changed since purchase, percent changed since purchase, daily change history for the previous 3 days in percent.

Stock purchases/sales may also be totally imaginary to explore your _shark hunch_ with different what-if scenarios using actual market data.

### Table of contents
1. [Used APIs and how to obtain required API key](#used-apis-and-how-to-obtain-required-api-key)
2. [Your trading data](#your-trading-data)
3. [User interface and how to use it](#user-interface-and-how-to-use-it)
4. [How to set it all up](#how-to-set-it-all-up) and [how to host your trading data in Dropbox](#protip-host-stockstxt-in-dropbox-for-easy-updating)
5. [Motivation](#motivation)

---

## Used APIs and how to obtain required API key

FinnStonks uses two APIs:
1. European Central Bank's [Foreign exchange rates API](https://exchangeratesapi.io) for up-to-date real-time
USD to EUR conversion.
2. Bloomberg's
[Market and Financial News API](https://rapidapi.com/apidojo/api/bloomberg-market-and-financial-news) 
for up-to-date real-time stock prices.

Bloomberg's API was chosen for stock data for a few reasons probably worth mentioning:
- It contains multiple exchanges worldwide, most importantly Nasdaq Helsinki and Nasdaq First North.
- It supports fetching market information of multiple companies with a single call, which is pretty neat when your usage is limited to 500 monthly API calls.

### Obtain API key

Bloomberg's Market and Financial News API requires an API key.

Register for a [RapidAPI](https://rapidapi.com) account
and subscribe to [Bloomberg Market and Financial News API](https://rapidapi.com/apidojo/api/bloomberg-market-and-financial-news) to 
obtain required API key.

Copy and paste API key to the beginning of `finnstonks.js`:
```
// rapidapi.com API key
rapidApiKey = 'PASTE YOUR RAPIDAPI.COM API KEY HERE';
```

### API limitations

At the time of writing this, Bloomberg's API is available for a Freemium plan, allowing 500 monthly API calls free of charge. 
Each refresh or pageload of FinnStonks consumes 2 API calls to fetch the current market information and 3 day price histories.

**This means you can refresh FinnStonks about 8 times a day, or, about once every 3 hours, providing that your copy of FinnStonks is 
open on a single device 24/7.** If your copy of FinnStonks is open on multiple devices (e.g. more than one person has access to it), it
will naturally reflect on the number of consumed API calls.

TODO: fetch price history only when day has changed since last pageload to save API calls.

## Your trading data

FinnStonks uses a simple `stocks.txt` file that should contain all your purchase and sale events in a simple delimiter-separated format.
Each line should read `<symbol:exchange>;<date purchased/sold>;<number of shares purchased/sold>;<purchase/sale price of 1 stock>`.
All additional fees regarding purchase or sale events should be calculated in the price per stock value.

Purchases and sales are separated by one empty line. Lines beginning with character `#` will be ignored, in case you want to play around with the data or ignore certain events for whatever reasons.

Example:
```
# BUYS
OR:FP;2020-09-04;5;288.34
SAA1V:FH;2020-02-12;40;12.85
SSH1V:FH;2020-08-04;2000;1.33
TSLA:US;2020-09-01;4;420.00
TL0:GR;2020-09-01;2;442.75
YEINT:FH;2020-06-15;80;14.92

# SELLS
TL0:GR;2020-12-08;2;525.70
```

## User interface and how to use it

You can safely ignore anything under this title, if you don't know what it means.

Most of the UI is configurable simply by clicking on things, even though there are a number of hard-coded settings in the javascript file (see [Settings](#settings)). To keep the UI as clean and simple as possible, I have opted not to include much descriptive information on the UI: See [user interactions](#user-interactions) for what is actually displayed on the table by default and what are the options.

### Prerequisites

- jQuery

Although Bootstrap and LESS are used for styling in `index.html`, they are not in any way required for `finnstonks.js` to run.

### Styling

All styling is freely customizable in `finnstonks.less`. You may use regular CSS or any styling you like,
modifying it should be pretty straight-forward.

### Settings

`finnstonks.js` contains a number of settings at the beginning of the file. Most should be pretty self-explanatory. Some settings that may not be self-explanatory:

- `var includeCashouts = true|false`: set to true if you want to take into account profits or losses you have made with previously owned stocks, that you have completely cashed out and no longer own. If false, any such assets are ignored in all calculations.
- `var effectiveDate = 'first'|'last'`: date to display if you have purchased shares of the same company on multiple occasions.
- `var alarmLimit = -1`: limit used to determine how many percent a stock can drop every day for 3 consecutive days before alarm styling is triggered.
- `var refreshInterval = 3`: interval in hours in which everything on page is auto-refreshed using real-time market prices. Set to `999999` or so if you don't want the page to auto-refresh at all.
- **Below** settings, you can find `var mockData = false;`. Set it to `true` when you do any development, such as styling or modify the Javascript. 
When `mockData` is `true`, data is mocked instead of fetched from Bloomberg's APIs. This way no API calls are consumed during development, when you probably refresh the page many times.

![Very compact FINNSTONKS PEPM](https://storage.googleapis.com/olaviinha/github/pepm/finnstonks-5.jpg)
`veryCompact` set to `true`, `showCashouts` set to true, everything clickable clicked once, to change what information is displayed.

### User interactions

Most of the UI is configurable simply by clicking on things.

**1. Click on _investment total_ value (top left) to toggle between:**
  - total euros invested. (Default)
  - total euros invested minus any previous sales profits/losses (current true cash loss).
  
**2. Click on _change_ value (top center) to toggle between:**
  - portfolio value change in euros. (Default)
  - portfolio value change plus any previous sales profits/losses in euros.
  - portfolio value change in percentage.
  
**3. Click on the _portfolio value_ (top right) to toggle between:**
  - portfolio value in euros. (Default)
  - portfolio value in euros plus any previous sales profits/losses
  - previous sales profits/losses only (no portfolio value)
  
**4. Click on the second column to toggle between:**
  - euros invested. (Default)
  - euros invested minus any previous sales profits/losses (current true cash loss).
  
**5. Click on the third column to toggle between:**
  - change in euros. (Default)
  - change in percentage.
  
**6. Click on the fourth column to toggle between:**
  - change in percentage. (Default)
  - current market value in total.
  
**7. Click on the last column to toggle between:** 
  - change in percentage. (Default)
  - change in euros.
  
The last column of the table shows the stock price change for the last three days. Each number means change from previous day.

## How to set it all up

1. Clone or download this repository.
2. Replace the example lists in `stocks.txt` with a list of your own, actual purchase and sale events: [instructions](#your-trading-data).
3. Obtain required API key: [instructions](#obtain-api-key).
4. Open `finnstonks.js` in a text editor, paste your API key in there and change any of the settings you wish.
5. Upload all files to a SSL secured server or run a http server on localhost (such as [http-server](https://www.npmjs.com/package/http-server)). You cannot run FinnStonks by `file://` protocol.
6. Whenever you perform a purchase or a sale, just add it to the corresponding list in `stocks.txt`.

### #protip: Host stocks.txt in [Dropbox](https://www.dropbox.com) for easy updating.

Once you have FinnStonks up on a server, you may also keep `stocks.txt` in your [Dropbox](https://www.dropbox.com), where it's possibly considerably easier to keep up to date than on a regular web server. Any other such cloud storage file sync apps are currently untested, but may work just as well (as long as it can produce an URL to the raw file with required CORS headers).

1. Place `stocks.txt` somewhere in your Dropbox.
2. Right-click it and select _Copy Dropbox Link_.
3. Edit `finnstonks.js` and locate line `var tradeEventsTxt = 'stocks.txt';` somewhere at the beginning.
4. Replace `stocks.txt` on that line with the Dropbox Link from your clipboard and save.

End result should look something like this:
```
var tradeEventsTxt = 'https://www.dropbox.com/s/b666pwrytk1pepm/stocks.txt?dl=0';
```


## Motivation
There are countless stock market monitors out there that enable you to follow stock prices of your choosing. However, apart from the
platform you've used to purchase your assets, very few seem to provide any ability to follow stock prices even _as if_ existing assets
(_number of stocks_, _starting point_). Even fewer an entire equity portfolio containing multiple companies, not to mention taking into 
account any further trading events, doing any calculations or customizing layout or the data shown. Furthermore, one may do different 
tradings on different platforms and lose the overview of the whole.

Hence an Excel spreadsheet is still a good friend of any stock investor. The purpose of FinnStonks is to replace all that hassle with an 
automated, clean, browser-accessible screen. All you do is add the event to the bottom of the list when you've purchased or sold some stocks
in spite of the used platform.
