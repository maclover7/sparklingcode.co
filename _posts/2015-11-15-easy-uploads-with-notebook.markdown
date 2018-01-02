---
layout: post
title:  "Easy Uploads with Notebook"
date:   2015-11-15 12:00:00
categories: attachments rails
---

Welcome back to my blog! This post will be centering around how to use a
Ruby file upload library that I built, called Notebook.

# The Origins

The origins of Notebook trace back to the Spring of 2015, to when I was
helping to keep the issue tracker under control for
[Paperclip](https://github.com/thoughtbot/paperclip). It may
not seem like much, but I commented on almost all open issues, "Hey, is
this still a problem?". Due to the help from Tute Costa and myself, we
were able to get the Paperclip issue tracker down from about 300 issues,
to just under 100 today.

This experience opened me up to the difficulties of maintaining an OSS
codebase, and also the complexity that goes into a project like
[Paperclip](https://github.com/thoughtbot/paperclip).

# Ship it

I reconnected with Tute a few months later, and we began work on
Notebook. The two of us were able to built almost the entire library in
one day -- I'd say that's pretty amazing! The feature set may have been
much smaller than what something like Paperclip could offer, but it was
definitely a strong MVP (minimum viable product).

Today v0.1.0 of Notebook is being released, with support for storing
files on both the filesystem and on Amazon S3. If you want to build
other storage adapters, go for it!

# How Do I Use It?

After all of that hype, it's time for some documentation about how you
even use Notebook. Notebook works with both Ruby on Rails, and also just
plain old Ruby.

### Notebook and Rails

To use Rails with Notebook, it's very simple. On the model you wish to
have an attachment on, say Post for example, just insert the following code:

```ruby
notebook_attachment :picture
```

Now, you have get the URL for the picture and show it via an
`image_tag`, for example: `<%= image_tag @post.picture.url %>`.
In your form where you want to add a picture, just simply place the
following code in:

```ruby
<%= f.file_field :picture %>
```

Finally, create a file called `config/initializers/notebook.rb`, and
specify the location where you would like to store your file.

That's all there is to it!

Feel free to submit pull requests adding new storage adapters or
features at the official Github repo -
[https://github.com/moss-rb/notebook](https://github.com/moss-rb/notebook).

Special thanks goes out to both [Jon Yurek](https://github.com/jyurek) for his years of
maintenance on Paperclip, and to [Tute Costa](https://github.com/tute) for helping me to write
Notebook.
