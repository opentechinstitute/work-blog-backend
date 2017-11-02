If you're reading this, its because you're an OTI staff member who wants to update the [OTI work blog](https://opentechinstitute.github.io/). To add a blog post, you need the following tools installed on your computer:
1. Git
2. [Hugo](http://gohugo.io/)

Please follow these instructions for adding a post:
1. Fork this repository to your own account and `git clone` to a convenient directory on your own computer.
2. In a terminal, change directory to your new folder created by the `git clone`.
3. Execute `hugo new post/<your-post-name-here>.md` (or `.org` if that's your preferred text formatting language).
4. Edit `content/post/<your-post-name-here>.md` in your favorite editor. In the auto generated header add a line `author: "<your name here>"` and make sure the `title:` reads how you'd like.
5. Type up your blog post.
    * All of Markdown is available to you.
    * Images go in `static/images/` and can be referenced under the path `/images/` within your file.
    * You can see a preview of your post by running `hugo server -D` in the directory and visiting the URL it gives you.
6. When you're done, change `draft: true` to `false` and set the `date:` to whenever you want the post to appear.
7. `git push` your changes up to your fork and issue a pull request against the main repo. Assign it to whoever you think should read it.

Once the PR is accepted, Travis will automatically run and push the new post to the website.