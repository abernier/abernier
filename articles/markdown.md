---
title: Markdown cheatsheet
tags: md, syntax, github
---

Markdown cheatsheet
===

Misc
---

> **Warning** Be careful

> **Note** <br>my subtitle note
>
> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse molestie pretium diam, eu varius nisi malesuada vitae. Phasellus quis turpis ut ipsum gravida varius.
>
> ```js
> // code also works inside
> console.log("this is cool")
> ```
>
> Lists also:
> - [ ] task A
>   - [ ] subtask A.1
>   - [x] subtask A.2 done
> - [ ] task B
>
> Tables too
> |ColA|ColB|
> |-|-|
> |one|two|
>
> > Even quotes :D
> > > That's crazy man!
>
> ## Even headings
> ### Or subheadings
>
> <details><summary>Details also</summary>
> <div>
> 
> #### We can hide anything, even code![^1]
> [^1]: My reference.
> 
> ```ruby
>    puts "Hello World"
> ```
> 
> </div>
> </details>
>
> and permalinks
> https://github.com/abernier/abernier/blob/81876e46681f01d4f0d80c7b80489ecc7dfbaedc/docs/test.md?plain=1#L1

<!--
I'm a comment
-->

<sup><sub>I'm small text</sub></sup>

Here is a list:

- A [relative link](docs/test.md) that points to this repo's file `docs/test.md`
- A link to another repo: https://github.com/abernier/vertex-journey [^foo]
- And to its `#readme` section: https://github.com/abernier/vertex-journey#readme [^foo]
- A link to code line 16—17 (external repo): https://github.com/abernier/vertex-journey/blob/fa786ff1882843b2ac56e086651c7eb25a5b3663/src/index.js#L16-L17 [^bar]
[^bar]: I'm the `bar` footnote
- A permalink to an deleted/old file (of this repo): https://github.com/abernier/abernier/blob/81876e46681f01d4f0d80c7b80489ecc7dfbaedc/docs/test.md?plain=1#L1
- a link to a closed issue: https://github.com/abernier/abernier/issues/1
- some keyboard keys: <kbd>w</kbd><kbd>a</kbd><kbd>s</kbd><kbd>d</kbd> + <kbd>space</kbd>
- An edited text: What the <del>fuck</del><ins>hell</ins>!

[^foo]: I'm the `foo` foonote!

|ColA|ColB|
|-|-|
|one|two|

> hey I'm fine!
>> Hey how are you

Collapse
---

<details><summary>CLICK ME</summary>
<div>
  
#### We can hide anything, even code![^1]
[^1]: My reference.

```ruby
   puts "Hello World"
```
  
</div>
</details>

Tasks
---

TODO:
- [ ] task A
  - [ ] subtask A.1
  - [x] subtask A.2 done
- [ ] task B

https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/about-task-lists

## Math

**The Cauchy-Schwarz Inequality**
$$\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)$$

Math expressions - @see: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/writing-mathematical-expressions

## mermaid, stl, geojson

https://github.blog/changelog/label/markdown/#:~:text=Mermaid%2C%20topoJSON%2C%20geoJSON%2C%20and%20ASCII%20STL%20Diagrams%20Are%20Now%20Supported%20in%20Markdown%20and%20as%20Files

```mermaid
  graph TD;
      A-->B;
      A-->C;
      B-->D;
      C-->D;
```
https://github.blog/2022-02-14-include-diagrams-markdown-files-mermaid/

```stl
solid Mesh
  facet normal 1.000000 0.000000 0.000000
    outer loop
      vertex 10.000000 0.000000 0.000000
      vertex 10.000000 -20.000000 20.000000
      vertex 10.000000 -20.000000 0.000000
    endloop
  endfacet
  facet normal 1.000000 0.000000 0.000000
    outer loop
      vertex 10.000000 0.000000 20.000000
      vertex 10.000000 -20.000000 20.000000
      vertex 10.000000 0.000000 0.000000
    endloop
  endfacet
  facet normal -1.000000 0.000000 0.000000
    outer loop
      vertex -10.000000 -20.000000 0.000000
      vertex -10.000000 0.000000 20.000000
      vertex -10.000000 0.000000 0.000000
    endloop
  endfacet
  facet normal -1.000000 0.000000 0.000000
    outer loop
      vertex -10.000000 -20.000000 20.000000
      vertex -10.000000 0.000000 20.000000
      vertex -10.000000 -20.000000 0.000000
    endloop
  endfacet
  facet normal 0.000000 0.000000 1.000000
    outer loop
      vertex -10.000000 -20.000000 20.000000
      vertex 10.000000 0.000000 20.000000
      vertex -10.000000 0.000000 20.000000
    endloop
  endfacet
  facet normal 0.000000 -0.000000 1.000000
    outer loop
      vertex 10.000000 -20.000000 20.000000
      vertex 10.000000 0.000000 20.000000
      vertex -10.000000 -20.000000 20.000000
    endloop
  endfacet
  facet normal 0.000000 0.000000 -1.000000
    outer loop
      vertex -10.000000 0.000000 0.000000
      vertex 10.000000 -20.000000 0.000000
      vertex -10.000000 -20.000000 0.000000
    endloop
  endfacet
  facet normal -0.000000 -0.000000 -1.000000
    outer loop
      vertex 10.000000 0.000000 0.000000
      vertex 10.000000 -20.000000 0.000000
      vertex -10.000000 0.000000 0.000000
    endloop
  endfacet
  facet normal 0.000000 1.000000 0.000000
    outer loop
      vertex -10.000000 0.000000 0.000000
      vertex 10.000000 0.000000 20.000000
      vertex 10.000000 0.000000 0.000000
    endloop
  endfacet
  facet normal -0.000000 1.000000 0.000000
    outer loop
      vertex -10.000000 0.000000 20.000000
      vertex 10.000000 0.000000 20.000000
      vertex -10.000000 0.000000 0.000000
    endloop
  endfacet
  facet normal 0.000000 -1.000000 0.000000
    outer loop
      vertex 10.000000 -20.000000 0.000000
      vertex -10.000000 -20.000000 20.000000
      vertex -10.000000 -20.000000 0.000000
    endloop
  endfacet
  facet normal -0.000000 -1.000000 -0.000000
    outer loop
      vertex 10.000000 -20.000000 20.000000
      vertex -10.000000 -20.000000 20.000000
      vertex 10.000000 -20.000000 0.000000
    endloop
  endfacet
endsolid Mesh
```

```geojson
{
  "type": "Polygon",
  "coordinates": [
      [
          [-90,30],
          [-90,35],
          [-90,35],
          [-85,35],
          [-85,30]
      ]
  ]
}
```

Ressources
---

- SPEC: https://github.github.com/gfm
- https://github.blog/tag/markdown/
- https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/autolinked-references-and-urls
