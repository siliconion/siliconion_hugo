---
title: "Hugo deploy to GitHub"
date: 2020-01-02T00:00:00-05:00
draft: false
---

This blog is powered by [Hugo](https://gohugo.io/). It is so easy to use! Hugo blogs can be easily deployed to GitHub by following [this tutorial](https://gohugo.io/hosting-and-deployment/hosting-on-github/). 

The only 2 things I had problem with were:

1.  When first deployed, my GitHub page kept 404 although all html files are update in the my <username>.github.io repo. To solve this, go to "settings" in the repo, scroll down to "GitHub Pages", make sure the correct branch is chosen in the source. Also choose a Jekyll theme even we are not using Jekyll. 

2. I followed the doc and created a submodule, and in no time it's all messed up. Git submodule is a mystery not worth solving. So I cloned the <username>.github.io repo outside of my source repo, and copy the files in public into it. My modified script is [here](https://github.com/siliconion/siliconion_hugo/blob/master/deploy.sh).

I probably should setup CD but meh.


