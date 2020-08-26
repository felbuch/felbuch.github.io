
Making a website is super easy! As long as you have the right tools. The tool I used to build this site is called **Jekyll**

### Why Jekyll?

I'll be brutally honest here: Simply because **it's easy!**  
It doesn't require installing packages, configuring virtual environments,... it's pretty much ready to use!  
All you need to do is follow the yellow brick road:

### Step 1: Have a GitHub account

You need a GitHub account.

![GitHub](/assets/img/github-logo.jpg)

GitHub allows you to host a [static website](https://en.wikipedia.org/wiki/Static_web_page).
This means you can have your own website in GitHub.  
But it's static, so it cannot have any applications the user interacts with.
Still, that's a very small price to pay for such an easy way to make a website.

If you don't have a GitHub account, you can create one [here] (https://github.com/).  
I won't go on the details on how to set up a GitHub account here, but if you need help doing that, you can probably find it in this [tutorial](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

### Step 2: Fork the Beautiful Jekyll repository

One you have your GitHub account set up, go the the [Beautifull Jekyll repository](https://github.com/daattali/beautiful-jekyll). You should see something like the picture below. Notice the **Fork** button, on the upper right corner. You need to click it!

![The Beautiful Jekyll Repository](./felbuch.github.io/assets/img/Beautiful_Jekyll_Fork.PNG)


When you click the Fork button, you're actually creating a copy of Beautiful Jekyll for youself. This will be the your website. Sure, you'll make changes to it. But the essential structure is already there.

This is a bit like using a template for a school project. You won't simply copy someone elses project. But it helps to have a file that's already properly formatted, so you can just change the content.

Let's begin by changing the most important thing of all:

### Step 3: Change the repository name. This will be your website address!

Once you've forked the Beautifull Jekyll repository, it should appear as a repository in your own GitHub.
You'll see a Setting button. Click on it.

![Click on the settings button to give your website a name](../assets/img/Beautiful_Jekyll_Settings.JPG)

This will take you to a screen where you will be able to change the name of this repository:

![Change your repository's name](../assets/img/Beautiful_Jekyll_Rename.JPG)  

This will also be your website's URL, so take a minute to think about how you would like to name it...  
Since your name is also a URL (and since it will be hosted on GitHub), your name must have the form **whatever_you_like.github.io**. Darth Vader, for example, could name his website *Death_Star.github.io*, or *Proud_Jedi_Dad.github.io*.


### Step 4: Customizing your website

You'll notice that the Beautiful Jekyll package comes with many files. One of them is called **\_config.yml**.  

This file configures your website's structure.
It has lots of stuff in it, but it's easy to understand if you just give it a try.

So open it. 
Look where it has a LinkedIn address and change it for your own.  
Do the same with the Facebook address and e-mail.  
If you don't want to show any information, just comment the entire line.  

Just open the file and go through it, changing whatever needs to be changed.      
You'll be fine, I promise!  

### You have a website!

Once you've commited the changes you made to the *_config.yml* file, you can pat yourself on the back and celebrate: You now have a website!  

![Celebrate!](../assets/img/celebrate.jpg)

"But wait!", you'll say.  
"Where are my posts? My pictures? All the content I want there to be in my website?"  

### Step 5: Your first blog post

Among all the files and folders you forked from Beautiful Jekyll, you'll find a folder names **_posts**.  

A post is simply a markdown file.  
It's a markdown file that follows 3 rules:
1. It starts with a date
2. It ends with `.md`
3. Spaces are represented by a short dasth: `-`

Want an example to make things clearer?
The file you're reading right now is called **2020-08-25-How-to-make-your-own-website.md**.

In summary:

> Write a file in markdown, name it correctly, drop it in the **\_posts** folder, and there you have it: your own blog post!  

Need a refresher in Markdown? There's awesome material out there, but why not [start with this one](https://guides.github.com/features/mastering-markdown/#intro)?  

Don't miss the section on adding pictures to a markdown document.  
Your blog will definitely need some pictures!
But you'll need to put these images into a folder.  
I put mine on the **assets** folder, inside the **img** subfolder.  

### Step 6: Own and make it your own

Let's give your website those final touches, to make it suit your style!  

Beautiful Jekyll comes with a number of default files.  
The *_posts* folder has two default posts, for example.
You won't want these files in your website, so just go ahead and delete them.  

Now, open the **\_config.yml** again and scroll down with me.  
Look for a section that looks somewhat like this one:

> \# --- Required options --- # \
>\# Name of website \
> title: Data Journey \
> \# Your name to show in the footer \
> author: Felipe Buchbinder

Here you can change your website's name. This name will appear on the navigation bar, on top of your website.  
You can also identify yourself as the author of the website.  

Next, you'll see a section that looks like this:  

> \# --- List of links in the navigation bar --- # \
>navbar-links: \
>  About Me: "aboutme" \
>  Resources: 
>    - Beautiful Jekyll: "https://beautifuljekyll.com" 
>    - Learn markdown: "https://www.markdowntutorial.com/" 
>    - DataViz catalogue: "https://datavizcatalogue.com/" \

These are the items that appear in your navigation bar.  
You can customize these items, by adding your CV or your LinkedIn, for example.
Don't forget to change the `aboutme.md` file for something that's a true description of yourself.
Also, make sure to put some interesting resources for your website's visitors.  
Make sure you understand the synthax here: when you hover over "Resources", a drop-down menu appear with links to each of the resources listed above. Here is a nice place to refer your visitors to other cool stuff around the web.

Next, you'll have the opportunity to change your avatar.  
The default avatar is GitHub's cat logo.  
But you can do better than that!  
Find a nice, small picture and use it as your logo. (Note that I saved it o the *assets/img* folder) 
Most pictures in the web are rectangles, but you can make it look round by setting `round-avatar` to true.

> \# --- Logo --- #
>\# Image to show in the navigation bar - works best with a square image \
>\# Remove this parameter if you don't want an image in the navbar \
>avatar: "/assets/img/top-of-page.jpg" \
>\# By default, the image is cut into a circle. You can disable this behaviour by setting 'round-avatar: false' \
>round-avatar: true

Next, you'll find a long list of commented social media addresses.  
Here's where you can add links to Twitter, Instagram, Facebook, Linkedin,... you name it!

Continue scrolling down, and you'll soon find a session that looks like this:

> \# Personalize the colours in your website. Colour values can be any valid CSS colour \
>navbar-col: "#000080" \
>navbar-text-col: "#FFFFFF" \
>navbar-border-col: "#DDDDDD" \
>page-col: "#FFFFFF" \
>text-col: "#404040" \
>link-col: "#FFFFFF" \
>hover-col: "#D3D3D3" \
>footer-col: "#EAEAEA" \
>footer-text-col: "#777777" \
>footer-link-col: "#404040"

Here's where you can change the colors of your website and give it a brand new look!  
Each color is designated by a name composed of letters and numbers.  
"#FFFFFF", for example, means "white", and "#000080" means "navy blue".  
These codes are called **CSS code**.  
[This website](https://www.rapidtables.com/web/css/css-color.html#blue) has the CSS code of pretty much any color you can think of.  
And if you have a CSS code and need to find out what color it is, this [amazing tool](https://www.colorhexa.com/) will surely help.

### Step 7: Running your website

To make changes to your website, simply add, commit, and push everything to the GitHub reposotory.  
Your website will be generated automatically.  
No need to press any buttons, or run any commands.  
If it's in your GitHub, it's running.  
But there's a catch: even after you push any changes to GitHub, it can take a while untill your actually see any changes on your webpage.  
So be patient. And be proud: You just made your very own website!  

![Celebrate!](../assets/img/celebrate.jpg)

(This time, it's for real!)

### Additional resources

If you want to learn more about [Beautiful Jekyll](https://github.com/daattali/beautiful-jekyll), a great source is their repository itself. Their `README.md`file is very helpful (in fact, that's what I followed to make this blog post). So make sure to check it out!

Don't like Beautiful Jekyll? There are many other great tools out there that you can use to make your own website. Check [pelican](https://blog.getpelican.com/) or [hugo](https://gohugo.io/) for instance. If you enhoy them and decide to write a blog post teaching how to use them, please let me know! :)


```python

```
