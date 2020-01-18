# Hugo Notes

## Local Development Testing
- `hugo server -D` : start server with drafts 
- `localhost:1313` : local dev server

## File Organization and Content
- There are two main content types:
  - **Single Pages**
  - **List Pages**
- All content/pages falls under the content folder
  - `hugo new {path}/{file}` : generate new files
- Pages/Content are displayed based on current themes/layouts

## File Structure
- archetypes: content that is common to all content on the site
  - can define data here, think globals
  - redefine default post metadata here
- content: your sites content
- data: like a database of sorts, store all data here
  - json, csvs, etc
- layouts: page layouts
  - html, css, etc
- static: where to store static elements, things that don't change
  - images, css, json
- themes: prebuilt themes
  - layouts, templates, html, etc

## Archetypes
Archetypes, as far as I'm currently aware, act as the base template for new posts. All new posts will take the metadata from `archetypes/default.md`. If I create a new archetype, `archetypes/my_dir.md` and created a new post under `content/my_dir/new.md`, this new post will inherit the metadata from the `posts.md` archetype.

## Shortcode
Markdown can be a little limiting when writing posts, and writing html can be cumbersome. Shortcode are ways to render predefined templates in markdown. Kind of like a react component, but in markdown. One example of this is the youtube shortcode, which works like this:
```
{{< youtube {youtube-url-tail} >}}
```

## Taxonomies
Taxonomies are user-defined groupings of content; classifications of logical relationships between content.
Hugo's default taxnomies are:
- Tags
- Categories
Taxonomies are built with this following structure:
```
Taxonomy (Categorization)
  Term (Key)
    Value (Content)
```
**Example**: Movie Website
Let's say we are making a website about movies and make the following taxonomies:
- Actors
- Directors
- Studios
- Genre
In each one of the movies, we can specifiy terms for these taxonomies (i.e. in the metadata of each of the movie content files). Hugo would automatically create pages for each Actor, Director, Studio, and Genre with each listing of all the movies that matched that specific tag.
```
Actor                 <- Taxonomy
  Bruce Willis        <- Term
    The Sixth Sense   <- Value
    Ubreakable        <- Value
    Moonrise Kingdom  <- Value
  Samuel L. Jackson   <- Term
    Unbreakable       <- Value
    The Avengers      <- Value
    xXx               <- Value
```

## Templates
Templates are the HTML containers to display our content. They are Go Templates written in HTML with the addition of variables and functions. All Hugo themes are build using Go Templates.

## Themes
https://themes.gohugo.io/

- install themes by cloning into theme directory.
- decalre theme in config

## Commands
`hugo` - ?
`hugo -D` - build with drafts
`hugo server` - start normal local server
`hugo server -D` - start server with drafts enabled
