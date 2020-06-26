# sample-handlebars-server-render

This is a sample of the Handlebars server rendering feature of Acoustic Content (formerly Watson Content Hub or WCH). This feature lets you associate a Handlebars template with a content type and layout. Any content item can then be rendered on the server when any content item for the type is published. There are two ways that the rendering can be done:
- It can be "pre-rendered" using the Handlebars template and the resulting markup is published to the Content CDN delivery system, by setting the "prerender" flag in the layout as shown in this sample.
- It can be rendered by calling the delivery render service, using this API: <API URL>/delivery/v1/rendering/render/content/<content ID>.

Links to documentation for this WCH rendering feature: 
- https://developer.goacoustic.com/acoustic-content/docs/rendering
- https://developer.goacoustic.com/acoustic-content/docs/render-an-html-article-page-with-templates-and-layout

This sample includes two content types with associated artifacts. The content types were created in the WCH user interface.

The *Feature block* content type uses these four elements:
- featureHeadline (Text)
- descriptionOfFeature (Formatted text)
- image (Image)
- readMoreLink (Link)
 
The *Feature block list* content type uses two elements:
- listHeadline (Formatted text)
- featureBlocks (multi-value reference to Feature block)

Here is a screenshot of the rendered HTML for one of the Feature block content items:
![Alt text](/docs/images/the-open-road.jpg?raw=true "Sample screenshot")

Here is a screenshot of the HTML for the Feature block list content item:
![Alt text](/docs/images/feature-block-list.jpg?raw=true "Sample screenshot")

## Installing the samples

### Install and initialize wchtools
Install the latest version of wchtools-cli:
* Windows: npm install -g wchtools-cli 
* Linux/Mac: sudo npm install -g wchtools-cli

Make sure that you have initialized wchtools with your user and tenant API URL. You will be prompted for your user password when deploying to the tenant. For more information, refer to [https://github.com/ibm-wch/wchtools-cli/blob/master/README.md#getting-started]

### Download and install the sample
* Download this repository. 
* From the content-artifacts directory run:
    * `wchtools push -Av --publish-now`
* Open the following URL in browser:
	* {delivery-url}/samples/feature-block/the-open-road.html
* And for the Feature block list:
	* {delivery-url}/samples/feature-block-list/feature-block-list-sample.html

For the value for {delivery-url}, use the "Delivery URL" value from the Watson Content Hub Information panel.

## Notes on publishing content and making updates
After you have initially uploaded and published the sample, when any Feature block or Feature block list is published, the corresponding HTML will be published on the CDN. 

Note that there is a default 11-minute cache time-out for the CDN. To clear the cache and view the updated HTML, run this command:
* `wchtools clear —cache`

To promote a change to a Handlebars template or layout JSON:
- Use `wchtools push -Av` to update all the edited artifacts.
- Use `wchtools render -r` to re-render the content.
- Use `wchtools clear —cache` to clear the CDN cache.

### Bypassing the CDN cache by using the delivery resources API

To avoid having to clear the CDN cache, you can use a URL to the delivery resources service, specifying the path to the generated HTML file as an argument:
* {api-url}/delivery/v1/resources?path={path-to-HTML}
* In our examples:
    - {api-url}/delivery/v1/resources?path=/samples/feature-block/the-open-road.html
    - {api-url}/delivery/v1/resources?path=/samples/feature-block-list/feature-block-list-sample.html

For the value for {api-url}, use the "API URL" value from the Watson Content Hub's  "Hub Information" panel.


## Notes on syntax used in the Handlebars templates

To output a simple text element:
```
	{{elements.featureHeadline.value}}
```

To output Formatted text that may include HTML tags, use the triple curly brace syntax:
```
	{{{elements.descriptionOfFeature.value}}}
```

To render an image:
```
	<img src="{{elements.image.url}} " alt=" " width="100% ">
```

To render a multi-value reference element:
```
    {{#each elements.featureBlocks.values}}
        <div class="row">
            {{markup}}
        </div>
    {{/each}}
```
 
 To output the entire rendering context JSON:
```
    <pre>{{stringify .}}</pre> 
```

## Description of files and folders under content-artifacts

/assets/dxdam/
  - The sample images for Feature block content items.

/assets/templates/feature-block.hbs
  
/assets/templates/feature-block-list.hbs
  - The Handlebars template files that are used to render content.

/content/
  - The content items used in the sample.

/layouts/feature-block-layout.json
 
/layouts/feature-block-list-layout.json
  - The layout descriptors for the two content types. The layout includes the name of the Handlebars template file
  - Note, the urlTemplate field in the layout, determines the resulting delivery url for the pre-rendered html
    - eg:   "urlTemplate": "/samples/feature-block/{name}.html"  results in {delivery-url}/samples/feature-block/{content-item-name}.html being rendered, when that layout is applied to a content item during rendering.

/layout-mappings/feature-block-mappings.json

/layout-mappings/feature-block-list-mappings.json
  - These files associates the layout descriptors with the content types.

/types/feature-block.json

/types/feature-block-list.json
  - The two content types described above.

