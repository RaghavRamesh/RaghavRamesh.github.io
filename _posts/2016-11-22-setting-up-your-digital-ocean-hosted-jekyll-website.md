---
title: "Setting up your Digital Ocean hosted Jekyll website"
date: 2016-11-22
categories: [jekyll, tutorial]
tags: [jekyll, tutorial]
---

This post contains a curated list of 'how-to' links on setting up your website and makes specific choices such as GoDaddy for domain name, Digital Ocean for an Ubuntu virtual private hosting, Jekyll for site generation, nginx for serving out your site, etc. The lack of flexibility in choices is intentional so as to keep the tutorial as short as possible and hopefully serve as the only how-to page you will need in order to have your website up and running.

I would have preferred such a list as well when I was setting up my website, but instead I had to search for different tutorials for each choice that I made along the way. Not to mention the different issues that came up during each step.

Let's get started without any further ado. The steps you need to do to setup your website

### Buying a domain name
  If you wish to buy one, you can from GoDaddy, gandi.net or from any other domain name registrar. A shoutout to [Honey](https://chrome.google.com/webstore/detail/honey/bmnlcjabgnpnenekpadlanbbkooimhnj), a Chrome extension - it helped bring my shopping cart value down drastically, by applying crowd-sourced promo codes. Although there is also a suspicion that the extension [captures information of all your internet activity](https://www.reddit.com/r/Frugal/comments/4phdxn/for_users_of_the_honey_coupon_chrome_extension/), so I'd be wary of that.

### Buying a Digital Ocean droplet, your virtual private hosting
  Buy a droplet from [Digital Ocean](https://www.digitalocean.com/), the cheapest one would do for now. Again, Honey helped me obtain two months of free hosting on the cheapest plan.

### Setup Jekyll on your Ubuntu VPS
  Next up is the major chunk of your website setup. You can follow [this link](https://www.digitalocean.com/community/tutorials/how-to-get-started-with-jekyll-on-an-ubuntu-vps) to get started with Jekyll on your newly bought droplet. This guide will walk you through setting up Jekyll for static site generation, using `capistrano` to deploy the generated site and finally `nginx` to serve it out. But there are a few issues with this article.

  - This article is outdated (as of 22nd Nov 2016) - the guide only works for `capistrano v2.15.x`. but not `v3.x.x` which is the version installed when you run ```gem install capistrano``` (as of Oct 2016). Like those commenting on this article, I was also stuck due to this. So be sure to install `capistrano v2.15`.

  - Even though the deploy script claims you don't need to use `sudo` to run the commands, I'm still asked to use it. But I didn't dig deep into this as yet. I'll update the post when I find out more.

  - You might run into a permission issue while running `cap deploy` if the `tmp` folder mentioned in the deploy script is not owned by the `deployer`. You can fix that by giving write permissions to the `deployer` to a custom `tmp` folder in a known folder location such as `/home/deployer/tmp`.

### Connect your domain name and VPS
  You can follow [this link](http://withr.me/add-domain-name-for-your-server-on-digitalocean/) to help you connect your domain name that you bought from GoDaddy for your server on Digital Ocean. Check [this link](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean) if you wish to setup subdomains and mail for your new domain.

If you've followed the above links, you'd have successfully setup your website. Congrats! :) A few things that you could do next

- Buy an SSL certificate for your website to enable secure browsing for your website visitors.
- Buy `whois` privacy. Else you'll find companies offering web development, hosting and SEO services contact you using the details you provided while registering your domain. It can get pretty annoying!







