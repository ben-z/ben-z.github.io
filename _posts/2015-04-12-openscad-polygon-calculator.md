---
layout:     post
title:			"OpenSCAD Polygon Calculator"
date:				2015-04-12 14:37:20
summary:    Bob the Unicorn attends the Unipark Secondary School. Bob likes Physics. One day, professor D. asks Bob to trace out all the parts of the F-18 model plane for 3D printing.
categories: openscad reactjs
---

Bob the Unicorn attends the Unipark Secondary School. Bob likes Physics. One day, professor D. asks Bob to trace out all the parts of the [F-18 model plane](http://www.rcpowers.com/community/threads/f-18-v4-%E2%80%9Cmultirole-parkjet%E2%80%9D-v4-pro-pack.16825/) for 3D printing. Bob is very excited because he has just learned about this really cool 3D designing program called [OpenSCAD](http://www.openscad.org/). With the measurement tool in Adobe Reader, Bob immediately began working on obtaining the dimensions of the PDF plan.

No success comes without obstacles. Bob is immediately confronted with the problem that while Adobe Reader provides accurate relative measurements (&Delta;x and &Delta;y) according to the previous point, OpenSCAD's `polygon()` only accepts absolute coordinates.

After some brainstorming, Bob comes up with a brilliant idea, why not add the points together and let OpenSCAD calculate the sum difference instead of manually using a calculator:

```js
polygon([
    [0.00, 0.00]
    ,[0.00+19.69, 0.00+0.00]
    ,[0.00+19.69+0.00, 0.00+0.00+18.28]
    ,[0.00+19.69+0.00+6.00, 0.00+0.00+18.28+0.00]
    ,[0.00+19.69+0.00+6.00+0.00, 0.00+0.00+18.28+0.00-18.28]
    ,[0.00+19.69+0.00+6.00+0.00+25.88, 0.00+0.00+18.28+0.00-18.28+0.00]
    ,[0.00+19.69+0.00+6.00+0.00+25.88+10.28, 0.00+0.00+18.28+0.00-18.28+0.00+57.15]
    ,[0.00+19.69+0.00+6.00+0.00+25.88+10.28-63.36, 0.00+0.00+18.28+0.00-18.28+0.00+57.15+0.00]
]);
```

As you can see, although the code is a bit more maintainable, this solution quickly becomes overwhelming because it gets very messy after a few coordinates. Bob has a 15 inch laptop and he has to reserve half of the screen for the PDF file.

What is to be done next, then?

Bob envisions a polygon calculator, that allows him to quickly convert the &Delta;x and &Delta;y into and from OpenSCAD code. This is what he made after an hour and half of intensive coding:

![The Amazing Polygon Calculator]({{site.url}}/assets/Polygon-Calculator-V1.png)

Bob can now measure the PDF and generate OpenSCAD polygon code with ease.

Bob is happy with what he has made, for about 2 hours. Then he suddenly realizes how much improvment can be done to this program:

1. The raw input/output shouldn't be necessary at all as the OpenSCAD code only requires absolute coordinates, and the difference can be easily found using those (Bob has been using `.split('+')` to get his coordinates from the raw input.)

2. It would be nice if there is a preview panel where the user can have a peek of the shape directly inside the app.

3. Lots of bugs are present because the program basically solely depends on substrings and type casts to get its coordinates.

Seeking improvement, Bob immediately starts to plan out a new version. He begins to draw a rough draft of the design in Sketch:

![Sketch]({{site.url}}/assets/Polygon-Calculator-Sketch.png)

Then he spends two weeks working on it. This is the final product:

![Result]({{site.url}}/assets/Polygon-Calculator-Result.png)

Bob is quite happy with what he has done, and as usual, still sees possible improvements, such as implementing variables into calculation. But that can be done at a later time.

*PS: Bob constantly seeks feedback. He will be very happy if someone checks out the [OpenSCAD Polygon Calculator](https://ben-z.github.io/OpenSCAD-Polygon-Calculator) and leaves valuable suggesstions/criticisms.*
