# patrickuhlmann.github.io

Visit: https://patrickuhlmann.github.io/

## Build the page locally:

Setup ruby 2.7:
```
curl -L get.rvm.io > rvm-install
bash < ./rvm-install
rvm install 2.7.0
rvm use 2.7.0
```

Build a page:
```
bundle install
bundle exec jekyll serve
```

Note Jerkyll does not work with Ruby 3.0 as of 28.09.2021

It will then be available under http://localhost:4000
