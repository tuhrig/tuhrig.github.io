README
======

## What is this?

This right here is my personal blog.
I use this blog to write about software development and related topics.
Back in the days, this blog was hosted by a Wordpress instance.
It was migrated to Jakyll / githup.io in December 2022.

## Run me locally

    gem install jekyll bundler
    bundle init
 
    # Install the used gems
    bundle

    # Run Jekyll to see the blog
    bundle exec jekyll serve

## Categories

- "coding"
- "architecture"
- "design-pattern" 
- "java"
- "kotlin" 
- "spring"
- "blog" 
- "academic" 
- "angularjs"
- "javascript"
- "html"
- "ddd"
- "database"
- "aws"
- "wordpress"
- "security"
- "software-design" 
- "devops"
- "personal" 
- "books"
- "python" 
- "data-mining"
- "android"
- "slides"
- "c"
- "uml"
- "cloud"
- "docker"

## Theme and styling

The theme and layout is forked from:

- https://github.com/amitmerchant1990/reverie.

## Code Blocks

Do it like this:

{% raw %}
```html
<html>
```
{% endraw %}

See: https://stackoverflow.com/questions/52324134/getting-an-liquid-exception-liquid-syntax-error-while-using-jekyll

## Load iframe content

<iframe
name="thridPartyContent"
class="speakerdeck-iframe"
frameborder="0"
allowfullscreen="true"
mozallowfullscreen="true"
webkitallowfullscreen="true"
data-ratio="1.3333333333333333">
</iframe>

<script>
if(consentGiven()) {
    const site = "https://speakerdeck.com/player/50141a0aa049a30002005abb";
    document.getElementsByName('thridPartyContent')[0].src = site;
}
</script>

## License

MIT
