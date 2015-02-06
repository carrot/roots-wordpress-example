# wordpress-roots-example

An example showing wordpress integration with roots through netlify.

### Wordpress Setup

Ok so first you need a wordpress install to use as your CMS. The easiest way to get one quickly is to just boot one up on a [Digital Ocean](https://www.digitalocean.com/) VPS. Check out [this guide](https://www.digitalocean.com/community/tutorials/one-click-install-wordpress-on-ubuntu-14-04-with-digitalocean) to get you up and running. 


> **Note:** Alternately, if you want to avoid having to manage your own server you could spin up a new wordpress installation at [Wordpress.com](https://wordpress.com/) for free. If you're using a [Wordpress.com](https://wordpress.com/) site then everything is pre-installed for you out of the box. Just verify that the site is public and you're ready to setup roots.

Cool, so you have wordpress installed. Next, you will need to install in two plugins in order to get everything wired up. Head to your wordpress admin interface and install [Jetpack](https://wordpress.org/plugins/jetpack/) and [Hookpress](https://wordpress.org/plugins/hookpress/). Once you have these installed, head over to the plugin configuration section; to get jetpack installed, it will require you to link up your wordpress.com account. If you don't currently have a wordpress.com account, it will ask you to create one.  Once that linking is complete you'll have turned on the "**JSON API**" module of jetpack. This will expose your wordpress content as a consumable API.

### Roots Setup

Ok, now we have a public API set up for our wordpress site, and we're ready to integrate with roots. Spin up a new roots site with `roots new` and make sure everything is working ok. Now install the [roots-wordpress extension](https://github.com/carrot/roots-wordpress) with `npm i roots-wordpress -S`. Configure it in your `app.coffee` (and `app.production.coffee`) with something like this:

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

In your `index.jade` file you can now access your posts with the `wordpress.post` local, loop through em and print out the titles in a list, using the `_url` to link to their single post pages. You'll also need to make a `_single.jade` template for single posts, as defined in your configuration. You can use the `post` local to access the post's data here. Examples for all of this can be found in this very repo. Run `roots watch` and make sure it's looking good.

### Deploying

Ok so everything is looking good and we're ready to deploy. To do this, we suggest using [netlify](http://netlify.com) because they offer custom build processes and an easy webhook interface for continuous integration.

First, add roots to your dependencies with `npm i roots -S`, then just run `roots deploy -to netlify`, enter in your API key (you can create one under "OAuth Applications > Personal token" in your account settings once you'd created an account on [netlify](http://netlify.com)), and it should ship right out. Open up the site in netlify and link it up with the github or bitbucket repo you are using for the site. It should auto-fill the roots deployment commands, and after linking build and compile your site from source.

Next, create a new webhook on netlify for your site, called "wordpress". It should give you a link. Copy the link and head back to your wordpress admin.

Because we've installed the very nice [hookpress](https://wordpress.org/plugins/hookpress/) plugin, on the left menu of wordpress, navigate to "Settings > Webhooks" and add a couple new hooks. From the actions list, select which actions you'd like to prompt a recompile of the site, then paste in the netlify webhook url. Personally I chose publish post, edit post, and delete post. That should be it -- try making a new post, and refresh your netlify site in a minute or two and the post should be live!

Each time your wordpress installation completes one of those actions (like publish, edit and delete), your wordpress CMS will send a request to netlify triggering a new build of your site.
