## Create Your blogpost
Run
```
hexo new draft "BlogPost Name"
```
and edit your blogpost in `./source/_drafts/BlogPost-Name.md`.

Images are saved in `./source/images/`.

Sample
```
---
layout: draft
title: BlogPost Name
tags: 
  - tag1 
  - tag2
categories: coding
description: The description for the blog to show in the home page.
--- 
## Hello there
This is some content.

![I love it when a plan comes together.](/images/Ateam.jpg)
```

When finishing, run
```
hexo publish BlogPost-Name
```

Finally, prepare the entire site for deployment. Run the hexo generate command:
```
hexo generate
```

## Deploy the website to your remote host
Run
```
./hexo_deploy.sh
```

