{
  "title": "iwBase: A responsive starter theme for Habari",
  "description": "iwBase: A responsive starter theme for Habari",
  "date": "2011-12-18",
  "url": "iwbase-a-responsive-starter-theme-for-habari/",
  "type": "post",
  "tags": [
    "habari",
    "iwbase"
  ]
}
iwBase is a responsive starter theme for Habari. It comes with two stylesheets supporting responsive fixed and responsive fluid layouts. The stylesheets include empty directives for all core areas, and leave most styling up to you. iwBase borrows heavily from [Skeleton](http://getskeleton.com), as well as the core [Habari](http://habariproject.org) themes Charcoal and K2 (K2 was a core theme until version 0.8 of Habari). 

[Download iwBase 0.8.1 from GitHub](https://github.com/imperialwicket/iwBase/zipball/0.8.1)

![iwBase.png](/files/./iwBase.png)

iwBase is intended as a few-frills starter theme. The image shows the header with Title, tagline, and navigation (navigation is hard-coded in the header, I did not want to include a custom menu block). Beneath the header is the left content section which includes a top banner area, and the main content display. The sidebar area is configured on the right. A bottom banner area covers the full width at the bottom of the page, directly above the footer, which displays a basic 'powered by habari' message and the atom links.

The theme includes a configuration option that selects either the fluid or fixed stylesheet.  The main and sidebar columns stack on tablet width screens and smaller.

I have not tested reverse compatibility, and assume that there will be minor issues in versions of Habari < 0.8\. If lots of people want iwBase for < 0.8, I'll consider back-porting. Honestly though, you should upgrade, and I'll likely just point you to [Msikivu](http://imperialwicket.com/msikivu-a-responsive-configurable-theme-for-habari) (which supports 0.7) or some other theme.
