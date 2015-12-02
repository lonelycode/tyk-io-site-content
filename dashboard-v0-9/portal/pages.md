+++
draft = false
title = "Pages"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -240
+++

Tyk pages are rudimentary, single-slug HTML pages that can be rendered to the portal, the template for each page is by
default in `portal/templates/page.html`, though others can be used by registering a new template code with the template file
using the `{{ define "newPageCode" }}` directive and reloading the dashboard instance.

All pages by default expose a `{{.Title}}` field along with any custom field name you create in the pages editor. Fields that
are created in the page editor can be accessed using the same dot-notation as the title field and are sub-fields of the 
`Fields` section.

For example, to expose a field called `"MainBody"`, we can access it like so:

	<div class="col-md-10">
		<h1>{{ .Page.Title }}</h1>
	
		{{ .Page.Fields.MainBody | markDown }}
	</div>

### Markdown

As you can see from the above snippet, the piped `| markDown` operator will try to render the content as markdown, this can be
very helpful for generating content without having to add custom markup into the raw content.

### Custom pages and URLs

When you create a new page, no matter what template is being used, the URL is always relative to `/portal` or your portal-root, if you have set this in the `tyk_analytics.conf` file.

So a page with the slug "test-page", would be under "/portal/test-page"

To define which template to use (if not using the defaults), you will need to set it in the "custom page code" section of the page editor. Just use the name that is at the top of the HTML template as defined in the "define" tag, for the home page it's:

	{{ define "indexPage" }}

And for a standard page it's:

	{{ define "portalPage" }}

To load a custom template you will need to restart the dashboard app though to make sure the templates are cached.
