---
title: Slate developer docs

toc_footers:
  - <a href='http://slateapp.com'>Get your beautiful presentations now</a>

includes:

search: true
---

# Introduction

Welcome to the Slate developer docs! The aim of these docs is to guide you through what's possible with Chalk and best practces for each section of your Slate site.

You can view code examples in the area to the right, with explanations on the left.

# Getting started

Your new Slate account will come with a default theme out of the box. We suggest your first steps are to familiarise yourself with the different templates, and what each one is used for.

Chalk is based on Twig, and follows the same principles.

#Fundamentals

##Layout

```php
{layout "html"}
```

Each whole-page template should start with the layout it will use.

Layouts are used to speed up development, providing the main structure, as well as resuable blocks. Using a block in your template will overwrite the block defined in the layout. See below for more info on the block system.

In most cases, you will just need to use the 'HTML' template, but you can create as many layouts as you want. See below for more info on the HTML template.

##Block System

```php
{block header}
	{include "header"}
{endblock}
{block content}
{endblock}
{block footer}
	{include "footer"}
{endblock}
```

Inside your layout, you define how the page will be structured, and where other templates can inject their content.

Content inside a block in the layout template acts as a default, and will be used until overridden.

The basic structure for a layout is on the right.

##Includes

```php
{include "alt_footer"}
```
Include any other template inside another one, useful for reusing code blocks, and hiding complexity from the main template.

Template name must be in quotes.

##Debug
Add `{debug(object)}` to your templates, where _object_ is the item you want to see available tags for.

This will output an array of data contained in that object.

<aside class="notice"><code>debug</code> has to be used inside a block or it will throw an error.</aside>

##Filters
The below is a list of common filters you can use to modify what an object's output. For example `{director.biography|striptags}` would remove any html found in the director's biography. Filters are initiated with a `|`.

Helper | Arguments
--- | ---
date |
length |
lower | _none_
raw |
replace |
resize | <ul><li>width</li><li>height</li><li>method<ul><li>resize (default)</li><li>crop</li></ul></li></ul>
striptags |
title |
trim |
upper | _none_

<aside class="notice">You must always use <code>|resize(width, height, method)</code> when serving images. Do not reference the <code>remote_path</code> directly.</aside>

##Settings

```php
{settings.company_name}
{settings.domain}
{settings.contact_email}
{settings.phone_number}

{settings.addresses.address}
{settings.addresses.address_next}
{settings.addresses.city}
{settings.addresses.post_code}
{settings.addresses.country}

{settings.socials.facebook}
{settings.socials.twitter}

{settings.seo.global_title}
{settings.seo.global_description}

{settings.logo}
```

Slate users are able to manage a lot of their basic settings, such as Company name, social profiles and logo from within their account. All of this data is available to use in the templates, to avoid hard coding anything.

Make use of this in footers and contact pages etc.

##Site

```php
{site.pathname}
{site.url}
{site.uri}
{site.pathname}

```

In order to check the active page or perform anything more complex using the url, you'll need access to the `site` object.

# Assets

```php
{asset name="asset.ext"}
```

# Custom Attributes

#Templates
## HTML

This is the master layout file, and where you should define your core structure, add meta tags and css and js assets.

This template is then used in all your other 'page-based' templates.

### JS
Javascript should be included before the closing body tag.

The default theme comes with a predefined block for custom javascript assets. Reuse that block in other templates to add non-global javascript to individual pages.

### CSS

The default theme comes with a predefined block for custom css assets. Reuse that block in other templates to add non-global javascript to individual pages.

## Home

```php
{block content}
    {for spotlight in spotlights.home order="random"}
        {spotlight.title}
        <img src="{spotlight.object.thumbnail|resize(780, 480, 'crop')}">
        {spotlight.description}
        <a href="/directors/{spotlight.artist.slug}">{spotlight.artist.name}</a>
    {endfor}
{endblock}
```

The home template is used to generate the view on the root of the domain.

The main block to replace on this page is `{block content}` which will hold all of your content.

Other blocks can be used to add custom footers or headers, or to set the meta title and descriptions.

## Header

Usually where you put your site's navigation etc. The header template needs to be included in other templates to be used. It comes packaged in `{block header}` in the default HTML template.

## Footer

Works as per the header.

## Director Listing

```php
{for director in directors sort="name"}
  <a href="/directors/{director.slug}">{director.name}</a>
  <img src="{director.avatar|resize(800, 450, 'crop')}">
{endfor}
```

This template controls the layout and content of `/directors/`.

Shows a list of all published directors.

If your client would like to control the order of the directors manually, you'll need to create a spotlight.

Option  | Parameters 
--- | --- 
sort | <ul><li>name</li><li>publication_date</li><li>other</li></ul>
order | <ul><li>ASC</li><li>DESC</li></ul>
start | _integer_
limit | _integer_

## Director Profile
```php
{director.name}
{director.biography|raw}
<img src="{director.avatar|resize(600, 340, 'crop')}">
{for content_type in director.content_types}
    {content_type}
{endfor}

{for region in director.regions_list}
    {region}
{endfor}

{for award in director.awards}
    {award.name}
    <!--{award.project}-->
    {award.year}
    {award.url}
{endfor}
```
## Recent Work Listing
```php
{for work in recent_works}

    <a href="/work/{work.urlname}">{work.title}</a>
    {work.associated_media.client.name}
    {work.associated_media.agency.name}
    {work.associated_media.artists_names[0]}

    <img src="{work.associated_media.thumbnail|resize(800,450,'crop')}">

{endfor}
```
## Recent Work View
```php
{work.title}
{work.associated_media.description|raw}
{work.associated_media.thumbnail|resize(800, 450, 'crop')}

{player}
```
## Showreel
```php
{if showreel.is_expired}
    <p>This showreel has expired.</p>
{elseif showreel.is_deleted}
    <p>This showreel has been deleted.</p>
{else}

{showreel.name}
{showreel.description}

    {for work in showreel.media}

        {work.title}
        {work.client.name}
        <img  src="work.thumbnail|resize(800, 450, 'crop')}" data-player-item data-mixed-media data-action="play" data-playlist-item="{loop.index0}" data-player="showreel">

    {endfor}

    {player name="showreel" media=showreel.media advance=true autoplay=false loop=false type="mixed" noembed=true}

    {if not user_device.mobile and showreel.download_allowed}<a href="#" data-download-showreel="{showreel.id}" data-player="showreel">Download Reel</a>{endif}

    <script type="text/javascript" src="{asset name='player.events.js'}"></script>

{endif}
```
## Project
```php
{director.name}
```
## Project Folder
```php
{director.name}
```
## Resource Access Form
```php
{director.name}
```
## News Listing
```php
{for article in news}

    <a href="/news/view/{article.urlname}">{article.title}</a>
    {article.publication_date|date("d.m.y")}

    <img src="url({article.artwork|resize(800,450,'crop')})">
    {article.teaser|raw}

    {if article.layout_type == "news"}

    {elseif article.layout_type == "link"}

        <a href="{article.link}">Visit link</a>

    {elseif article.layout_type == "clip"}

        <div style="background-image: url({article.artwork|resize(900, 570, 'crop')});">
            {player name=article.urlname clip=article.associated_clip noembed=true}
        </div>

    {elseif article.layout_type == "director"}

        <a href="/directors/{article.associated_director.slug}">{article.associated_director.name}</a>
        <img src="{article.artwork|resize(900, 570, 'crop')}">

    {endif}

{endfor}
```
## News Article
```php
{article.title}
{article.publication_date|date("d.m.y")}

{article.content|raw}

<img src="{article.artwork|resize(680, 270, 'crop')}">

{if article.photos is not empty}
    {for photo in article.photos}
        <img src="{photo|resize(800, 450, 'crop')}">
    {endfor}
{endif}

{for video in article.clips}
    <img src="{video.thumbnail|resize(600, 340, 'crop')}" data-mixed-media data-playlist-item="{loop.index0}" data-action="play" data-player="article_video">
{endfor}
```
## Photographer Listing
```php
{director.name}
```
## Photographer Profile
```php
{director.name}
```
## Player Embed Form
```php
{director.name}
```
## Player Embedded
```php
{director.name}
```

# Player
```php
{director.name}
```

# Pages
## Using layouts
## Placeholders

# Location based controls

# Useful Snippets

## Next and Previous News

## Multi video recent work pages

```php

```

This works in conjunction with a custom attribute, with a comma separated list of video IDs.

## Load different images for mobile

> object will be relavant to the content - showreel/spotlight etc.

```php
{if user_device.mobile and not user_device.tablet }
    {set img_url = object.thumbnail|resize(640,360, 'resize')}
{elseif user_device.tablet and not user_device.mobile}
    {set img_url = object.thumbnail|resize(502,282, 'resize')}
{else}
    {set img_url = object.thumbnail|resize(549,309, 'crop')}
{endif}
```

> img_url can then be used in the src in your loops

Using the device detection, you can set different image sizes to load, to reduce page size for mobile.

## Linking to specifc videos on a director's profile

```php
{set auto_play = site.url matches "/play\\/(.*)/" ? 1 : 0}

{if auto_play == 1}
    {set uri_parts = site.url|split('/')}
    {set selected_clip_urlname = uri_parts[uri_parts|length-1]}
{endif}

{ set selected_clip = null }
{ set selected_clip_index = 0 }

{set player_media = director.published_showreels[0].clips}

{if selected_clip_urlname }
    { for media in player_media}
        { if media.urlname == selected_clip_urlname }
            { set selected_clip_index = loop.index0 }
            { set selected_clip = media }
        { endif }
    { endfor }
{endif}

{if not selected_clip}
    { set selected_clip = player_media[0] }
{endif}
```
By adding `data-clip-id={work.id}` to each item in your list of videos on a director's profile, you can make use of in built pushstates.

When accessing one of these urls directly, it will set the `selected_clip`, and you can then use that to change the first video that loads on the page.



