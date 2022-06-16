# hugo-shortcode-gallery

This is a theme component for hugo.

This component contains a shortcode to include a gallery in your .md files.

The gallery is rendered using autogenerated thumbnails arranged in a
[grid](http://miromannino.github.io/Justified-Gallery/). With a click on the images
a [lightbox](http://brutaldesign.github.io/swipebox/) is opened an all images can be
viewed fullscreen.

# Demo

You can see this shortcode-gallery in action on [my website](https://matze.rocks/images/).

## Installation

Clone this git repository into your _themes_ folder.

```
git clone https://github.com/mfg92/hugo-shortcode-gallery.git
```

Next edit your projects
_config.toml_ and add this theme component to your themes:

```
theme = ["your-main-theme", "hugo-shortcode-gallery"]
```

To read about hugo's theme components and how to use them have a look at
https://gohugo.io/hugo-modules/theme-components/.

## Usage Example

Here is an usage example:

```
{{< gallery match="images/*" sortOrder="desc" rowHeight="150" margins="5" thumbnailResizeOptions="600x600 q90 Lanczos" showExif=true previewType="blur" embedPreview="true" >}}
```

This shortcode will generate a gallery containing all images of the folder _images_.
The folder must be next to the .md file where this gallery is used in. This uses [page bundles](https://gohugo.io/content-management/page-bundles/)
so the directory layout should look like this:

```
new-post-name/
    index.md
    images/
        DSC_0001.jpg
        DSC_0002.jpg
```

The parameter `sortOrder` decides whether the images are sorted ascending ("asc") or descending ("desc").

The `rowHeight` parameter determines the height of the rows that are displayed while the
`margin` parameter defines the gap between the images.

A thumbnail is generated using the `thumbnailResizeOptions` parameter, they are handed over
to _Hugo's_ [image processing](https://gohugo.io/content-management/image-processing/)
function using the fit method. In the example above, the generated thumbnails have a width of max 600 pixel and
a height of max 600, the actual width and height depend on the original aspect ratio. The JPEG image quality is 90% and the
scaling uses the high quality _Lanczos_ filter.

If `previewType` is set to "blur" (or "color"), a very low resolution image (or a single pixel image) will be loaded for every image in the gallery first.
The hight resolution thumbnail images (see `thumbnailResizeOptions`) will only be loaded if they are on the currently visible part of the page (or close to it).
This leads to a faster loading page. You can set `previewType` to "none" to disable this feature and all thumbnails will be directly loaded.

Enable `embedPreview` to let hugo embed the tiny preview image directly in the page HTML as a base64 strings. This reduces the amount of required network round trip times.

The setting `thumbnailHoverEffect` configures what should happen when the mouse hovers above a thumbnail in the gallery.
It defaults to "none", but it can be set to "enlarge", in that case the image is scaled up (x1.1) in a short smooth animation.

The size of the image as shown in the gallery can be customised using the (optional) `imageResizeOptions` parameter. The syntax is the same as for `thumbnailResizeOptions`. If ommited, the image will be displayed in its original size.

The setting `lastRow` configures the justification of the last row of the grid. When set to "justify", the entire grid including the last row will be fully-justified, right and left. This parameter respects all of the `lastRow` options of Justified Gallery, including "nojustify" and "hide".

When the users clicks on an image, a lightbox shows up displaying the clicked image in large using the whole available space.
If the image contains a title/description in the EXIF metadata field _ImageDescription_ or a title is defined in the image's sidecar file (see section below) there will be a top bar displaying that.
If the `showExif` option is set to `true` (without quotes), some parts of the image's EXIF data will be shown on the bottom bar e.g.: "Canon EOS 80D + EF100-400mm f/4.5-5.6L IS II USM 400mm f/8 1/400sec ISO 2500".
The EXIF display will only work if you add following lines to your _config.toml_:

```TOML
[imaging.exif]
    includeFields = ".*"
```

An advanced setting is `filterOptions`: It allows the user to filter the displayed images by using buttons.
The text of the buttons and the regex used to filter has to be specified in a JSON array of objects. Currently it is only supported to filter by EXIF tags, image description, start rating or color labels. In the future it will be possible to filter by image name or other EXIF fields (pull requests are welcome). In addition to the metadata of the EXIF embedded in the image, a metadata sidecar file (see section below) can be used to add metadata for filtering.

Additionally to the filter buttons, a button to activate full screen mode of the gallery is added.

An example of the `filterOptions` JSON:

```
filterOptions="[{label: 'All', tags: '.*'}, {label: 'Birds', tags: 'bird'}, {label: 'Macro', tags: 'macro'}, {label: 'Insects', tags: 'insect'}]"
```

When `filterOptions` is used, the switch `storeSelectedFilterInUrl` can be set to `true`. This will instruct the gallery to append the name of the filter to the url displayed in the browser when a filter button is clicked. This has two purposes: The user can share this link and recipients will see the gallery with the same filter as the original user. Furthermore the selected filter is stored in the browsers history.

As many websites/themes already include _jQuery_, this theme component will use the available _jQuery_ lib.
If the page does not already use _jQuery_ the parameter `loadJQuery=true` must be used to
instruct the theme component to load the provided _jQuery_ lib.

All settings can be done globally in the site's _config.toml_, for that the prefix `gallery` has to be used. E.g. `galleryLoadJQuery` instead of `loadJQuery`.

## Sidecar files

The metadata embedded in a image can be extended/overshadowed by a metadata sidecar file. The file must have the same name as the image plus ".meta" (e.g. "image.jpg.meta"). The content has to be a _JSON_ like:

```JSON
{
"Tags": ["macro","insect"],
"Title": "Maya the Bee",
"ColorLabels": "RG",
"Rating": 3
}
```

## Requirements

This component requires a hugo **extended** version >= 0.83.

## Dependencies

The component uses (and includes) [_Justified Gallery_](http://miromannino.github.io/Justified-Gallery/)
to render the images between the text and [_Swipebox_](http://brutaldesign.github.io/swipebox/)
to show them full screen. These dependencies are included in this repository.

## Troubleshooting

When bigger galleries are processed it can be required to set hugo's timeout property in the _config.toml_ to a higher value:

```
timeout = 60000 # This is required for larger galleries to be build (60 sec)
```
