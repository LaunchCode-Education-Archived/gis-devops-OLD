---
title: "Studio: LaunchCart REST"
---

In this studio, we'll add additional RESTful endpoints to the LaunchCart application. It builds upon the changes we added in the [LaunchCart REST walkthrough](../../walkthroughs/launchcart-rest/).

## Getting Ready

From the same [`launchcart` project/repository](https://gitlab.com/LaunchCodeTraining/launchcart) that you used previously, make sure you are in the `rest-walkthrough` branch that you used earlier today.

If you don't have the walkthrough code, you can fetch changes from the remote and check out the `rest-studio` branch.

```nohighlight
$ git fetch
$ git checkout rest-studio
```

## Add `new` Parameter to `/api/items`

Add a `new` parameter to the `GET /api/items` endpoint. Note that this should work *in conjunction* with the `price` parameter, so that one, both, or neither can be used when querying the resource.

## Add `/api/customers` Resources

Add the following REST resources:
- `GET /api/customers`
- `GET /api/customers/{id}`
- `GET /api/customers/{id}/cart`

Write integration tests before coding each controller method. When testing, be sure to test for the expected status code, content type, and response contents.

## Add `/api/carts` Resources

Add the following REST resources:
- `GET /api/carts`
- `GET /api/carts/{id}`
- `PUT /api/carts/{id}` - allow items to be added to a cart

A cart resource should include information about its owner, as well the items contained in the cart. The `PUT` method should allow for items to be added and removed from the cart.

Write integration tests before coding each controller method.

## Turning In Your Work

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day.

- Commit and push your work to GitLab
- Notify the instructor that you are done, along with the name of the branch that you completed your work in