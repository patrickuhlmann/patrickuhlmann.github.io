---
layout: post
title:  "How to use mock routes in Angular tests"
date:   2021-03-25 21:00:33 +0100
categories: angular testing router
---
The Angular Route class takes 8 arguments in the constructor. For a simple test, I didn't want to construct it by hand. Thus I used the RouterTestingModule. Here you can see how it is done:

{% highlight typescript %}
describe('ArticleService', () => {
  let router: Router;
 
  beforeEach(() => {
    TestBed.configureTestingModule({ imports: [RouterTestingModule.withRoutes([])] });
    router = TestBed.inject<Router>(Router);
  });
 
  describe('addArticle', () => {
    it('should navigate to overview page after adding an article', () => {
      articleService = new ArticleService(router);
      const navigateSpy = spyOn(router, 'navigate');
      articleService.addArticle('title');
      expect(navigateSpy).toHaveBeenCalledWith(['overview']);
    });
  });
});
{% endhighlight %}

It verifies that the code under test called the navigate method with the page overview.
