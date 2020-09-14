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
In addition to what you learnt earlier on `create_time Special Attr`, you can see different `Attrs Types` this time. You can learn more on each from 

### Comment front-end



## Next
This finalises the tutorial. Next, is expanding this blog app into a magazine that allows users to register and subscribe to categories and tags, and perhaps contribute to the magazine.

A sample of the results of this tutorial is hosted as part of [`Nawah Samples` repository](https://github.com/nawah-io/nawah_samples/).

[Back to Index](../README.md)