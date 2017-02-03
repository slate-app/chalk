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

Helper | Function | Arguments
--- | --- | ---
date | Format a date | [See PHP Docs](http://uk1.php.net/manual/en/function.date.php)
length | Gets the length of an array |
lower | Sets a string to lowercase | _none_
raw | Outputs a string as HTML | _none_
replace | Replaces contents of a string| {"Foo": "Bar", " ": "-"}
resize | Resizes and manipulates an image | <ul><li>width</li><li>height</li><li>method<ul><li>resize (default)</li><li>crop</li></ul></li></ul>
striptags | Removes all HTML from a string |
title | Sets a string to Title Case | _none_
trim |
upper | Sets a string to UPPERCASE | _none_

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
{settings.addresses.latitutde}
{settings.addresses.longitude}

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

##Stylesheets and Scripts
These will be uploaded to Slate as Assets, and then referenced in your layout.

See [Assets](#assets) below for more info.

##Inline CSS/JS

```php
{literal}
$('.selector').on('click', function() {
	//do stuff
});
{endliteral}
```

In order to use inline CSS or JS, you need to escape the `{}`, so that they're not interpreted as Chalk code. To do this, wrap your code blocks in `{literal}` tags.

#Templates
## HTML

This is the master layout file, and where you should define your core structure, add meta tags and css and js assets.

This template is then used in all your other 'page-based' templates.

### JS
Javascript should be included before the closing body tag, with the exception of jquery, which needs to be loaded in the head.

The default theme comes with a predefined block for custom javascript assets. Reuse that block in other templates to add non-global javascript to individual pages.

### CSS

The default theme comes with a predefined block for custom css assets. Reuse that block in other templates to add non-global CSS to individual pages.

## Home

```php
{block content}
    {for spotlight in spotlights.home order="random"}
        {spotlight.title}
        <!--{spotlight.type}-->
        <img src="{spotlight.object.thumbnail|resize(780, 480, 'crop')}&quality=80">
        {spotlight.description}
        <a href="/directors/{spotlight.artist.slug}">{spotlight.artist.name}</a>
    {endfor}
{endblock}
```

The home template is used to generate the view on the root of the domain.

The main block to replace on this page is `{block content}` which will hold all of your content.

Other blocks can be used to add custom footers or headers, or to set the meta title and descriptions.

Videos, Images, Artists and News can all be added to a spotlight. As they have different data structures, be sure to check the spotlight type and set the content accordingly.

## Header

Usually where you put your site's navigation etc. The header template needs to be included in other templates to be used. It comes packaged in `{block header}` in the default HTML template.

If you need to include the header anywhere manually, use `{include "header"}`

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

The snippet on the right Shows a list of all published directors, sorted by name.

If your client would like to control the order of the directors manually, you'll need to create a [spotlight](#spotlights).

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
<img src="{director.avatar|resize(600, 340, 'crop')}&quality=80">

{for reel in director.published_showreels}
	{reel.displayed_name}
	{for work in reel.media}
		{work.title}
        <img src="{work.thumbnail|resize(600, 340, 'crop')}&quality=80}">
	{endfor}
{endfor}

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

The `director_profile` template controls the layout and content of `/director/{director-name}`. 

The content on the right shows the basic content structure.

See [Player](#player) below for details on how to include a video player on this page.

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

The `recent_works_listing` is used to control the layout and contents of `/work`.

`{recent_works}` is an array containing all content added to Recent Work in the Slate admin.

Data associated with each video or image is available inside `associated_media`.

The publication date for Recent Work items is taken from the linked library video or image, and the `{urlname}` is built from the video or image's title.

## Recent Work View
```php
{work.title}
{work.associated_media.description|raw}
{work.associated_media.thumbnail|resize(800, 450, 'crop')}

{player}
```

The `recent_works_view` is used to control the layout and contents of individual pieces of work on `/work/work-name`.

`{work}` is an array including all the data about the Recent Work item. It contains `associated_media` which relates to the actual library entry that was added to Recent Work.

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
        <img  src="work.thumbnail|resize(800, 450, 'crop')}" 
        data-player-item 
        data-mixed-media 
        data-action="play" 
        data-playlist-item="{loop.index0}" 
        data-player="showreel">

    {endfor}

    {player name="showreel" media=showreel.media advance=true autoplay=false loop=false type="mixed" noembed=true}

    {if not user_device.mobile and showreel.download_allowed}<a href="#" data-download-showreel="{showreel.id}" data-player="showreel">Download Reel</a>{endif}

    <script type="text/javascript" src="{asset name='player.events.js'}"></script>

{endif}
```

This is the template that is used for showreels. 

Video playback tracking and showreel view notifications and tracking are all handled automatically.

See [Player](#player) below for more info on the player code.

Make sure to include the checks for expired and deleted reels, otherwise user input in the admin won't be reflected in the template.

In the default tempalte the download option is also conditional on the user not being on a mobile device.

## Project
```php
{project.name}
```

Layout for the root of a project.

Projects are used for delivering assets to users.

There are 4 data types to be aware of in Projects.

- Project
- Folder
- Section
- Item

Items are always in Sections.
Sections can be in a folder or just in the project.
Folders can be in folders or in sections in a folder.
Folders can also be in the project root.

All of these situations need considering when building out your template. The basic template is a good guide to use.

`{project}` is populated with all the data from the currently viewed project.

## Project Folder
```php
{director.name}
```

An extension of the above.

`{folder}` is populated with all the data from the currently viewed folder.

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

`{news_listing}` shows content added via the news module. There are 4 types of news:

### News
This is your default type of news to build. It contains the following.

- Artwork
- Teaser
- Content
- Clips
- Photos

### Link

- Artwork
- Destination link

### Clip
### Director




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

#Spotlights

# Assets

```php
{asset name="assetname.ext"}
```

In addition to the templates, Slate allows you full control over stylesheets, javascript files, fonts and images. 

These are all controlled via the 'Assets' section, accessible via the link on the template listing screen.

Once added, assets are able to be included in any file using the syntax on the right.

CSS and JS assets will be editable inside Slate. After uploading the asset for the first time, subsequent changes should be pasted into the asset editor.

##Fonts
If you wish to use local fonts, instead of a webfont service, there are a few points to note.

Any font files you upload to Assets will have `Access-Control-Allow-Origin: *` added.

These can be included using `@font-face` inline in a template, using the asset include syntax, or they can be referenced via an absolute path in your stylesheet.

To get the absolute path, head to the asset listing page, and copy the path on the right. The base path remains the same for all assets, with only the filename changing.

If you use a CSS pre-processor, it is a good idea to add this base path as a variable for all image or font files you reference, as they will all need to be absolute urls.

# Custom Attributes

Each type of content has a predefined list of meta data/credits associated with it, as shown above. You might need to extend this for your needs. 

Attributes can be added to:

- Videos
- Illustrations
- Directors
- Photographers
- News
- Showreels

There are 8 types of attributes that can be added:

- Integer
- Boolean
- Decimal
- String
- Floating Point Number
- Autocomplete Collection
- WYSIWYG Editor
- File Upload

##Autocomplete Collection
```php
{for editor in work.editors}
	{editor.value}
{endfor}
```

This will build up a list of entries that can be reused to save the user typing the whole ting each time, and to avoid inconsistencies. Content is stored as an array.

##File Upload
```php
{for file in work.attribute_name.files}
	{debug(file)}
{endfor}

{work.spotlight_image.files[0].file|resize(1200, 0)}
```
Files are also stored in an array, and depending on their usage can be looped through or used to show a single item.

#Image manipulation
By default, `|resize()` only lets you set width, height and whether to crop or resize. However, it's possible to add a series of extra parameters to the image url.

These are added outside of the `{}`, starting with an `&`.

##Width
```php
{img|resize(300, 0)}
```
Set in the `|resize()` helper.

The desired width of the image; it will resize according to the method you select (see below). If you do not want to restrict width, pass in 0 (0x100.jpg will only restrict to 100 height).

##Height
```php
{img|resize(0, 100)}
```
Set in the `|resize()` helper.

The desired height of the image; it will resize according to the method you select (see below). Set to 0 to not restrict height.

##Crop or Resize
```php
{img|resize(300, 0)}

{img|resize(300, 150, 'crop')}
```

Set in the `|resize()` helper.

Resize is the default method

Both dimensions must be supplied in order to crop an image

##Quality
```php
<img src="{img|resize(300, 0)}&quality=80">
```

The quality it should resample to; from 0 to 100. Will convert to 0-9 automatically for PNG, 1-100 for JPG.

We recommend applying compression to all images.

##Gravity
```php
<img src="{img|resize(300, 150, 'crop')}&gravity=n">
```
Used in combination with `crop`, defines the area to crop from.

Possible values:

- nw => Top left
- n => Top
- ne => Top right
- e => Right
- se => Bottom right
- s => Bottom
- sw => Bottom left
- w => Left

Any other value, be it center, phteven or no value at all will result in a standard center crop.

Note: when processed, the value is:

- Converted to lower case
- Any dashes, underscores, spaces are replaced with an empty string
- North/east/south/west written in full are converted to their single character representation
- As a result, North East or south-west are also valid values.

##Interlace
```php
<img src="{img|resize(300, 0)}&interlace=plane">
```

Allows you to set the interlace method (progressive image).

Possible values:

- line
- plane

Any other value will result in no interlacing and as such is the default behaviour.

Please note that interlacing PNGs actually makes them bigger, the image manipulator currently does nothing to prevent this.

##Crop
```php
<img src="{img|resize(300, 150, 'crop')}&crop[x]=3000&crop[y]=1000&crop[width]=600&crop[height]=2000">
```

The crop settings allow you to specify an area that should be cropped out of the original, before applying the necessary size transformations.

- x => The X (horizontal coordinate) where the cropping should start
- y => The Y (vertical coordinate) where the cropping should start
- width => The width of the crop box
- height => The height of the crop box

So say you have an image that's 5000 by 5000 pixels. You request a resized version as on the right.

What you ultimately want is an image that is exactly 300x150. What the tool does first though, is grab an area 600x2000 out of the original 5000x5000 image, starting at 3000 pixels from the left, 1000 from the top. So the area it grabs is from 3000,1000 to 3600,3000.

The ratio does not match however, so it will now resize that new 600x2000 "image" (crop) down to 300x1000 first. Then it crops off 425 pixels at both the top and bottom to get to that 150 height (1000 - 150 = 850 pixels too many, so it evenly cuts them off).
If you had given it resize as method, it would resize that 600x2000 all the way down to 45x150. Without a height restriction you would've received 300x1000. And so on.

##Effects
```php
<img src="{img|resize(300, 150)}&effects=greyscale">
```

Effects to apply to the image. This can be either comma separated (?effects=effect1,effect2) or as an array (?effects[]=effect1&effects[]=effect2). The following are valid effects:

- greyscale
  - makes the image greyscale
  - Aliases: bw, grayscale, desaturate
- invert
  - inverts the image
  - Aliases: negative
- sepia
  - applies a sepia effect

##Format
```php
<img src="{img|resize(300, 0)}&format=jpeg">
```

The output format. One of jpg, jpeg, png or gif. Gives exactly 0 fucks about any implications that come with these conversions, this is entirely up to the user; i.e. it will not prevent you from converting a transparent PNG to a JPG (and thus losing all transparency).

# Location based controls
```php
{set location = user_location()}

{set regions = ['US', 'GB']}

{set selected_location = location.country_code }

{if site.query.region|upper }
    { set selected_location = site.query.region|upper }
{elseif "Europe" in location.time_zone}
    {set selected_location = 'GB'}
{elseif selected_location not in regions}
    { set selected_location = 'US' }
{endif}

```
If you need to show different content based on a user's location, there is an object containing that information.

This would be used in conjunction with an attribute to only show the relevant content.

The example on the right checks for a `?region=XX` query parameter in the url and overrides the user's IP based location with one set via the site, using a dropdown select for example.

# Device detection
```php
{if user_device.mobile}
	//Do Stuff
{elseif user_device.tablet}
	//Do Other Stuff
{else}
	//Do Default Stuff
{endif}
```

If you need to detect the user's device in order to change the displayed content or functionality, `user_device` will return true/false for mobile and tablet, as on the right.

# Useful Snippets

## Next and Previous News

## Multi video recent work pages

```php
{set full_playlist = [work]}
{if work.campaign_name[0].value}
    {for other_work in works}
        {if other_work.campaign_name[0].value == work.campaign_name[0].value and other_work.id != work.id}
            {set full_playlist = full_playlist|merge([other_work])}
        {endif}
    {endfor}
{endif}
```

The example on the right uses a `campaign_name` custom attribute. This would find all pieces of work with the same 'Campaign Name' as the currently viewed work and assigns them to a new array `full_playlist`.

This array can then be looped through to show the work.

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



