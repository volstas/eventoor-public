# Premise
I made [eventoor.com](https://eventoor.com) primarily to learn SvelteKit and Cloudflare, and just as an opportunity to test tools and libraries I haven't tried before. I also wanted to build something end-to-end, from nothing to fully functioning and deployed in production.

As for what it is—it solves a simple issue: sharing dates in a global world. I was frustrated every time I saw a tweet, a post, or a Discord message saying that something will happen at "X time in Y timezone." It made no sense to me to ask people to convert timezones manually. 

My goal was to build a free, super simple, quick way to create and share these types of events. As a bonus, I added calendar integration so people can easily add it to the calendar of their choice and get notified within their native workflow.

While I'm not actively maintaining it, there are people who are using the tool, and it's chugging along nicely on the free tier of Cloudflare due to the way it was built.
As there's no ads, I optimized everything for the free cloudflare plan and now it can serve tens of thousands of events monthly without ever hitting the limits.

# Open source?
I didn't really build it for open source, so I won't make it public, but I will elaborate on the architecture here.

# Architecture

## eventoor.com
SvelteKit application deployed as Cloudflare Pages & Workers. It has two parts:

* **Homepage**
    We pre-render it so that Cloudflare can statically serve it. This is important because static serving has unlimited free bandwidth and requests.

* **REST API**
    A single endpoint to create events. It does validation and creates a short URL by which the full event is retrieved. This obviously hits the Cloudflare Worker and database, so it uses some of our limits from the free plan.

## evntr.cc
Separate SvelteKit application deployed as Cloudflare Pages & Workers.

* **Event Rendering**
    This is used for rendering the event from the short URL. To minimize the worker/database requests, we use CDN caching. Data does not change after creation, so we CDN cache it for a maximum amount of time. Because of this, if this page gets updated, we end up needing to purge the cache, but that's fine.

## **D1 Database**
Used this to store the "short URL to event data" mapping. SQL database is a bit of an overkill, but I wanted to try out serverless SQL as I've already used various key-value serverless databases before.

## **Networking**
Using the domain management services from cloudflare for certificates. Various cache and redirect rules to improve the experience. 


# CI/CD & Infrastructure

Set up to have local environment, staging, production. Automatic deployments from commits to master branch. 
Infrastructure as a code using Wrangler. Managing of variables and secrets for different environments.


# Tools used

* **SvelteKit**
    Primary framework I wanted to learn. I love the way reactivity is done and the runes system. Plus, I was very curious about a framework that does both frontend and backend, instead of the usual separation where frontend is done in React/Vue and the backend is in Node.js or a non-JS framework. SvelteKit felt like a very elegant way to do both.

* **Trix**
    This is a rich text editor. I actually ended up forking it and modifying it to fit my needs better. Especially cruciar was fixing pre-rendering for trix as that was a hard requirement for me. You can see the changes here: [trix-ssr](https://github.com/volstas/trix-ssr).

* **Drizzle**
    Haven't used a TypeScript-based ORM before, and this one seemed like the new hot thing. While my setup is very simple, it still was interesting to have an ORM and automatic migrations setup.

* **Tailwind CSS**
