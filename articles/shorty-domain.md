# Shorty domain

I always wanted my own short-domain...

![image](https://user-images.githubusercontent.com/76580/211225629-a5a56422-cfae-47b2-9742-baab67ef6beb.png)[^1]

[^1]: Credits: https://www.flickr.com/photos/chmurka/2524849923


You know, those user-friendly [youtu.be](http://youtu.be) links when you share a video, that redirect you to the full URL:

```mermaid
flowchart LR
    youtu.be/KgJUNYS7ZnA --> https://www.youtube.com/watch?v=KgJUNYS7ZnA
```

## Principles

I wanted something:
1. **simple**<br>
   After all it's all about a 3xx server response
2. **cheap**<br>
   I didn't want to spend more than a few bucks /month for this
3. with a **public database** of shortened-links<br>
   This is particularly important because we know how online services are fragile, and doomed to go down at some time.

## Domain

I chose a domain: `abernier.link`

- `abernier` being my nickname for years
- and the [`.link` tld](https://icannwiki.org/.link) appearing quite appropriate for this use

I purchased it on my historic registrar: [gandi.net](https://gandi.net)

## Database

The simplest form I found: a [`urls.CSV`](https://github.com/abernier/shorty/blob/main/urls.csv) file.

As a bonus, Github:
- easily allows us to directly edit the file online
- nicely displays the CSV file as a readable table

<img width="733" alt="image" src="https://user-images.githubusercontent.com/76580/211223888-f8346658-0fc8-4f30-b0f3-c1618985c992.png">

## Code

I created a public Github repo to host the source code: [`abernier/shorty`](https://github.com/abernier/shorty)

I opted for a _plain old_ Express [`server.js`](https://github.com/abernier/shorty/blob/main/server.js), proactively boring and simple here:

```js
import express from "express";
import csv from "csvtojson";

const app = express();

//
// DB (from CSV)
//

const jsonArray = await csv().fromFile("./urls.csv");
const db = new Map(jsonArray.map(({ short, original }) => [short, original]));

//
// all-route
//

app.get("*", async (req, res, next) => {
  const short = req.url;

  const exist = db.has(short);
  if (!exist) return next(); // Not found

  // Redirection
  const original = db.get(short);
  res.redirect(original);
});

// 404
app.use((req, res, next) => {
  res.status(404).send('Not found');
});

const port = process.env.PORT || 3000;
app.listen(port, () => console.log(`Server is running on port ${port}`));
```

## Deployment

1. A [`Dockerfile`](https://github.com/abernier/shorty/blob/main/Dockerfile) describes recipes to build a Docker image of the server.
   ```Dockerfile
   FROM node:18-alpine
   
   COPY package.json package-lock.json ./
   RUN npm ci
   COPY . ./
   
   CMD ["node", "server.js"]
   ```
2. A Google [Cloud Build](https://cloud.google.com/build) `trigger` re-builds and re-deploys the Docker image, when `main` branch is updated
   <img width="1468" alt="image" src="https://user-images.githubusercontent.com/76580/211224628-14f44287-2109-4144-a84e-3a8895661adc.png">
3. A Google [Cloud Run](https://cloud.google.com/run) `service` runs this Docker image
   <img width="1468" alt="image" src="https://user-images.githubusercontent.com/76580/211224566-6a213929-840f-44fd-ad9d-91f3ffb42142.png">
4. a Gandi Web-redirection forwards HTTP requests to the Cloud Run instance
   <img width="1468" alt="image" src="https://user-images.githubusercontent.com/76580/211224921-1d0e25cc-0295-4a73-9ac7-7bed4c49a748.png">

## Result

This article is available at: [abernier.link/shorty-domain](http://abernier.link/shorty-domain)
