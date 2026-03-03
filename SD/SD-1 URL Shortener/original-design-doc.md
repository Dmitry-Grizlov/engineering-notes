# URL Shortener

## Overview
A service that gives shorter links instead of a long parametrized ones
Example: 
```
Before: https://www.some-website.com/segment1/segment2/segment3/segment4/segment5
After: https://www.serv.ice/abc123
```

## Requirements
- User authentication, authorization so users only have access to their links (or can be without users, but won`t be eable to modify/delete links. Makes it easier, but will need to implement mechanism for data cleanup and check links expiration)
- User can enter their link and get short version
- When the user opens the link, it redirects them to the original website
- Redirect should work fast
- Server, that will recieve process link requests, find original links and redirect
- Data storage
    - MySQL for all records (links, users, etc.)
    - Reddis for fast access to the most used links
- Observability to easier identify bottlenecks
- Analytics to define the most popular links
- Horizontal-scaling "friendly": it should be easy to spin up new servers to distribute the workload

## Scale Assumptions
Don't understand how and what to count/write (lack of knowlege)
My vision here is simple, make it work and scale horizontally as needed. If it is not enough, look into bottlenecks and scale/update technologies (e.g. add more dbs,use db sharding, increase cache)

## API Design
For MVP
Create URl endpoint
```
POST
https://www.serv.ice/link

Payload:
{
    original_url: string,
    link_display: string // this is optional, but can be done that if the link is not occupied user can define the url e.g. instead of serv.ice/abc123 it will be serv.ice/business_name/product_code
}

Response:
{
    link: string 
}
```

Get Endpoint that users will actually click to go to the original website
```
GET
https://serv.ice/abc123

Resposnse:
original link page
```

## High-Level Architecture
- URL Generation Service
- Redirect Service (can be one service with generator, but I`m pretty sure generator wont have a huge workload so it is better to extract it)
- Gateway (api users interacts with)
- Database (MySQL, Postgre, SQLite etc)
- Cache (Redis)

## Data Model
Table: *link*
- link_id
- original_url
- short_url
- created_at
- expires_at

Table: *usage*
- link_id
- click_count
- TODO: Add more things here as needed to track characteristics (e.g. how often it is clicked, from where it is mostly clicked (what country/region), etc.)

## Short Code Generation Strategy
### UUIDs approach
Generate UUID for each new link

### Composite Identifier
Generate UUID using the timestamp + some other property that will guarantee uniqueness

## Redirect Flow
- Server recieves GET request (let's say serv.ice/abc123) -> 
- Server checks cache first -> 
    - Record found -> redirect to the original URL
    - Record not found -> search record in the db
        - Record in DB found -> cache it and redirect to the original URL
        - Record in the DB not found -> return 404
    - Record expired -> mimic not found behavior - return 404

## Caching Strategy
- Key-value pair to store everything:
    ```
        key: short URL without host, e.g. serv.ice/abc123 -> abc123
        value: hash of the object {original_url: "<original_url>", expires_at: <unix_timestamp>}
    ```

- Key-value pair to store links + another pair to store expiration (sounds expensive):
    ```
        key: short URL
        value: original URL
        key: short URL
        value: expiry timestamp
    ```

## Scaling Strategy
- Horizontal scaling
    - deploy more servers for url generation or redirect as needed
    - Increase storage size as data grows
    - Increase cache size as more data gets cached
- Queries optimization
- Add more db instances to distribute the load
- Switch to technologies that will compensate bottlenecks

## Failure Modes
- Redis failure: 
    - Fallback to DB
    - Restart Redis
- DB failure: 
    - Fallback to redis, if available
    - Restart db
- Server Failure
    - Compensate by multiple server instances running in parallel, redirect workload to the available server

## Bottlenecks
- With big amount of links (millions records stored), if record is not cached, it will take long to get the data from the db
- High requests amount will give hard time to the redirect service (can happen something similar to DDOS)

## Observability
- Graphana (visualize data)
- Prometheus (collect data)
- Google analytics (collect frontent stuff to improve on UX, maybe store analyytics on links usage if not go with usage db table in our system)

## Future Improvements
- Add dashboard so users could increase the life of their links or delete/update links if needed
- Improve mechanism for generating the keys in the short URLs
- Add ability to create custom short urls
