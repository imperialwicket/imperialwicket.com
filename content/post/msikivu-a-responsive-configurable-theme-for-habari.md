{
  "title": "Msikivu: A responsive, configurable theme for Habari",
  "description": "Msikivu: A responsive, configurable theme for Habari",
  "date": "2011-11-27",
  "url": "msikivu-a-responsive-configurable-theme-for-habari/",
  "type": "post",
  "tags": [
    "habari",
    "opensource",
    "msikivu"
  ]
}
[Get Msikivu](https://github.com/imperialwicket/msikivu)

I finally got around to upgrading Habari, and I decided it was about time for me to generate a theme. Habari does a really great job of making theme generation straight-forward to anyone with even a small amount of PHP familiarity. However, I think Habari is missing out on a lot of non-developer users as a result of not having more themes available and more configuration options in each theme. It does come with nice themes out of the box, but these themes allow only a small amount of customization. I am hoping to address this to some degree by offering a theme that still comes ready to customize for experienced developers, but gives non-developers a lot of configuration options as well.

Since I used the Habari core template Mzingi as a foundation, I am following its technique and calling my theme 'Msikivu' - which Google tells me is equivalent to 'responsive' in Swahili. When I was originally investigating, I did not see any fully responsive Habari themes, so I figured that would be a great added benefit for this theme to have. I noticed [Wazi](https://github.com/ringmaster/wazi) after I was nearly finished with the core of Msikivu, but more templates is always better than fewer for a cms.

The current version of Msikivu is available [on github](https://github.com/imperialwicket/msikivu). A few notes:

*   This started as Mzingi, and still has some leftover code that needs cleaning.
*   Responsive behavior is predominantly controlled by [Skeleton](http://getskeleton.com). I was hoping to support a wide screen version out of the box, but selected Skeleton mostly due to its small size. There are a few other nice, responsive frameworks, but most of them are easily two or three times the size of Skeleton.
*   Msikivu has many areas to use for block presentation. There are three banner areas above the content. The primary content area is comprised of a left sidebar, content, and a right sidebar. There are three footer areas below the content.
*   Each area and also the content div have configurable widths using the theme's configuration options. The defaults are displayed below, but there are a number of options since you can collapse or increase each area using quick and easy theme options. For example, I could easily change my left sidebar to zero width, increase my content to twelve, and update my banner areas to be eight, eight, four.  This would yield a two column layout with the top banner area split evenly above the main content and another banner area placed above the right sidebar.
*   There is another configuration option to allow a stylesheet specific to particular color schemes and/or designs.

For reference, this site is using the gray theme from a recent iteration of Msikivu.  Below is an image representing the default layout (before any width customizations).  As I mentioned, the widths for all blocks and the content area are configurable in the theme options, so this is really just a baseline for structural reference. The header, nav, and content areas are configured in the theme, all other areas are available for block assignment.

![msikivu_demo.png](/files/./msikivu_demo.png)

On my to do list:

1.  Restyle the admin area configuration formui with a custom template.
2.  Minor stylesheet cleanup and reorganization.
3.  Minor css cleanup and optimization.
4.  Add additional stylesheets supporting a variety of colors and styling. I am aiming for three or four color schemes and two style distinctions. The 'custom' stylesheet is also present, and will continue to contain comment css directives for at least one of the styles. This allows for simply css customizations with little investigative hassle.
5.  Add a wide screen responsive layout to the Skeleton stylesheets.
6.  Port or ensure compatibility with Habari 0.8.
7.  Possibly add styling for popular plugin blocks.
