{
    "title":"Tiny contribution to Ocaml's GC documentation",
    "link":"https://github.com/ocaml/ocaml/pull/9399",
    "image":"",
    "description":"Fix documentation mismatch with struct.",
    "tags":["ocaml","documentation", "gc"],
    "fact":"",
    "weight":"999",
    "sitemap": {"priority" : "0.8"},
	"featured":true
}

While looking at an article explaining the new "best fit" GC strategy for OCaml [here](http://www.ocamlpro.com/2020/03/23/ocaml-new-best-fit-garbage-collector/), they had a link toward the source code and I went to see what it looks like.

[Here is the source](https://github.com/ocaml/ocaml/blob/trunk/runtime/freelist.c#L882) I was looking at, and the string documenting the struct did not have the same name for the field in the struct. It's a tiny tiny thing but the kind that feels messy when you read the code.