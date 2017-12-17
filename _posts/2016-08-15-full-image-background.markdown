---
title:  "01 - Full image background in CSS3"
date:   2016-08-15 11:30:23
categories: [Development]
tags: [programming]
---
While setting background image for one of my websites, I was trying to set a background having following characteristics:

- Entire page should be filled with image, no white space
- No scrollbar, due to the image
- Aspect Ratio should be maintained
- Image should be centered
- Cross-browser compatibility
- Site should look like a flash site, though it’s a RoR site

So, after an hour effort, I had my site’s look as per my requirement. Here is the code

```css
html {
background: url("/assets/bg.gif) no-repeat center center     fixed;
-webkit-background-size: cover;
-moz-background-size: cover;
-o-background-size: cover;
background-size: cover;
}
```

Thanks to background-size property of CSS3.

Now some tips:

html element is better than body as it’s always has the height of the browser at least. Image is set as fixed and centered, then its size is set using background-size and cover keyword.

It’s better to save the image as gif, as png, jpeg has larger size.For better performance and less loading time , we should kep our page of small size.

It works in

- ​    IE9+
- ​    Firefox3.6+
- ​    Chrome
- ​    Safari3+
