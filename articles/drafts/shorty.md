I always wanted my own short-domain...

You know, those user-friendly [youtu.be](http://youtu.be) links when you share a video, that redirect you to the full URL:

`youtu.be/KgJUNYS7ZnA` → `https://www.youtube.com/watch?v=KgJUNYS7ZnA`

## Principles

I wanted something:
1. **simple**<br>
   after all it's all about a 3xx server response
2. **cheap**<br>
   I didn't want to spend more than a few bucks /month for this
3. with a **public database** of shortened-links<br>
   this is particularly important because we know how online services are fragile, and doomed to go down at some time.

## Domain

I chose a domain: `abernier.link`

- `abernier` being my nickname for years
- and the [`.link` tld](https://icannwiki.org/.link) appearing quite appropriate for this use

I purchased it on my historic registrar: [gandi.net](https://gandi.net)

## Code

I created a public Github repo to host the source code: [`abernier/shorty`](https://github.com/abernier/shorty)

I opted for a _plain old_ Express [`server.js`](https://github.com/abernier/shorty/blob/main/server.js): proactively boring and simple here.

## Database

The simplest form I found: a [`urls.CSV`](https://github.com/abernier/shorty/blob/main/urls.csv) file

As a bonus, Github:
- easily allows us to directly edit the file online
- nicely displays the CSV file as a readable table

<img width="733" alt="image" src="https://user-images.githubusercontent.com/76580/211223888-f8346658-0fc8-4f30-b0f3-c1618985c992.png">

## Deployment

I needed
