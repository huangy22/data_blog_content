title: A Journey From Physics To Data Science (Part I)
tags: 
  - getting started
categories: data science
date: 2018-03-18
---

Data science is no doubt a hot field and attracts professionals from all different domains who are able to organize the astonishing amount of data as well as get insight from data. Though transitioning into data science sounds very tempting, it is not an easy decision to make when you’ve already invested years of effort into another career path. 

I recently made a transition from a Ph.D. student in computational condensed matter physics to a machine learning engineer at a data-driven personal styling company [Dia&Co](https://www.dia.com/welcome) and I want to share with you my journey and experience. If you are interested in making the transition to data science or still hesitating, hopefully my story can give you a better idea about the process and help you to get more confidence.

<!-- more -->

## Research Background

I was a Ph.D. student in computational condensed matter physics at University of Massachusetts, Amherst. The timeline for my transition was as follows:

- 2016-10: Start to learn data science
- 2017-01 to 06: Take data science courses from CS department, prepare for Insight interview
- 2017-06 to 08: Insight Data Science at NYC
- 2017-08 to 10: Interviews for jobs
- 2017-10: Accept offer from Dia&Co
- 2017-10 to 12: Waiting for OPT, taking stats courses from Rutgers University
- 2018-01: Start working
- 2018-03: Thesis Defense

From the time I decide to make the transition to the point I finally landed with a full-time data scientist job, it took me almost exactly one year. 

One thing that made my transition easier is that my day-to-day research involves quite a lot of coding. In the earlier years of my research, I used FORTRAN to write Monte Carlo algorithms since all the legacy codes from our group are in FORTRAN. Around 2015, my colleague and I made a bold and correct decision to learn about Python and C++, and rewrite a quite complicated code (I wrote the first version in FORTRAN but finally we realized that the scale of this code is no longer managable) with proper object-oriented design. Although it took really long to develop the code (ended up being more than 10,000 lines), I definitely learned very valuable lessons about coding through the process. If you are interested about this project, here is the github repo: [Feynman Simulator](https://github.com/kunyuan/Feynman_Simulator).

Although research has a lot of fun, I notice myself sometimes enjoy more on the implementation part as it really makes me feel fulfilled when I finally get something running. This is probably not the best trait for a theoretical physicist as it's more important to have a deep understanding of the theory and come up with innovative ideas. Although I have a solid understanding of the theory in my field, I just don't think being a theoretical physicist will bring out the best in me. That's why the idea of becoming a data scientist really hits it off with me.

## Reading Blogs
However, it still takes a lot of courage to finally decide to leave the field you have been working in for 10 years and start as a newbie in a totally different area. Fortunately there are a lot of people out there to help us out. Recently there are more and more successful examples who have made the transition from academia to data science in industry and I really gained a lot of confidence from reading their blog posts, Quora answers, etc. 

Back in the end of 2016, when I was reading a great series of blog posts [Yet Another PhD to Data Science Post](http://blog.ethanrosenthal.com/2015/09/23/start-to-insight/), I would never imagine that I will be working with the author, [Ethan Rosenthal](http://blog.ethanrosenthal.com/), at the same company one year later. How small the world is! His blog also greatly inspires me to start my own as I think it's a fantastic way to learn by teaching as suggested by [Richard Feynman](https://collegeinfogeek.com/feynman-technique/) and also share the ideas with other people. 

## Taking Courses
Between October 2016 and January 2017, I was mainly preparing my data science knowledge by taking online courses. 

I took one MOOC on Coursera, which is Andrew Ng's [Machine Learning](https://www.coursera.org/learn/machine-learning). If you only have the time to take one course in machine learning, I would say this is it. Andrew is a fantastic teacher and really explains all the concepts concretely and clearly. The course covers a large variety of machine learning algorithms (both supervised and unsupervised) in a very accessible level. It's a great way to get familiar with all the different flavors in the current machine learning field.

At the same time, I also followed Andrew's [CS229 Machine Learning](https://www.youtube.com/watch?v=UzxYlbK2c7E) course at Stanford. This is a much math-heavier course and really deep dives into a lot of advanced topics such as learning theory, reinforcement learning and adaptive control. It's also really interesting to learn about the cool applications of machine learning in something like robotic control, speech recognition, and text processing. (But keep in mind that these online videos have been taken 10 years ago, these are definitely not the state-of-art techniques any more.)

It's also quite important to learn Computer Science fundamentals such as algorithms and data structures. The most important reason is that, in the interview process for data scientists, we'll often encounter coding interviews. It'll take quite a long time to practice coding exercises, so the earlier you start working on that the better. I followed the [MIT 6.006 Introduction to Algorithms](https://www.youtube.com/playlist?list=PLUl4u3cNGP61Oq3tWYp6V_F-5jb5L2iHb) online course and found it really interesting. I really liked the Dynamic Programming part that Erik taught. I would also highly recommend practicing coding questions on Online Judge platforms such as [Leetcode](https://leetcode.com/problemset/all/) and [HackerRank](https://www.hackerrank.com/domains/algorithms).

Starting from January 2017, I was back to school after the winter break and started taking the data science courses from the CS department of UMass. The two courses I took were Machine Learning and Data Visualization.

### Machine Learning CS589 at UMass
The reason I decided to take another machine learning course is that although I learnt a lot of concepts and theory from Andrew, I still want to work on a end-to-end project that I can practice data collecting, cleaning, preprocessing and modeling techniques. In this course, I worked on several homework projects involving classification, regression and clustering tasks. The homeworks are designed as Kaggle competitions which requires us to try out different models and compete with our peer classmates, which also got me familiar with [Kaggle](https://www.kaggle.com/) platform. And for the final project of the course, we are free to use any open dataset and work on anything we feel interested in. I worked on building a academic paper citation network which I'll talk in the next section.

### Data Visualization CS590V at UMass
This course is quite an adventure for me. The biggest accomplishment that I'm quite proud of is to learn how to use [D3.js](https://d3js.org/) and HTML/CSS to build interactive visualizations and websites. During the course, I made several data-drive interactive visualizations and hosted them on the department [website](http://www-edlab.cs.umass.edu/~yuanh). You can see how my skills evolved from the first assignment where the only interactive element is a list of buttons, to the final project which involves fancy [force-directed network graphs](http://www-edlab.cs.umass.edu/~yuanh/590v/project/pages/articles.html) and interactions between different plots!


## Project: Academic Paper Citation Network

I combined the machine learning and data visualization skills I have acquired and worked on a project that involves data collection, preprocessing, clustering algorithm development, and interactive visualizations. I'm going to write a series of posts about this project in the near future. In a word, I collected the data for all the academic articles published on Physics Review journals and stored the json files in [MongoDB](https://www.mongodb.com/). Then I implemented two types of clustering algorithms to group the physics articles based on their citation network. At the end, I built the visualizations using [D3.js](https://d3js.org/) to show the papers from different fields and subfields in physics, identify their citation relationship, and also find out the most influential physicists in different fields.

## Insight Data Science

As the end of the sememster was around the corner, I applied for [Insight Data Science Program](https://www.insightdatascience.com/) at the end of March. I'll talk about my experience of applying and participating the Insight program, interviewing with companies in the next post!

