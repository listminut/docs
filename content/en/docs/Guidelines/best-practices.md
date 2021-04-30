---
title: "Best Practices"
linkTitle: "Best Practices"
weight: 100
date: 2021-04-30
description: >-
     This page includes best practice patterns or anti-patterns to help you avoid into a pitfall.
---

{{% pageinfo %}}
These are best practices to guide you in the right direction, but there will always be some exceptions where these rules don't apply.
{{% /pageinfo %}}

## Only update a model when it has changed

Updating data in a model requires a call to the DB. However, if the data being
updated, is already in the right state, this call would be a waste. You can
validate whether you need to do an update using the `changed?` method.

❌ Instead of doing this:

```
one = 1
two = 2
@my_model.update(one: one, two: two)
```

✅ Do this:

```
@my_model.one = 1
@my_model.two = 2
@my_model.save if @my_model.changed?
```

## Use early returns

To avoid nesting code inside if/else branches, try to check for errors first and return as soon as possible

❌ Don't do this:
```
if everything_worked?
  # … hundreds of lines of code
else
  return error
end
```

✅ Do this:
```
return error if something_failed?

# … hundreds of lines of code
```

The same applies when we call redirect_to in a controller, just make sure to also return after calling redirect_to
```
redirect_to homepage and return if error.present?
```

## Avoid the use of unless (still use it when necessary)

Try to use if as often as possible instead of unless.
Only use unless in single line conditions.

Examples:

❌ This is not ok
```
return error unless params.blank?
```
✅ Because it can easily be replaced by
```
return error if params.present?
```
✅ This is ok, because we don’t have a `current_user.not_admin?` method
```
redirect_to homepage unless current_user.admin?
```
❌ Don’t do this
```
unless current_user.admin?
  # … hundreds of lines of code
end
```
✅ Just use `if !current_user.admin?`
```
if !current_user.admin?
  # … hundreds of lines of code
end
```

{{% alert title="Note" color="info" %}}
`present?` and `blank?` are exact opposites, so this **always** applies : `something.present? == !something.blank?`
{{% /alert %}}
