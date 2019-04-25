---
layout: post
title:  "Lightning Palette - A Quick, Usable, Color Scheme Generator"
date:   2019-03-21 17:25:00 -0700
categories: 
---

## Overview
Lightning Palette is a simple web app with a simple purpose -- allow anyone to create a harmonius color scheme at the press of a button, regardless of whether they have a background in color theory or design.

<a href="https://jcng.github.io/lightning-palette/"><img src="/blog/images/screenshots/lightning-palette-thumb.jpg" alt="Lightning Palette Screenshot" class="postImg" style="width: 70%"/></a>

**[View Project](https://jcng.github.io/lightning-palette/)**

**[Source](https://github.com/jcng/lightning-palette/tree/master/js)**

*Technology Used: HTML, CSS, JavaScript (ES6), [MaterializeCSS](https://materializecss.com/)*

## Context

Let's imagine a man named Aaron, the owner of a small shop that sells exotic cheese to well-off customers. He knows a lot about cheese, but not much about design or color theory. Despite this, he's looking to revamp his social media presence, and part of that is making a logo for himself.

After a few false starts, Aaron draws up a dapper cheese wheel that he's happy with using to brand his shop. Unfortunately, when it comes to add color, he quickly finds that creating a harmonius color scheme isn't as easy as just choosing his three favorite colors. Perhaps he looks up some tips and sees something about complementary colors. "Complementary" sounds like a good thing right? He tries a few complementary combos and is dismayed to find that, while visually exciting, they make his store look more like a sports bar than a sophisticated cheese store.

For someone like Aaron who's unfamiliar with color theory finding a combination of colors that look good together can be frustrating. The goal of Lightning Palette is to abstract those frustrations away behind theming descriptions that are easy for anybody to understand.<!--break--> 

(Okay, let's be real. I mostly just wanted a project to play around with and practice, and this is just a loose justification for its existence.)

## Development Details

### Concept
I actually started working on this little side project because of a practice problem shared in a JavaScript Discord I joined. The problem was simple: generate a random hexadecimal using vanilla JS.

My solution was straightforward: create a string containing all the possible characters in a hexadecimal, then randomly choose from them and concatenate.
``` JavaScript
/**
 * Returns hexadecimal color
 */
function randomColor() {
    const hexChars = '0123456789ABCDEF';
    let color = '#';
    for (let i = 0; i < 6; i++) {
        let code = Math.floor(Math.random() * 16);
        color += hexChars.charAt(code);
    }
    return color;
}
```

After solving the problem, I thought it would be a fun little exercise to make this color scheme generator. My goal was to constrain the randomness of color generation to harmonious combinations: analogous (three closely spaced colors on a color wheel), split complementary (two closely spaced colors opposite on the wheel from another color), and triadic (three evenly spaced colors).

### Color Conversion
Setting up the color harmonies was trickier than I anticipated. The most intuitive way to handle color shifting is by taking a base color and finding colors to combine with it by shifting it by an angle on the color wheel. Unfortunately, Hexadecimal is not very friendly to making angular adjustments like that. Because of this, I had to do some pretty math-heavy calculations to convery the hexadecimal to a RGB color, then convert the RGB color to HSL, which could then be adjusted angularly. Luckily, people smarter than me already figured out those calculations.

### Not True Random
After getting the hue shifting calculations right, I immediately ran into another issue: Truly random color schemes will produce a lot of unusable color combinations. If the base color's saturation is too low, or if the lightness is too extreme on either end, then any adjustments to hue will not be noticeable (e.g. no matter how much you shift the hue of true black, it will always stay black). Obviously, this is not conducive of good color schemes. I took the easy route to solve this by setting up hard boundaries for saturation and lightness values:

```JavaScript
let color = rgbToHsl(hexToRgb(randomColor()));
color.s = getRandomNumber(0.5, 1);
color.l = getRandomNumber(0.5, 0.8);
```
It's possible that these could be tweaked to allow a higher range of colors.

### Usability Tweaks
At this point, the base functionality of the app was done, so I worked on a few tweaks to make it easier to use. The first was setting the swatch text to contrast with the color it was on top of. This was pretty easy to accomplsih with some CSS:
```CSS
.swatch-text {
    font-family: "Segoe UI",Roboto,Oxygen-Sans,Ubuntu,Cantarell,"Helvetica Neue",sans-serif;
    font-weight: bold;
    background: inherit;
    -webkit-background-clip: text;
            background-clip: text;
    color: transparent;
    filter: invert(1) grayscale(1) contrast(100);
    text-transform: uppercase;
}
```
The other small addition I made was to allow the user to just click on a swatch to copy the associated hexadecimal, instead of having to manually highlight.

```JavaScript
function updateClipboard(newClip) {
    navigator.clipboard.writeText(newClip);
}

//...

let swatches = document.getElementsByClassName('swatch');
    for (s of swatches) {
        s.addEventListener('click', (event) => {
            if (event.target.className === 'swatch-text') {
                let el = event.target;
                updateClipboard(el.innerHTML);
                M.toast({html: "Copied!"}); // this is just for Materialize
                
            }
            else if (event.target.className.includes('swatch ')) {
                let el = event.target.children[0];
                updateClipboard(el.innerHTML);
                M.toast({ html: "Copied!",
                          displayLength: 1000,
                          classes: "tost-copied"
                         });
            }
        })
}
```

### Materialize

While I was working on this project, I also wanted to give MaterializeCSS a go. I was impressed by how easy it was to get going with it. The documentation is great, so I was able to get everything working for my project very quickly. However, it does accomplish this ease-of-use by being fairly strongly opininionated. When I wanted to do things that overrided the default functionality, it became more of a pain to work with.

For something like this project where I don't care too much about specifically how it looks, I think MaterializeCSS is great. I could just stick it in my project and with minimal effort it makes everything look nice and sleek. I'll definitely continue to use it for small projects like this, but I can't imagine working with it for a big project that I wanted to have a very personalized look.

## Challenges & Lessons Learned

The biggest challenge and lesson learned I faced while making this app are tied together. It is hands down one of the most hilariously simple mistakes I've made while programming. While working on the "click to copy" functionality, I ran into a classically annoying type of bug -- one that only happens *sometimes*. Most of the time I clicked the swatches, the hexadecimal would be copied to my clipboard as expected. But occasionally it just... wouldn't.

I spent an embarassingly long time trying to figure this out. Was there something happening asynchronously with Materialize or the Clipboard API that I wasn't accounting for? Was there something wrong with how I was loading assets? Is there some browser incompatibility I'm not aware of?

I didn't figure it out until I stepped away from the code and took a fresh look the next day. In the end, the problem was stupidly simple. I had attached the click event listener to the `span` containing the text for the hexadecimal code, not the `div` showing the swatch color. Most of the time I clicked directly on the text, which seemed intuitive and worked as intended. But occasionally I would click on just the swatch itself, which doesn't seem unreasonable since they resemble buttons.

The lesson learned is two part:

1. Check the simplest solution first.
2. Don't try to debug when you're tired.

## Future Ideas and Improvements

### Readability
I wrote the code for this project almost half a year ago. Going back and reading through the code now has definitely opened my eyes to some readability issues it has. At the time I wrote it, I was really into doing practice problems on CodeWars. It's a cool platform, but the answers the community values have a heavy bias toward a code-golf mentality. There were some parts of my own code where readability could be greatly improved at negligible cost in performance.

For example:
``` JavaScript
return `#${toHex(Math.round(r))}${toHex(Math.round(g))}${toHex(Math.round(b))}`;
```

Sure it isn't the hardest thing in the world to understand once you see this is from the function `hslToHex(h, s, l)`, but that's still way too much information to unpack at once. What does `toHex()` do? Where are `r`, `g`, and `b` coming from? Why do they need to be rounded? And this is all setting aside the fact that template strings are a bit of a headache to read in general (at least to me). A quick fix would just be storing the value in a variable before returning it. having the line read `return hexString;` instead would already dramatically improve the functin's readability.

### Setting Base Color

When I showed Lightning Palette to a friend, he asked me about a feature that is very obvious in retrospect: "Can I choose a color to start from?" In my imagined scenario for the app, the user had idea what colors to use. Realistically, this won't be the case for most people. Even the least design-savvy user will probably be going into it with at least one color they were interested in using. Being able to set a base color to generate other random colors from would be a big step towards making this project more practical.

### Common Tags

I'd like to expand more on the idea of a "descriptive color scheme generator". A lot of people probably have vague ideas of how they want a color scheme to feel, even if they don't know the specific colors they would have to use to achieve it. Adding a feature of generating schemes based on common descriptive tags (e.g. earthy, neon, joyful, airy) could be great for these people. Implementing this would require a good amount of subjective judgement though, since those descriptions aren't as clearly defined as something like "split complementary".

### More Expressive Preview

I think Lightning Palette could use some work in giving the user an accurate idea of what the color shceme will feel like in actual use. Right now all the swatches (Primary, Secondary, and Accent) are the same size. In reality, the primary color would occupy much more space in the design than the other colors. Simply changing the swatch sizes to reflect their prominence in a design could help with this. Another fun idea would be styling Lightning Palette itself dynamically based on the generated color scheme.