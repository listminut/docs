---
title: "Extract Method"
linkTitle: "Extract Method"
weight: 1900
date: 2021-03-08
description: >-
  By pulling out small, clearly named methods, our code will not require readers
  to sift through implementation details or rely on comments in order to
  understand what is being done
---

Investigate your larger methods, especially ones longer than 10 lines. Do you
see any groups of functionality that could be extracted out into smaller, more
clearly named, methods?

For example:

```
# Sanitize Input 
escaped_input = HTML::Escaper.new(input).escape 
stripped_input = SpaceRemover.new(escaped_input).run 
censored_input = ForbiddenWordCensor.new(stripped_input).run
```

Here we are doing things that sanitize input, so these could be pulled out into
a new method called sanitize_input. Then we could also remove the associated
comment, since the name clearly describes what is happening.

```
def sanitize_input(input) 
  escaped_input = HTML::Escaper.new(input).escape 
  stripped_input = SpaceRemover.new(escaped_input).run 
  ForbiddenWordCensor.new(stripped_input).run 
end
```

By pulling out small, clearly named methods, our code will not require readers
to sift through implementation details or rely on comments in order to
understand what is being done. It also can help us remove duplicated code,
because we can now use this new smaller method again elsewhere.
