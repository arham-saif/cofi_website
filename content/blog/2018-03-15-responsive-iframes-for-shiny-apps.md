---
title: Responsive iframes for Shiny Apps
author: Paul Campbell
date: '2018-03-15'
slug: responsive-iframes-for-shiny-apps
twitterImg: img/shiny_iframe.png
description: "Seamlessly embed shiny apps into your website with responsive iframes."
categories:
  - Tutorials
tags:
  - shiny
  - dashboards
---

# Getting Shiny out into the wild

<a href="https://shiny.rstudio.com/" target="_blank">Shiny</a> has really changed game in terms of analytical web-application development. Anyone with a solid grasp of <a href="https://en.wikipedia.org/wiki/R_(programming_language)" target="_blank">R programming</a> and some basic HTML + CSS knowledge can get production quality apps and dashboards up and running in days rather than months, _and_ be in complete control of the process yourself. Furthermore, because it's all open-source software, you have total ownership of the product you build - unlike many expensive off-the-shelf <a href="https://en.wikipedia.org/wiki/Graphical_user_interface" target="_blank">GUI</a> solutions.

## Problem

Although you have full ownership of your shiny app, one drawback is that you may run into trouble when it comes to deployment. Shiny apps don't _just work_ in the browser. R code can't be run client-side like javascript can. And they can't be deployed on a standard webserver. Shiny apps need to be hosted on a server with R and shiny installed and connected to a running R process. Whilst R is a fast growing language, support for it in the IT world remains relatively small, so this means you will generally be tasked with setting up your own hosting environment, outwith your standard web architecture. <a href="https://www.shinyapps.io/" target="_blank">shinyapps.io</a> from Rstudio is a very user-friendly service that will take care of hosting for you, or if you want more control, setting up your own shiny cloud server is an option. But this isolation can lead to a breakdown in integration between shiny content and regular web content.

## Solution

One way around this is to embed shiny apps into standard webpages in an iframe. This is easily done with a standard iframe, however, shiny apps have a responsive design which means the height of the app is determined by the end users window size. This makes it difficult to seamlessly integrate dynamic content into the iframe which most commonly have a hard coded `height` attribute. This tends to result in either scroll bars appearing around the app or a big gaping void of existential abyss between the bottom of your app and the next piece of content on your webpage.

Luckily, <a href="https://github.com/davidjbradshaw" target="_blank">David J. Bradshaw</a> has built a javascript <a href="https://github.com/davidjbradshaw/iframe-resizer" target="_blank">`iframe-resizer`</a> library to help us fill this shiny void. Here is a shiny example of it in action on this very webpage.

The buttons below are part of a running shiny app hosted on <a href="https://www.shinyapps.io/" target="_blank">shinyapps.io</a>. Press some of them and see what happens...

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/3.5.16/iframeResizer.min.js"></script>
<style>
  iframe {
    min-width: 100%;
  }
</style>
<iframe id="myIframe" src="https://cultureofinsight.shinyapps.io/iframe_example/" scrolling="no" frameborder="no"></iframe>
<script>
  iFrameResize({
    heightCalculationMethod: 'taggedElement'
  });
</script>

## How to do it

To acheive this behaviour we need:

- one `js` script sourced in the shiny app
- a tagged placeholder `div` at the point in the app you want to be 'end' (after all the charts 'n tables)
- another `js` script sourced in the parent `HTML` page
- some `iframe` styling
- the `iframe` itself
- a final script telling `iframeResizer` to go to work on our `iframe` and look for the tagged `<div>`

I'm using cdn versions of the scripts but you can download them <a href="http://davidjbradshaw.github.io/iframe-resizer/" target="_blank">here</a> and use local versions if you'd prefer.

So firstly, add this to your shiny app `UI`:

```{r}
tags$head(
      tags$script(src="https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/3.5.16/iframeResizer.contentWindow.min.js",
                  type="text/javascript")
      ),
```

Then find the spot in the UI you want to be endpoint and add this placeholder `<div>` [^*]:

```{r}
HTML('<div data-iframe-height></div>')
```

Finally, add this code to the parent HTML page you are embedding the shiny app in, in the position you want the app to render (change the source url in the iframe to your shiny app url). For the <a href="https://bookdown.org/yihui/blogdown/" target="_blank">blogdowners</a> amongst us, you can paste this HTML code straight into any <a href="https://rmarkdown.rstudio.com/" target="_blank">Rmarkdown</a> or regular markdown document:

```{html}
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/3.5.16/iframeResizer.min.js"></script>
<style>
  iframe {
    min-width: 100%;
  }
</style>
<iframe id="myIframe" src="https://YOUR_SHINYAPP_URL.com" scrolling="no" frameborder="no"></iframe>
<script>
  iFrameResize({
    heightCalculationMethod: 'taggedElement'
  });
</script>
```

And that should be that!

## Going forward

There's real potential for shiny to find it's way onto more webpages in the public domain. With the ability to match the parent webpage's `CSS` styling combined with responsive iframe integration, shiny app development and design can be tailored for webpages they are destined for. Here's a great <a href="http://www.piaschile.cl/mercado/benchmarking-internacional/" target="_blank">example</a> of a shiny app built into a full website. h/t <a href="https://twitter.com/jbkunst" target="_blank">Joshua Kunst</a>.

If you're interested in bespoke web-applications for your website, get in touch with us <a href="https://cultureofinsight.com/contact/" target="_blank">here</a>.

Thanks for reading.

[^*]: You can skip this step and use `heightCalculationMethod: 'lowestElement'` in the final script, but I found this only worked when the iframe height _increased_ in height but not when it _decreased_ in height.
