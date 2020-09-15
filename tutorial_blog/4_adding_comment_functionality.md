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
                {'status': ATTR.LITERAL(literal=['pending'])}
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
Unlike `Blog` module which you created earlier, `BlogComment` module is having more structure in `methods`. This is due to the nature of comments which requires different kind of management. The combination of two `Permissions Sets` for method `read`, as well as the existence of `query_args`, and the `doc_mod` option for method `create` `Permission Set`, are all working together for that purpose. Next you will learn the purpose of each.

##### `read`
Unlike any other method in `Blog` or `BlogComment` modules, method `read` has two `Permissions Sets` defined by two `PERM` objects, although first `Permission Set` is more familiar. Second `Permission Set` has ...

### Comment front-end

## Next
This finalises the tutorial. Next, is expanding this blog app into a magazine that allows users to register and subscribe to categories and tags, and perhaps contribute to the magazine.

A sample of the results of this tutorial is hosted as part of [`Nawah Samples` repository](https://github.com/nawah-io/nawah_samples/).

[Back to Index](../README.md)