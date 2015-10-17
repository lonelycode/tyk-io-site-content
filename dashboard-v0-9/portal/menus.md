+++
draft = false
title = "Menus"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
	parent = "tyk-portal"
    weight = -230
+++

The portal supports a data structure to hold rudimentary menus, all pages have access to all menus, and can be accessed using 
the `.Menus.MenuName` field tag. They are arrays that consist of slugs and names, an implementation example would be:

	<ul class="nav navbar-nav">
	  {{ range $index, $menuItem := .Menus.Main}}
	  <li><a href="/portal/{{$menuItem.URL}}">{{$menuItem.Title}}</a></li>
	  {{ end }}
	  <li><a href="/portal/apis/">API Catalogue</a></li>
	  {{ if not .PortalConfig.DisableSignup }}
	  	{{ if not .UserData }}
	  	<li><a href="/portal/register/">Register</a></li>
	  	{{ end }}
	  {{ end }}
	  {{ if not .PortalConfig.DisableLogin }}
	  	{{ if not .UserData }}
	  	<li><a href="/portal/login/">Log in</a></li>
	  	{{ end }}
	  {{ end }}
    </ul>
    
In the snippet above we can also see a bunch of settings fields, in order to react to the configuration of the portal, the 
core portal config object is exposed to the template and can be used to change how the template is rendered.