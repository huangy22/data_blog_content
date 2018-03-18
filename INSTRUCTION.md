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


## Using nbconvert to include ipynb in the post
use plugin: [hexo-jupyter-notebook](https://github.com/qiliux/hexo-jupyter-notebook)
1. Enable post asset by adding this line in your `_config.yml`: `post_asset_folder: true`
2. put your jupyter file in post asset directory. You can use `ln -s` , `cp source direction` and etc. 
3. Make sure load jquery 2 (jquery 3 will error. I will fix at soon.) before you use `asset_jupyter`

``` html
<script text='text/javascript' src='/path/to/jquery.js'></script>
{% asset_jupyter python_path jupyter_file_name %}


eg : (i use next theme , so the jquery is in lib/jquery/index.js)
<script text='text/javascript' src='/lib/jquery/index.js'></script>
{% asset_jupyter /data1/anaconda3/bin/python crash-mxnet-ndarray.ipynb %}

```
