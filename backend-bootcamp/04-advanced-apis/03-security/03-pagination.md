---
title: Pagination
---

# Pagination

If you ever used Google you might have notices how the page does not contain 5 billion links to other websites. Instead, you can view the first 10 results on the first page, the next 10 on page 2, etcetera.

This concept is called pagination, and it means that you don't get all elements of a collection at once. If you want to see the last one, you can, but normally you just look at the first few pages. This has great benefits for you because you don't have to download megabytes of data and it has even greater benefits for Google because they don't have to calculate and send all results but just ten at a time.

Without pagination, your app will not scale. If you start with 10 items it's fine to view them on one page, but 1000 is already pushing your clients and your server.

One form of pagination is introducing limit and offset [query string params](http://expressjs.com/en/api.html#req.query):

Sequelize, as most ORMs supports this as part of it's query system.

```js
router.get("/", (req, res, next) => {
  const limit = req.query.limit || 25;
  const offset = req.query.offset || 0;

  Image.findAll({
    limit,
    offset,
  })
    .then(images => {
      res.send(images);
    })
    .catch(error => next(error));
});
```

Note we set the limit to 25 if there is no limit (undefined, null or 0) and we set the offset to 0 if there is no offset (undefined, null or 0). Also note that at this point nothing is stopping you from indicating limit = 99999999. In a real world app you might want to prevent that from happening.

**Limit** indicates how many results are on a page. If you put it to 3, you'll get 3 images back. **Offset** determines how many results to skip. If you put it to 5, you'll skip the first 5 images and receive the images starting with image 6. Which results you get based on a limit/offset is depending on the order.

Currently we're building an API, but this API will be used in some form of UI.
In any UI you want to present the user with a previous/next button at least, and maybe with page numbers and a number of results.
To do that, you need to know what the total number of results is.
Currently, we do not send that back to the client so the client has no way to tell how many images there are (if there are more than the given limit). Thankfully we can get both pieces of information with one simple call to `.findAndCountAll()`.

With this code we add a `total` property to our response:

```js
router.get("/", (req, res, next) => {
  const limit = req.query.limit || 25;
  const offset = req.query.offset || 0;

  Image.findAndCountAll({ limit, offset })
    .then(result => res.send({ images: result.rows, total: result.count }))
    .catch(error => next(error));
});
```

Test these routes with httpie. Make sure you can control the number of items you receive. (This is how we send query params on httpie)

```shell
$ http :4000/images offset==0 limit==2
```

## Pro tip: Protect yourself at all times

Notice how we initialize the `limit` query-string parameter with the number `25`, if no value is provided. However, if a value _is_ provided, we don't impose a maximum. It's not unthinkable that some tables will grow very large and have millions of rows. This happens to every successful application. If such a request takes a few seconds, several of these queries performed at the same time can cause huge performance issues. This is an easy attack vector for a hacker trying to bring your site down. So, protect your API by imposing a maximum:

```javascript
const limit = Math.min(req.query.limit || 25, 500);
```

A Distributed Denial-of-Service (DDoS) attack will bring down almost any website, but we shouldn't make it easy on them.
