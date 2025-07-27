---
layout: post
title:  "Overlapping images in emails (including Outlook)"
date:   2025-07-26 21:20:00
categories: html email
---
# The problem
Recently, I had to create an email template that contains multiple images and the designer came up with this design:
![Figma mock](/assets/article_images/2025-07-27-overlapping-images-in-emails/main.png)
There are 2 major problems that need to be solved here:
* make the images round
* overlap the images

# The solution
The following snippet seems to solve the problem in a relative easy way:

{% highlight html %}
<html>
  <body>
    <table align="center">
      <tr>
        <td width="40" style="max-width: 40px">
          <img width="84" style="border-radius: 180px" src="https://i.imgur.com/49K69xc.jpeg" />
        </td>
        <td width="40" style="max-width: 40px">
          <img width="84" style="border-radius: 180px" src="https://i.imgur.com/AKNRGd9.jpeg" />
        </td>
        <td width="40" style="max-width: 40px">
          <img width="84" style="border-radius: 180px" src="https://i.imgur.com/q4Qcpth.jpeg" />
        </td>
      </tr>
    </table>
  </body>
</html>
{% endhighlight %}

Because the email clients and CSS do not get along very well we must use tables in order to style the template:
* we have a table with one row containing 3 td tags, each of them with an img tag
* the images have a width set to 84 pixels and a border-radius set to 180px
* the overlapping is done using the max-width CSS style. The image has a width of 84px, but it’s parent has a maximum width of 40px.

Here is how it looks in an email:
<div style="text-align:center">
  <img src="/assets/article_images/2025-07-27-overlapping-images-in-emails/email1.png" />
</div>

# The Outlook problem
The classic version of Outlook for Windows does not use a modern rendering engine for HTML and the email looks like this:
<div style="text-align:center">
  <img src="/assets/article_images/2025-07-27-overlapping-images-in-emails/email2.png" />
</div>
* the images are not rounded, because border-radius is not supported
* they are not overlapped because the image’s width forces the td to expand and ignore max-width

This is why we need to use a different approach in order to make the template look the same in Outlook:

{% highlight html %}
<td width="40" style="max-width: 40px">
  <!--[if mso]>
    <v:oval xmlns:v="urn:schemas-microsoft-com:vml" xmlns:w="urn:schemas-microsoft-com:office:word" style="height:84px;width:84px;position:relative" fill="t" strokecolor="white" strokeweight="4px">
      <v:fill type="frame" src="https://i.imgur.com/49K69xc.jpeg" />
    </v:oval>
  <![endif]-->
  <!--[if !mso]><!-->
    <img width="84" style="border-radius: 180px" src="https://i.imgur.com/49K69xc.jpeg" />
  <!--[endif]-->
</td>
{% endhighlight %}

Here are the key aspects that make this solution work on Outlook:

* We use VML elements that are supported only by classic Outlook
* We use Outlook conditionals to display those new elements only on Outlook
* The v:oval element solves the border-radius problem
* VML elements support position: relative CSS and makes it so the element itself does influence the width of the td, so the td will have the width fixed to 40px, but the v:oval will be 84×84. This mimics the max-width property
In the end, the email will look like this:
<div style="text-align:center">
  <img src="/assets/article_images/2025-07-27-overlapping-images-in-emails/email3.png" />
</div>
