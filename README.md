# patrickuhlmann.github.io

Visit: https://uhlme.ch/

## Preview the page locally:

Preview the page:
```
docker run -it --rm -v "$PWD":/usr/src/app -w /usr/src/app -p 4000:4000 ruby:2.7 sh -c "bundle install && bundle exec jekyll serve --host 0.0.0.0"
```

It will then be available under http://localhost:4000

Note: Jerkyll does not work with Ruby 3.0 as of 23.07.2023
