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
            'permissions': [PERM(privilege='create'), PERM(privilege='*', doc_mod={'status':'pending', 'status_note':''})],
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
1. Checking all comments on a blog post: which can be done by making a call with `Query` set as `[{'blog': 'BLOG_ID_HERE'}]`.
2. Attempting to check all pending comments to approve or delete: which can be done by making a call with `Query` set as `[{'status': 'pending'}]`.
3. Revisit deleted comments to confirm once more: which can be done by making a call with `Query` set as `[{'status': 'deleted'}]`.

That should be it. A lot of more cases could arise once you increase the level of complexity in your app, but for now that should be it. Now, let your [grey cells](https://www.agathachristie.com/characters/hercule-poirot) do some checks and imagine how `query_args` could help you make sure no intruder would make a malicious call? Let's look at the possibilities:
* Your front-end non-privileged `Permission Set` of method `read` would allow intruder to read all `BlogComment` docs by simply calling `blog_comment/read` endpoint without passing any `Query Args`. This would be translated at method read as `[{'status': 'approved'}]`, which would should all comments across your app.
* You can limit this by setting `query_args` to: `'query_args': {'blog': ATTR.ID()}`. Note this is using a single `query_args` set, without a list being present. This is another DX aspect of Nawah that allows developers to skip lists for `query_args` when only one `query_args` set is expected to be met.
3* Now let's imagine you are authenticating as `ADMIN` in order to manage comments. You make the call to endpoint `blog_comment/read` with `Query` set as `[{'status': 'pending'}]` in order to get all `BlogComment` docs with `pending` status to review them. You will be shut with the following error: `Could not match query with any of the required query_args. Failed sets:['blog': Missing]"`.
* The error is self-explanatory, informing you `query_args` sets are present on the method, and it's expecting you to meet at least one of the sets (which is currently one only), but you failed to because `Query` is missing `blog` in it.
* Although you can overrun this error by passing `blog`, effectively meeting the requirements of `query_args`, but this would mean at `ADMIN` level you would be required to review comments on blog level, and not as a whole, which is not good UX.
* `query_args` for rescue! Since `query_args` allow for multiple, endless, combinations of sets, you shall update it to what it looks like in the module earlier.
* The results of adding `query-args` set `{'status': ATTR.LITERAL(literal=['pending', 'deleted'])}` is allowing calls to method `read` to skip `blog` and have `status` with the value being either of `pending, deleted`. How would this affect the calls in all the cases?
* If you are authenticated as `ADMIN`, effectively having the privilege `blog_comment.read`, you shall be able to still make `blog_comment/read` calls with `blog` in `Query` to read all comments on the blog post regardless of `status`.
* You, as `ADMIN`, shall also be able to use the second `query_args` set to get all comments that are either of `pending, deleted` by passing `status` in `Query`. Note that passing `Query` as `[{'status': 'approved'}]` without `blog` would result in error: `Could not match query with any of the required query_args. Failed sets:['blog': Missing], ['status': Invalid]"` which explains your call failed because it failed to meet either of the `query_args` sets.
* For non-privileged users, as `ANON` users, They would still be able to use either of the two `query_args` sets, but here comes `query_mod` from the `Permission Set` that allows non-privileged users to access method `read`--If non-privileged user attempts a malicious call by calling `blog_comment/read` with `Query` as `[{'status': 'pending'}]`, the call would pass second `query_args` set requirements, but `query_mod` of `Permission Set` would modify call `Query`, inserting `{'status': 'approved'}`, which would then make final `Query` that is reaching method `read` set as `[{'status': 'pending'}, {'status': 'approved'}]`, which would effectively result in no values at all because `BlogComment` docs can't match both the conditions `'status' is 'pending' AND 'status' is 'approved'`.

Congratulations! You just created secure API endpoint without writing single line of code checking users permissions! This is integral part of Nawah, which leaves developers to architect the whole app before even needing to write any code.

##### `create`
Similar to using `Query Modifier` or `query_mod` in method `read` to modify `Query` on specific `Permission Set`, method `create` is using another aspect which is `Doc Modifier` or `doc_mod` which does the same but to call `doc`.

As you can tell already, method `create` is the endpoint to be used to add comments to current blog posts, this asserts it will be used either by you to comment on the blog posts with additional follow-up comments, or by your visitors. In this case, you expect your visitors comments to have `status` set to `pending`, while not for you. The combination of the two `Permissions Sets` on method `create` replicate this behaviour, again, without the need to write any single line verifying user's privileges.

### Comment front-end

> The approach you will use to implement comments functionality afterwards is very subjective and there are endless approaches, but to maintain clean, high-performing, `hello world`-base the following approach was selected.

Now that your app includes `BlogComment` module, it is time to add the functionality to your front-end. To begin, let's think the choices:
1. Blog posts shall be shown on the front-end without comments initially to limit unnecessary footprint on clients memory, as well as computing at endpoint-side.
2. A button indicating the action to load comments should be present at the end of every blog post.
3. Once the button is clicked or tapped, it should start the action to load comments and get those comments to show, replacing the button.

Easy, isn't it? To implement this approach without going more classical by putting all the code back into the class, you will:
1. Within `HomeComponent` class, Define an object that is placeholder dictionary mapping blog posts `_id` to `Observable` that is a call to `blog_comment/read` endpoint.
2. Create a method that creates call `Observable` for any needed blog post.
3. Update the template to make use of the previous two elements.

In code, this means the following:
```typescript
//   This is being added after:
//   blogs$: Observable<Res<any>> = this.nawah.call({
//     endpoint: 'blog/read'
//   });

  comments$: {
    [key: string;]: Observable<Res<any>>
  } = {}

//   This is being added after:
//   constructor(private nawah: NawahService) { }

  function loadComments(blog_id: string): void { // Best Practice Note: This is not snake_case, but since unique attribute of MongoDB is '_id' the underscore is skipping capitalisation
    this.comments$[blog_id] = this.nawah.call({
      endpoint: 'blog_comment/read',
      query: [{ blog: blog_id }]
    });
  }
```
and for `home.component.html`:
```html
<!--
    This is being add after:
        <p class="blog-post-meta">{{ blog.create_time }}</p>

        {{ blog.content.en_AE }}
    
    and, before:
    </div> /.blog-post
-->

        <div *ngIf="comments$[blog._id]">
            <div *ngFor="let comment of (comments$[blog._id] | async)?.args.docs" class="card">
                {{ comment.content }}
            </div>
        </div>
        <button *ngIf="comments$[blog._id]" class="btn btn-secondary" (click)="loadComments(blog._id)">Load Comments</button>
```

## Next
This finalises the tutorial. Next, is expanding this blog app into a magazine that allows users to register and subscribe to categories and tags, and perhaps contribute to the magazine.

A sample of the results of this tutorial is hosted as part of [`Nawah Samples` repository](https://github.com/nawah-io/nawah_samples/).

[Back to Index](../README.md)