# Make Jupyter-generated Reveal decks more manageable

Let's say you tell IPython to turn your notebook into a [Reveal.js][1] slide show:

```bash
ipython nbconvert slides.ipynb --to slides
```

The output is a file called `slides.slides.html` that is **on the order of 10,000 lines long**. The vast majority of this code consists of style sheets that ought to be in separate files. If you want to customize some aspect of your slideshow, you shouldn't need to arm wrestle a 10,000-line file to do it. In fact, the beauty of writing slides in HTML is that the content is light-weight, plaintext markup. Editing a Reveal slide deck can be quite simple if the style and script components are properly broken out.

This repo lets me paste the content of my presentation into a relatively small HTML file and let the "style" and "script" directories handle the rest.

In addition to making it easier to edit Jupyter-generated Reveal slide decks, this repo also includes everything necessary for rendering. You should be able to open your `slides.html` and have everything work even if you're disconnected.

The rest of this document explains what I did.

## Breaking out style sheets
### Bootstrap
As any web developer will tell you, it is much better to reference style sheets and scripts in other files than to copy everything into one HTML file. Unfortunately, our exported `slides.slides.html` contains an entire un-minified version of Bootstrap. Let's move that somewhere else.

Bootstrap begins on line 44 and ends at line 6341. I replaced all of this with one line:

```html
<link rel="stylesheet" type="text/css" href="bootstrap/css/bootstrap.min.css">
```

Later we are going to include bootstrap locally to avoid dependence on internet connectivity.

### Font Awesome
The entirety of Font Awesome is included too. Again, we're just going to replace all that CSS with a link to Font Awesome.

With Bootstrap removed, Font Awesome should start around line 46 and continue until line 1851. I replaced all that with the following line:

```html
<!-- Font Awesome CSS -->
<link rel="stylesheet" type="text/css" href="font-awesome/css/font-awesome.min.css">
```

### IPython CSS
Almost done referencing stylesheets. The remaining contents of the `<style>` block are taken directly from a file called `ipython.min.css` located in the IPython repository.

Okay, we have successfully replaced the first `<style>` block with three lines of HTML. The remaining two `<style>` blocks contain miscellaneous CSS that I broke out into a new file called `misc.css`.

For some reason the file includes a CDN reference to an older version of Font Awesome than the one pasted into it:

```html
<!-- Get Font-awesome from cdn -->
<link rel="stylesheet" href="//netdna.bootstrapcdn.com/font-awesome/4.1.0/css/font-awesome.css">
```
I deleted this too.

## Scripts
### MathJax
I included a local MathJax. I know it's a big chunk of files, but it's important to me to remove the internet connection dependency.

### JQuery
I also included JQuery here. This was an easier decision to make: the file isn't nearly as large as MathJax.

___
I would like to modify nbconvert to make it perform these steps automatically, but the Jupyter development team is currently splitting the IPython project into several repos for independent development. I am waiting for the nbconvert repo to stabilize.

[1]: http://lab.hakim.se/reveal-js/
