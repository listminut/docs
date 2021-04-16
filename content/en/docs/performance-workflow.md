---
title: "Performance Workflow"
linkTitle: "Performance Workflow"
weight: 100
description: >-
     This section outlines how to implement tested performance improvements.
---

## Performance Workflow

This page explains the process used for performance upgrades. The four main steps to this workflow are:

1. [Run MiniProfiler](#miniprofiler) on the page, look at the times for code execution and the number of queries
2. [Extract non-performant code into it’s own method and test](#extract-and-test) it as is
3. [Create new version of the method](#improve-performance) with the faster code
4. [Share learnings](#share-learnings) you have made in the Best Practices page

### MiniProfiler

To identify any slow and non performant code using a Profiling tool is useful. Currently MiniProfiler is included to the development server and can easily be used on any slow pages.  
You will be able to open the MiniProfiler tool on the top left of the page in question.
  
![Button](/img/Performance%20Workflow/MiniProfiler%20Button.png)  

Once you have opened the MiniProfiler look for code that takes a long time to run and/or excessive numbers of SQL requests.
  
![Breakdown](/img/Performance%20Workflow/MiniProfiler%20Breakdown.png)
  
By clicking on the SQL query breakdown you can further understand which queries take long times to execute or that are repeated multiple times.
  
![Slow Request](/img/Performance%20Workflow/MiniProfiler%20slow%20request.png)
  
![Repeated Request](/img/Performance%20Workflow/MiniProfiler%20repeated%20request.png)
  
You will see that the queries have a the traceback which will lead you to the exact section of code causing the issue.  
Here the issue is due to the code in `app/controllers/listworkers_controller.rb` :
```
@p_cat_choosen = Array.new
@tab_cat.each do |category_id|
  unless category_id.blank?
    s_category = SCategory.find(category_id)
    @p_cat_choosen << s_category.p_category unless @p_cat_choosen.include?(s_category.p_category)
  end
end
```

### Extract and Test

Once you have identified the faulty code you should extract it into its own method. This methodology is outlined by Martin Flower in his book _Refactoring_ (e.g. https://lm-docs.herokuapp.com/docs/guidelines/codeimprovements/extract-method/ ).  
In the previous example the extracted code was placed in `app/models/p_category.rb` :  
```
def self.get_p_categories_from_s_category_ids_deprecated(s_category_ids)
  chosen_p_categories = Array.new
  s_category_ids.each do |category_id|
    unless category_id.blank?
      s_category = SCategory.find(category_id)
      chosen_p_categories << s_category.p_category unless chosen_p_categories.include?(s_category.p_category)
    end
  end
  chosen_p_categories
end
```
You should then thouroughly test the method in such a way as to ensure the modification will behave in the same way.


### Improve Performance

At this point you can now change the method name to include a _"deprecated"_ suffix.  
You then re-write the method in a more performant way. Make sure the new code still passes on all the tests written previously.  

```
def self.get_p_categories_from_s_category_ids_deprecated(s_category_ids)
  chosen_p_categories = Array.new
  s_category_ids.each do |category_id|
    unless category_id.blank?
      s_category = SCategory.find(category_id)
      chosen_p_categories << s_category.p_category unless chosen_p_categories.include?(s_category.p_category)
    end
  end
  chosen_p_categories
end

def self.get_p_categories_from_s_category_ids(s_category_ids)
  PCategory.joins(:s_categories).where('s_categories.id IN (?)', s_category_ids).distinct.to_a
end
```

You now should write a comparative performance test. This can be placed in the `spec/performance` folder.  
This should follow the following format:
```
it "should run quicker than the old implementation" do
  expect { subject.tested_method }.to(perform_faster_than { subject.tested_method_deprecated }.at_least(2).times)
end
```
Once the new method has passed all the tests and performs to the desired standard, you should commit you work.

### Share Learnings

Finally if you have made any changes or solved a problem that might reoccur in the future, don't hesitate to write an entry to the best practices page of our documentation: https://lm-docs.herokuapp.com/docs/guidelines/best-practices/
