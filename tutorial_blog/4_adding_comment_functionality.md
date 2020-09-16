[Back to Tutorial Index](./README.md)

# Tutorial: Personal Blog App

## 4. Adding Comment Functionality
What is a blog without the ability to hear feedback on it! Comment functionality is so essential to any blog regardless of its nature and target audience, and next you will learn about adding it to your app.

### Comment module
As you already can tell, you need to create a module to provide the required functionality. Since you already know the basics you should be able to figure out most of the following:
```python
from nawah.base_module import BaseModule
from nawah.classes import ATTR, PERM

class BlogComment(BaseModule):
    collection = 'blogs_comments'
    attrs = {
        'blog': ATTR.ID(),
        'name': ATTR.STR(),
        'email': ATTR.EMAIL(),
        'content': ATTR.STR(),
        'status': ATTR.LITERAL(literal=['pending', 'approved', 'deleted']),
        'status_note': ATTR.STR(),
        'create_time': ATTR.DATETIME(),
    }
    methods = {
        'read': {
            'permissions': [PERM(privilege='read'), PERM(privilege='*', query_mod={'status':'approved'})],
            'query_args':[
                {'blog': ATTR.ID()},
                {'status': ATTR.LITERAL(literal=['pending', 'deleted'])}
            ]
        },
        'create': {
            'permissions': [PERM(privilege='create', doc_mod={'status':'pending', 'status_note':''})],
        },
        'update': {
            'permissions': [PERM(privilege='update')],
        },
        'delete': {
            'permissions': [PERM(privilege='delete')],
        },
    }
```

Though the module looks mostly familiar in structure, some elements are not, which are:

#### `attrs[...]`
In addition to what you learnt earlier on `create_time Special Attr`, you can see different `Attrs Types` this time. You can learn more on each from reference for [`ATTR Controller`](/reference/controllers/ATTR.md). The `BlogComment` module is having all sort of `attrs` that allow your users to interact with your blog posts such as: `name`, `email`, `content`. You also can see `blog` attr of type `ID` which serves as the link between the comment and the blog--The blog post unique `_id` attr shall be supplied here in order to link the pieces together. Also, `attrs` include `status`, and `status_note` which control the comment status. This is much needed in order to keep new comments on hold (by having `status` set at `pending`), while you, as blog admin, shall update `status` to `approved` or `deleted` based on what you think is better for the audience, while you can keep a reminder to yourself on your decision by updating `status_note`.

#### `methods[...]`
Unlike `Blog` module which you created earlier, `BlogComment` module is having more structure in `methods`. This is due to the nature of comments which requires different kind of management. The combination of two `Permissions Sets` for method `read`, as well as the existence of `query_args`, and the `doc_mod` argument for method `create` `Permission Set`, are all working together for that purpose. Next you will learn the purpose of each.

##### `read`
Unlike any other method in `Blog` or `BlogComment` modules, method `read` has two `Permissions Sets` defined by two `PERM` objects, although first `Permission Set` is more familiar. Second `Permission Set` has argument `query_mod`--Query Modifier. Argument `query_mod` of `PERM` controller allows developers to modify `Query` passed to the method if the `Permission Set` is the one the call was allowed based on. For instance, having multiple `Permissions Sets` allows you to modify both `Query` and `doc` passed to the method based on the associated `privilege`. What you have seen earlier means the following:
```python
# 'read': {
	# 'permissions':
[
	PERM(privilege='read'),
	# First, check if call is made by party (authenticated or non-authenticated) is having the privilege 'blog_comment.read'. Note, 'blog_comment.' is omitted for DX reasons. If so, pass the call with Query, doc as-is.
	PERM(privilege='*', query_mod={'status':'approved'})
	# However, if first Permission Set check fails, try again with this Permission Set which allows any party (due to privilege='*'), but modify call Query by inserting the bit {'status':'approved'}, effectively allowing non-('blog_comment.read'-)privileged users to only 'read' comments that are approved, and not pending or deleted comments.
]
```
It's that simple in Nawah to control access and make sure your users can only access what you want them to access without the need to write much beyond config-like combinations of `PERM` objects. You can even take that further by adding any number of `Permissions Sets` each to check specific privilege and accordingly pass or modify `Query` or `doc`, or both.

One more thing present under `read` method definition is `query_args`. Method definition argument `query_args` allows developers to specify set of arguments to be present in call `Query`, ultimately avoiding cases empty `Query`, or tailored one might expose forbidden data. The use-case of `query_args` is very widely open to endless options, but in `BlogComment` it is used to smartly make `read` method work for all your app cases. Let's think about the cases where you might need to make the call to endpoint `blog_comment/read`, what could it be? One of:
1. Attempting to check all pending comments to approve or delete.
2. Revisit deleted comments to confirm once more.
3. Checking all comments on a blog post.

That should be it. A lot of more cases could arise once you increase the level of complexity in your app, but for now that should be it. Now, let your [grey cells](https://www.agathachristie.com/characters/hercule-poirot) do some checks and imagine how `query_args` could help you make sure no intruder would make a ^^^^^^ call? ....

### Comment front-end

## Next
This finalises the tutorial. Next, is expanding this blog app into a magazine that allows users to register and subscribe to categories and tags, and perhaps contribute to the magazine.

A sample of the results of this tutorial is hosted as part of [`Nawah Samples` repository](https://github.com/nawah-io/nawah_samples/).

[Back to Index](../README.md)