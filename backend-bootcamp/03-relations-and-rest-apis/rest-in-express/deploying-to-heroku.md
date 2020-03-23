---
title: Deploying to Heroku

tags:
  - Heroku
---

# Deploying our app

A big part of software development is of course getting our apps live and out onto the web so users can start interacting with it. What would this mean for a backend like the one we worked on these past few days? Well several things:

1. Creating a new PostgreSQL database.
1. Setting up the new database with our tables/models.
1. Set up all our configurations correctly, like database connections or the `PORT` in which our app will be available.
1. Getting our code on some server and have it run.

We already know how to do several of this things (like step 3.) but let's go over this step by step.

Start up by going to the Heroku website and creating a new app. Once we get to the app administration dashboard/home screen we are going to proceed with adding a PostgresSQL DB. Heroku provides lots of "addons" (resources) which you can hook up to your app. One of these is the one we are going to use, `Heroku Postgres`. So, head over to the resources tab and set it up:

![HerokuResources](https://p98.f4.n0.cdn.getcloudapp.com/items/nOum5NnG/Image+2020-01-06+at+4.45.20+PM.png?v=07b3b99b2115d1b09ad28bb5f03e7781)

![HerokuPostgres](https://p98.f4.n0.cdn.getcloudapp.com/items/RBuXEd9j/Image+2020-01-06+at+4.45.50+PM.png?v=efd7b535fbe24a4ea01cffcc3938f8fa)

Confirm that we want to provison our app with this resource and let's move onto the next step.

Now that we have our heroku instance with a Postgres database attached it's time to tweak a bit of our apps configs to make everything work smoothly when deploying. This can be split up into two steps. First, we need to tell our app how to use and connect to Heroku's database. After that we have to give Heroku some instructions on how to run our migrations once the app's deployment is finished, so our tables get built.

For the first part, we are going to back to the `config.json` file, where we set our DB configs. Luckily, making this work with Heroku cannot be simpler. There is no need to set all the keys in the config objects ourselves, Heroku takes care of all that through one `env` variable. We just need to tell Sequelize to use that and we do that by setting the `production` configs to simply:

```json
"production": {
    "use_env_variable": "DATABASE_URL"
  }
```

And done! Sequelize will load all necessary configs from that `env` variable.

Also, we need to configure the `start` script for our app. We do this in the package.json file by adding to the `scripts` object like this:

```js
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
  },
```

This way we tell Heroku how to run our program once it's deployed

On our index file we are going to need to add two things:

The first one is a middleware to make our server handle `cors` requests. To do this first we need to install the middleware using `npm install cors` and then apply it to our express server as **the first middleware we use**.

```js
const cors = require("cors");
app.use(cors());
```

Finally, Heroku sets it's own PORT on which the server will run through another `env` variable. So to tell Express to use that port we can do on our `index.js` file:

```js
const PORT = process.env.PORT || 4000;
```

### Post Release

Heroku, as a big deployment service, has a ton of configs and options we can set up to manage our app's deployment. One of these are `release` options. They let us provide instructions to Heroku about what to do before releasing the app. Here is where we are going to set up our migrations execution. These Heroku configs are specific to each app, so they live side by side with the app's code, in a file called a `Procfile`. We are going to set up a very simple one, but if you want to read more about what can be done with them you can do so [here](https://devcenter.heroku.com/articles/procfile).

Create a file called `Procfile` (capital P) and with no extension on your projects root. Inside we are going to define a `release:` key where we can tell Heroku to run a certain command once the app's build finishes. For our simple app we can just do:

```
release: npx sequelize-cli db:migrate
```

As straight-forward as it can be: we are telling Heroku to run our migrations on release. This is great but this way we can only run one command on release, which is fine for our example case but maybe you wish to do some other stuff? No problem, we can define a bash script (`.sh`) where we place all the commands we want to be executed and tweak our `Procfile` so that it executes that script, like:

`Procfile`

```
release: bash post-release.sh
```

`post-release.sh`

```bash
npx sequelize-cli db:migrate
## some other instruction..
```

Great! Push all this to Github and point your new Heroku app to your Github repo and branch, as we usually do for deployment.

### Monitoring deployment and release

Once you get to the actual deployment you are going to see the regular build log and once that is finished the `release` instructions will be executed, which will show up on a separate log.

`Deployment stage`
![herokuDeployLog](https://p98.f4.n0.cdn.getcloudapp.com/items/Z4uwr8ok/Image+2020-01-07+at+10.35.59+AM.png?v=7ca4dc74c813bea5d52dfd3211228da6)

`Release stage`
![herokuReleaseLog](https://p98.f4.n0.cdn.getcloudapp.com/items/9ZuNRndK/Image+2020-01-07+at+10.37.53+AM.png?v=dab2460ea896396d823198745b1998c9)

### Checking out the DB

You can use your Database client to connect to our remote DB now. If we go back to Heroku's website, back to the `Resources` tab and click on our `Heroku Postgres` we'll be taken to a new page where we can interact with and configure our DB. We can create `Dataclips` wich are nothing more than SQL statements to be exectued on our DB. To connect to our DB we must go to the `Settings` tab and click on `View Credentials...` where we'll get all the data we need to set our DB client.

![HerokuCredentials](https://p98.f4.n0.cdn.getcloudapp.com/items/8Luw9zeq/Image+2020-01-07+at+10.55.43+AM.png?v=2b76f93c1974c6952cd80b63713040fa)

Another option we have on this same screen is to `Reset` our database. If something goes wrong with our migrations we can adjust them in our code and then reset the database and re-deploy to have our tables be constructed again.

If everything goes ok we should have our live API working in Heroku so try adding some Users and TodoLists through our REST routes.

Try it out!
