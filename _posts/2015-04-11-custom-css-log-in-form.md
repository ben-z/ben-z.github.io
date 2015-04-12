---
layout:     post
title:			"Custom CSS Log In Form"
date:				2015-04-11 23:10:39
summary:    I happened to come across this task on codersclan the other day and decided to see how competent I am to solving this problem.
categories: css form login
---

I happened to come across this [task](https://www.codersclan.net/ticket/1090) on codersclan the other day and decided to see how competent I am to solving this problem.

The goal is relatively simple, create this in HTML and CSS.

![Goal]({{site.url}}/assets/custom-css-login-goal.jpg)

I started by tracing this profile icon in Sketch, which came out to be simple, but quite nice:

![An amazing profile icon]({{site.url}}/assets/custom-css-login-profile-icon.png)

The key icon is a bit more complicated, so I decided to make an "abridged" version of it:

![An amazing key icon]({{site.url}}/assets/custom-css-login-key-icon.png)

Everything after this is also pretty straight forward. CSS is a lot easier to write thanks to the "Copy CSS Attributes" function of Sketch's. The HTML is even simpler:

```html
<form class="login">
    <i class="user-icon"></i><input type="text" placeholder="Username" />
    <br />
    <i class="key-icon"></i><input type="password" placeholder="Password" />
</form>
```

It looked like this at the end:

![Result]({{site.url}}/assets/custom-css-login-result.png)

The whole project took around an hour and half to complete, which, with the reward of a chance to earn $25, might not seem worthwhile. But I like to view it as an opportunity to shape up my skills, for possible future projects.

The result came in my favour two days after I submitted my response, the person chose mine as the best solution. :D

The code can be found [here](https://gist.github.com/ben-z/02ac210d73b6e90ba6a1) has a gist, and a demo is made on [CodePen](http://codepen.io/anon/pen/azrmoB). Feel free to share your feedbacks(or critisisms) in the comment section of the [gist](https://gist.github.com/ben-z/02ac210d73b6e90ba6a1), I would love to hear what you think about this project!
