This page is a notice on how to edit libgdx wiki pages. Please read this before contributing to the libgdx wiki.


## linking to code/docs ##
Links are done as follows: `[ClassName](link to docs) [(code)](link to code)` for example:
```
[Texture](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/Texture.html)
[(code)](https://github.com/libgdx/libgdx/tree/master/gdx/src/com/badlogic/gdx/graphics/Texture.java) 
```

renders the following:

[Texture](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/Texture.html)
[(code)](https://github.com/libgdx/libgdx/tree/master/gdx/src/com/badlogic/gdx/graphics/Texture.java) 

### Notes on Doc Links ###

* please note that there should be a space in between `ClassName (Code)` style formatting, in order to differentiate the two.

* please make the format `ClassName (Code)` with the word `Code`, not `Source` or any derivative of that. Consistency is key!

*  if a link to documentation ends in a right paren `)`, it will mess up the markdown. take this as an example: `http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/graphics/Texture.html#getWidth()`. when using the markdown formatting of `[]()` the end paren will mess up the link, so please remember to escape the ending paren (`)`) so for example:


## Videos ##

Videos are not supported on github :( so we use a small workaround by posting a screenshot of the video, which leads to a link to the youtube video. Here is the syntax:

```
<a href="http://www.youtube.com/watch?feature=player_embedded&v=YOUTUBE_VIDEO_ID_HERE
" target="_blank"><img src="http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg" 
alt="IMAGE ALT TEXT HERE" width="480" height="360" border="10" /></a>
```

Lets hope Github eventually supports embedded videos!



## The Main Table Of Contents ##

If you make a page, you will most likely want it to be displayed on the main Libgdx wiki Table of contents and the sidebar Table of Contents. When you create an article, please create a second edit of the Home page with the appropriate positioning of your article. Mirror this change in the sidebar ToC, as to maintain likeness between the two.



## Tables of Contents per page ##

The way that we are pursuing ToC's in the wiki are through `<a>` tags and links to them from the header. It resembles the following:

`[Creating a World](#creating_a_world)`

where the #creating_a_world would be the following header:

`## <a id="creating_a_world"></a>Creating a World ##`

You will have to manually create a list out of the headers. 

If there is a better solution known, please let me know on IRC (sinistersnare), i would love to have a more elegant way to do this than hacking with HTML.


## Adding Images ##

Images need to be added manually through a desktop interface (A.K.A. not through the github web interface). Images are stored in the `images/` of the libgdx wiki, which arent accessed through the libgdx wikis github interface. To add an image, you must clone the repo: `$ git clone https://github.com/libgdx/libgdx.wiki.git` add your images to the images folder using the appropriate naming scheme `my-page-name#` where # is the order of the picture displayed on the page (this can be ommitted if only one image is used in the page, but recommended). Images are linked to with the following syntax (assuming the image is stored in the `images/` directory) `[ [images/using-libgdx-with-intellij-idea01.png] ]` (without the spaces in between brackets) which will display:

[[images/using-libgdx-with-intellij-idea01.png]]


## Large Multi-Page Edits ##

Github's web interface is the only way that a non-contributor can easily edit a wiki. If a person is to make a large edit that spans multiple pages, it can be done via the web interface, but it is recommended to take the following steps:

* fork the repo
* clone your forked repo locally
* make necessary changes
* commit and push that to your forked repo
* email a libgdx maintainer (who has commit rights) to clone your repo and push it themselves.

Github does NOT have a robust pull request system for wiki changes, so this is the way it is for non-trivial changes to the wiki. If you have any problems, **PLEASE** contact support@github.com with your wishlist (hopefully something like "please make github wikis better! $IDEAS"). If you have a problem, it is necessary that you voice yourself to Github!

## Commit Messages ##
It is optional to create a commit message for a wiki edit, however it is very imperative that you make one. make a small effort to say something like the following 'fixed typo...' see the next section `Small Typo Fixes` for more information. Also please add the Page name to the commit if only editing a single page in the commit, and the commit message does not create redundancy.


### Small Typo Fixes ###
Github wiki's diff system is not as robust as the diff system for code. If you make a tiny change, in the commit message please say "Fixed typo alpa -> alpha" or something of the sort

here are some good and bad commit message examples:

good:
* `Added docs about serialization @ Reading and Writing JSON` 
* `Added info about BitmapFonts`
* `Fixed paren issue in link @ Accelerometer`
* `Added more rules for consistency`

bad: 
* `fixed typo`
* `lulhax`
* `Sinistersnare is amazing` (regardless of how true it is, this is a bad commit message)
