{
    "title":"hugo-resume fix some css",
    "link":"https://github.com/eddiewebb/hugo-resume/pull/36",
    "image":"",
    "description":"Fix a responsive design issue that made blog posts not fit within the parent container.",
    "tags":["css","hugo-resume","responsive design"],
    "fact":"",
    "weight":"999",
    "sitemap": {"priority" : "0.8"},
	"featured":true
}

It feels good to fix the theme I use for the blog, I give back to a tool I will use for some time hopefully :)

The problem was that when looking at blog post with full resolution on my laptop, it displayed nicely. But when viewing it with smartphone resolution, it looked like this:

{{< figure src="/img/contributions_width_overflow.png" alt="blog post with text overflowing">}}

After some duckduckgoing, I stumbled upon `box-sizing: border-box`, an element I have not used before.I went to MDN to see what is was [MDN page on box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing), then looked around if it could be that element tripping me up. 

But according to that doc, because the CSS used `border-box` instead of `content-box`, my content should have behaved nicely and not overflowing. 

After more time looking around, [a StackOverflow response](https://stackoverflow.com/a/38892861/3729797) once again saved the day. When using `border-box` on a container, you have to set the width on the inner child. 

I added `width: 100%` on the inner child, and "voila":

{{< figure src="/img/contributions_without_width_overflow.png" alt="blog post with text overflowing">}}