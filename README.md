Slimsy
============
**Effortless Responsive Images with Slimmage and Umbraco**

![](Slimsy.png)

[![Build status](https://ci.appveyor.com/api/projects/status/a7rxrfkxc5dx8cuo?svg=true)](https://ci.appveyor.com/project/JeavonLeopold/slimsy)

Umbraco Package: [https://our.umbraco.org/projects/website-utilities/slimsy](https://our.umbraco.org/projects/website-utilities/slimsy)
<br/>Nuget Package: `Install-Package Our.Umbraco.Slimsy`
<br/>MyGet Latest Build Package: [https://www.myget.org/gallery/slimsy](https://www.myget.org/gallery/slimsy)


**Note** This package requires Umbraco v7.1.3+

Slimmage.js used in conjunction with ImageProcessor.Web (included in Umbraco v7.1) and the built-in Umbraco Image Cropper will make your responsive websites images both adaptive and "retina" quality (if supported by the client browser).

Slimsy installs everything you need to use Slimmage.js in Umbraco v7.1. It includes Slimmage.js, SlimResponse and some helper methods that work with both dynamic and typed published content models.


## Implementing post package installation

### 1. Add slimmage.js to your pages

In your master template add the Slimmage Javascript file(s) to the top of your head section (Slimmage should be the first js library)

**without bundling scripts (plain HTTP requests)**

```
	<script type="text/javascript">
		window.slimmage = { verbose: false };
	</script>
	<script src="/scripts/slimmage.min.js"></script>
```

**with bundling of your scripts you can fetch the configuration from a separate file**

    	<script src="/scripts/slimmage.settings.js"></script>
    	<script src="/scripts/slimmage.min.js"></script>

If you don't already have js bundling in place you should take a look at the [Optimus](http://our.umbraco.org/projects/developer-tools/optimus) package, it will allow you to bundle them together in minutes.

### 2. Adjust your image src attributes

Use the GetResponsiveImageUrl or GetResponsiveCropUrl methods on your dynamic or typed content/media items. For these methods to function correctly your image property types should use the built-in **Image Cropper**.

#### GetResponsiveImageUrl(width, height)
use this method for setting the crop dimensions in your Razor code, assumes your image cropper property alias is "umbracoFile"

e.g. An initial image size of 270 x 161. This example is looping pages, each page has a media picker with property alias "Image"

    @foreach (var feature in homePage.umbTextPages.Where("featuredPage"))
    {
        <div class="3u">
            <!-- Feature -->
            <section class="is-feature">
                @if (feature.HasValue("Image"))
                {
                    var featureImage = Umbraco.Media(feature.Image);
                    <a href="@feature.Url" class="image image-full">
					<img src="@featureImage.GetResponsiveImageUrl(270, 161)" alt="" />
					</a>
                }
                <h3><a href="@feature.Url">@feature.Name</a></h3>
                @Umbraco.Truncate(feature.BodyText, 100)
            </section>
            <!-- /Feature -->
        </div>
    }

e.g. If you need only a width dimension (and a flexible height) set height parameter to 0

		<img src="@featureImage.GetResponsiveImageUrl(270, 0)" alt="" />

#### GetResponsiveImageUrl(width, height, propertyAlias)
Overloaded method, with additional propertyAlias parameter to specify your image cropper property alias.


e.g. An initial image size of 270 x 161, Image Cropper property alias of "Image"

		<img src="@featureImage.GetResponsiveImageUrl(270, 161, "Image")" alt="" />

e.g. If you need only a width dimension (and a flexible height) set height parameter to 0

		<img src="@featuredNewsItem.GetResponsiveImageUrl(859, 0, "Image")" alt="" />

#### GetResponsiveImageUrl(width, height, propertyAlias, outputFormat)
Overloaded method, with additional outputFormat parameter so you can specify if this image should be returned as a specific format type.

e.g. If you wanted a image to always be returned as a png

		<img src="@featureImage.GetResponsiveImageUrl(270, 161, "Image", "png")" alt="" />

#### GetResponsiveCropUrl(cropAlias)
use this method when you want to use a predefined crop, assumes your image cropper property alias is "umbracoFile".

		<img src="@featureImage.GetResponsiveCropUrl("home")" alt="" />

#### GetResponsiveCropUrl(cropAlias, propertyAlias)
Overloaded method, with additional propertyAlias parameter to specify your image cropper property alias.

		<img src="@featureImage.GetResponsiveCropUrl("home", "Image")" alt="" />

#### GetResponsiveCropUrl(cropAlias, propertyAlias, outputFormat)
Overloaded method, with additional outputFormat parameter so you can specify if this image should be returned as a specific format type.

		<img src="@featureImage.GetResponsiveCropUrl("home", "Image", "png")" alt="" />

# Advanced Options

By default Slimsy v1.1.0+ will request that all images generated by ImageProcessor are jpg as this provides the best compression and optimisation, this is regardless of the source image type. You can disable this behaviour by adding the following appsetting to web.config.

    <add key="Slimsy:Format" value="false"/>

The above overloaded methods specifying outputFormat will of course take precedence over this default.

If you really wanted to have all default URLs return pngs you could also do this

    <add key="Slimsy:Format" value="png"/>

Added in Slimsy v1.1.2 - you can now specify the default background color by added another appsetting to web.config. As an exmaple this setting is used if ImageProcessor is converting a png to a jpg and it as some transparency

	<add key="Slimsy:BGColor" value="fff"/>

# Sliders/Carousels

When using a JavaScript slider it is likely that you will need to activate the slider once Slimmage has completed it's slimming. e.g.:

    window.slimmage.readyCallback = function() {
            $('.bxslider').bxSlider();
        }

Slimmage is only compatible with Sliders where the image dimensions can be computed at  browser render (not hidden by CSS).

Here are a few that are known to be compatible:

- [bxSlider](http://bxslider.com/) (free WTFPL)
- [carouFredSel](http://dev7studios.com/plugins/caroufredsel/) (licensed)

# Upgrades

Slimsy v1.1.4 includes the update to Slimmage.js v0.4.1 - for this version of Slimmage you must ensure your images have the `max-width:100%` css property.

# Test Site

A test site is included in the solution, the username and password for Umbraco are admin/password.
By default the test site is configured to use full IIS (due to IIS Express SQL CE persistence issue) on the domain slimsy.local, you can change it to use IIS Express if you prefer.

# Credits and references

This project includes [Slimmage](https://github.com/imazen/slimmage) and [SlimResponse](https://github.com/imazen/slimresponse) Both projects are MIT/Apache dual licensed by Imazen. The Umbraco package also includes [HtmlAgilityPack](http://htmlagilitypack.codeplex.com/) licensed under the [MS-PL](http://opensource.org/licenses/MS-PL).

Many thanks to Douglas Robar for naming Slimsy.

# Change log

[Here](Changelog.md)