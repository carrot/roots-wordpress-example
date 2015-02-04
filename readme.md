# wordpress-roots-example

An example showing wordpress integration with roots through netlify.

### Wordpress Setup

Ok so first you need a wordpress install. Probably the easiest way to get one quickly is to just boot one up on Digital Ocean. Check out [this guide](https://www.digitalocean.com/community/tutorials/one-click-install-wordpress-on-ubuntu-14-04-with-digitalocean) to get you up and running.

Cool, so you have wordpress installed. Next, you will need to put in two plugins in order to get everything wired up. Install [Jetpack](https://wordpress.org/plugins/jetpack/) and [Hookpress](https://wordpress.org/plugins/hookpress/). Once you have these installed, head over to the plugin configuration section, and make sure jetpack is set up by logging in with a wordpress.com account, and you have turned on the "JSON API" module.

### Roots Setup

Ok, now we have a public API set up for our wordpress site, so we're ready to integrate with roots. Spin up a new roots site with `roots new` and make sure everything is working ok. Now install the [roots-wordpress extension](https://github.com/carrot/roots-wordpress) with `npm i roots-wordpress -S`. Configure it in your `app.coffee` (and `app.production.coffee`) with something like this:

```
wordpress = require 'roots-wordpress'

module.exports =

  extensions: [
    wordpress
      site: 'your-site-url-or-ip-address.com'
      post_types:
        post:
          template: '_single.jade'
  ]
```

In your `index.jade` file you can now access your posts with the `wordpress.post` local, loop through em and print out the titles in a list, using `_url` to link to their single post pages. Then make a `_single.jade` template for single posts. You can use the `post` local to access the post's data. Examples for all of this can be found in this very repo. Run `roots watch` and make sure it's looking good.

### Deploying

Ok so everything is looking good and we're ready to deploy. First, add roots to your dependencies with `npm i roots -S`, then just run `roots deploy -to netlify`, enter in your API key (you can create one under "OAuth Applications > Personal token" in your account settings), and it should ship right out. Open up the site in netlify and link it up with the github or bitbucket repo you are using for the site. It should auto-fill the roots deployment commands, and after linking build and compile your site from source.

Next, create a new webhook on netlify for your site, called "wordpress". It should give you a link. Copy the link and head back to your wordpress admin.

On the left menu, go to "Settings > Webhooks" and add a couple new hooks. From the actions list, select which actions you'd like to prompt a recompile of the site, then paste in the netlify webhook url. Personally I chose publish post, edit post, and delete post. That should be it -- try making a new post, and refresh your netlify site in a minute or two and the post should be live!