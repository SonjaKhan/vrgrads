VRGrads
====================

Website for the University of Washington Computer Science & Engineering Virtual Reality/Augmented Reality capstone course.



*Setup* 

if you don't already have jekyll, run

```
gem install jekyll
```

if you don't already have bundler, run
```
gem install bundler
```

then run
```
bundle exec jekyll serve --baseurl '' --watch 
```
and navigate to localhost:4000


**Things to be aware of**

Links should be prepended with site.base_url. gh-pages serves it from 
/vrgrads. Running it locally serves it from /, which is why we 
override the baseurl when running it on localhost.
