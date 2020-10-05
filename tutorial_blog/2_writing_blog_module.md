[Back to Tutorial Index](./README.md)

# Tutorial: Personal Blog App

## 2. Writing Blog Module
The methodology to adding single functionality for Nawah apps is creating a Nawah module. A Nawah module is a Python class inheriting Nawah `BaseModule`; One aspect of Nawah is bootstrapping the process of creating modules with [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) operations. This allows developers to focus on building the advanced aspects of their apps, while the routine operations are available out-of-the-box for developers by adding set of instructions. To begin adding blog feature to your app, you need to being by creating a Python module, a file with extension `.py` in your app package; Create new empty file `blog.py` in `/packages/my_awesome_blog/` directory. Now, you need to start creating your `Blog` (Python class, and, ) Nawah module in the new created file. The next steps underline the process:

### Blog Module
Start by adding the following code to your new file:
```python
from nawah.base_module import BaseModule
from nawah.classes import ATTR, PERM

class Blog(BaseModule):
    collection = 'blogs'
    attrs = {
        'title': ATTR.LOCALE(),
        'content': ATTR.LOCALE(),
        'create_time': ATTR.DATETIME(),
    }
    methods = {
        'read': {
            'permissions': [PERM(privilege='*')],
        },
        'create': {
            'permissions': [PERM(privilege='create')],
        },
        'update': {
            'permissions': [PERM(privilege='update')],
        },
        'delete': {
            'permissions': [PERM(privilege='delete')],
        },
    }
```
Let's explore the different parts of the previous code:

#### `class Blog(BaseModule)`
Keyword `class` is how you define a class in Python. The class `Blog` uses `UpperCase` naming scheme, and that's essential in Nawah. Another, even more important, aspect of defining Nawah modules is inheriting `BaseModule`. `BaseModule` is collection of meta methods and attributes that allow developers to write less code, while getting more functionality. `BaseModule` shall be imported with the following import syntax:
```python
from nawah.base_module import BaseModule
```

#### `collection = 'blog'`
On to attributes of Python class, Nawah module, `Blog`. Attribute `collection` is the collection name to interact with from the database. The methodology of manually setting the collection name is giving opportunity developers complete control in advanced scenarios. The collection name should follow `snake_case`.

#### `attrs[...]`
`attrs` is where you define what data fields your Nawah module is having. Nawah allows you to define any number of `attrs` per your requirements, in addition to defining an `Attr Type`--Every Nawah module `attr` has an [`Attr Type`](/reference/controllers/ATTR.md) matching its contents. This is integral part of Nawah, because defining `attrs` and the associated `Attrs Types` releases the developer from building a verification mechanism such as to verify all required fields are present in any data doc added to the database, as well the typing of the content of each field. Going forward, you will learn about `Attrs Types` and `Special Attrs`, however, for now you can see the module defines three `attrs`, namely: `title`, `content`, and `create_time`. Both `title`, and `content` associated with `LOCALE Attr Type`. This means each single field of either of `attrs` hold single string (`STR Attr Type` per Nawah) value representing the corresponding value in each localisation supported in by your Nawah app. This is a feature of Nawah, which allows developers to develop apps without worrying on extending support for new localisations at time of development or any given time in the future. While, `create_time` a `Special Attr` is of `DATETIME Attr Type` meaning the value of this attr is an ISO-formatted date, time string. The value for `create_time Special Attr` is dynamically supplied by Nawah internals at time of creating the doc.

#### `methods[...]`
`methods` is where you define available endpoints for Nawah clients. Endpoints are the form of `snake_case_module_name/method_name` which is used to call specific method. A method in Nawah is by default a `Websocket` method, meaning the majority of the calls in Nawah eco-system are done in parallel, modern, single `Websocket` connection per client. This is a design decision by Nawah. However, Nawah allows developers to set methods to be available over `GET` or `POST` methods of `HTTP/1`. Yet, whatever interface you do choice, you still get all the benefit of defining structural access control attributes to methods in Nawah.

Looking at the code snippet, you can see `methods` define the following CRUD methods: `read`, `create`, `update`, `delete`. In Nawah, all you need to do to get any of the CRUD functionalities on any module is defining a method of the same name. In fact, this is one of the most amazing features about Nawah, allowing you to explore the structure rather than repeating yourself adding such functionalities again and again. All CRUD methods of Nawah are accessible specifically over `Websocket` interface. Method is where you define the previously, swiftly, mentioned access control schemes. For now, you will explore the most basic access control attribute which is `permissions`.

##### `permissions[...]`
`permissions` is required attribute to defining every method in a Nawah module. It is a list (array) of `Permissions Sets`. `PERM` controller can be imported as:
```python
from nawah.classes import PERM
```
every `Permission Set`, a `PERM` object, has required attribute `privilege`. `PERM.privilege` attribute defines what privilege the current user session should have to be able to access the method--In Nawah, every call is bound to a session. Every session is bound to a user. Every user has set of privileges defined by the groups the user is member of. By this logic, even when a user is not authenticated there's a session and a user bound to the call. That's Nawah has runtime and in-database `ANON` session and user. With this you can understand how Nawah framework manages access control at all levels, at any interface, at any authentication status.

### Interacting with from Sandbox
Once you add the previous code snippet to your newly created `blog.py`, launch your app, (or, relaunch it if you have it up already,) so you can begin to interact with the new `Blog` module and feature.

Earlier in [Chapter 1](./getting_started.md) you attempted to confirm your app is launched and read to receive connections and calls by browsing the address it's being served on, however, you didn't interact with the app. At this stage, your app is still only having the backend layer, without any front-end. At Masaar where [LIMP](/what-is-LIMP.md) was developed, it was essential to have the ability to interact with the apps before a front-end is developer. A tool was developed under the name `LIMP Sandbox` was developed for this purpose. Nawah is having an upgraded version of it under the name `Nawah Sandbox` available for use as Github-Pages-hosted webapp at: [https://nawah-io.github.io/nawah_sandbox/](https://nawah-io.github.io/nawah_sandbox/).

Nawah Sandbox allows developers to interact with Nawah apps whether a front-end is available or not. This allows you to access methods that you didn't yet begin to offer in the front-end, quick testing, or data extraction from your app. To begin using it browse the previous URI, you will be greeted with:
1. Page header sporting the title `Nawah Sandbox` and two indicator boxes, allowing you to quickly determine the connection, and authentication statuses.
2. Top area offering the following toolboxes:
   1. Init: This toolbox is where you set your Nawah app backend (aka, API) URI, `anon_token`, and, `auth_attrs`. These are the required values to connect to any Nawah app API.
   2. `Auth`: Once you are connected to your Nawah app API, you might want to authenticate in order to access endpoints that aren't available to `ANON` user. It is this toolbox which allows you to do it.
   3. `Call`: The most basic aspect of Nawah Sandbox is being able to make calls to a Nawah app API without having a front-end. To be able to make calls of any kind, toolbox `Call` is available.
3. Bottom area serving as console to any data sent or received to and/or from the API.

#### Connecting to API
Now that you are familiar with the UI of Nawah Sandbox, begin to use it to interact with your app--Begin by setting the values in `Init` toolbox as the following:
1. API URI: This should be the address your Nawah app API is served on. If your app is running on your computer then your API URI should be: `ws://localhost:8081/ws`. Keep in mind you might still have different hostname, and/or port number.
2. API Anon Token: This is `anon_token` value from your App Config which can be found in `nawah_app.py` file. Nawah CLI creates this value randomly but it always starts with `__ANON_TOKEN_` followed by random number.
3. API Auth Attrs: This is a space-separated list of attrs available for use to authenticate the users. The logic behind having per-app custom authentication attributes is allowing developers to define as many, as custom, authentication attributes as needed. By default, Nawah CLI creates new apps with `email` as the only Auth Attr available. You can confirm the same, be checking the value of `auth_attrs` in `nawah_app.py`. You need to set this field to `email` for now.

Once you have the values in `Init` toolbox hit `init()` button to connect to Nawah app API. The console should show two new messages:
```json
{
  "status": 200,
  "msg": "Connection ready",
  "args": {
    "code": "CORE_CONN_READY"
  }
}
{
  "status": 200,
  "msg": "Connection established",
  "args": {
    "call_id": "a6ggzs",
    "code": "CORE_CONN_OK"
  }
}
```
These messages are received from your Nawah app API indicating a successful connection. Next is to attempt to making some calls.

#### Making Calls in Nawah Sandbox
Now you have already passed a long way--You created the app API, launched it, and connected to it, as if you are connecting from your own front-end app. To make a call you need to know what endpoint you are calling. As explain earlier, endpoint is the format of `snake_case_module_name/method_name`. You also need to know what `Query Args` and `Doc Args` are required to be passed for the method. These are variable values required for methods to act in specific way. The methodology in Nawah is `Query Args` are the arguments you pass to specify conditional arguments, while `Doc Args` are the arguments you pass to set or update values in the database.

Begin with making a simple call to method `read` of module `Blog`. This means the endpoint is `blog/read`--In `Call` toolbox set Endpoint field to `blog/read`. Keep SID, and Token fields without a change as they are already having the correct populated values for the session. Hit `call()`, and you should receive the following message in console:
```json
{
  "status": 200,
  "msg": "Found 0 docs.",
  "args": {
    "total": 0,
    "count": 0,
    "docs": [],
    "groups": [],
    "call_id": "728soh"
  }
}
```
That is valid response. You called module `Blog` asking it to `read` all available blog posts and it responded to you saying `"Found 0 docs."`. The response however includes more than this message. Let's breakdown the response:
1. `status`: HTTP numerical response status code. Possible values are `200`, `400`, `403` and `500`.
2. `msg`: Textual response message.
3. `args`: Additional call response arguments.
   1. `total`: Total number of available docs (read: documents) for query.
   2. `count`: Number of docs returned in this response. This is different from `total` as other value is the total number of available docs, whether returned in this response or not. The difference between the two values show up when you limit number of response docs using `$skip`, and, `$limit` `Special Query Args`, which allow you to query a slice of the available docs.
   3. `docs`: List of returned docs in this call response. Usually, every query is a JSON object having a unique `_id` value, and the `attrs` of the module.
   4. `groups`: ...
   5. `call_id`: A unique identifier for Nawah framework and Nawah client SDK to identify which response belongs to which call. This is an internal working that you should not bother about at all.

The previous response structure is what you will get almost in all of your successful responses. Now that you have understood the nature of the responses you can expect from the Nawah app API, you need to up your game by trying to make further calls and figuring out what other methods are good for.

### Creating Blog Post
> ℹ️ Nawah client SDK and Nawah framework monitor the live of the connection. Normally, a `Websocket` connection breaks when either of the two sides of the connection closes it. However, this measurement was added when in mobile environnement it was noticed that the connection sometimes remain up even if the mobile app is closed. Eventually, `Connection Timeout Workflow` was introduced with Nawah client SDK sending a `hearbeat` call every 30 seconds, keeping Nawah app API aware this client is still up and connected, and respond with `Heartbeat received`. When using Nawah Sandbox this sometimes floods the console with such messages, so keep your console tidy when using Nawah Sandbox using `Clear` button which is located at the end right of the console area header.

> ℹ️ Nawah Sandbox `Call` toolbox takes up a good space. If you ever need to inspect the responses but enjoy larger space, use `Hide` button which is located at the end right of toolboxes area header. You can tap any of the toolboxes buttons to bring the area back to the clicked toolbox.

To be able to read a blog post, you need to create one. In Nawah Sandbox, set the endpoint to `blog/create`, and hit `call()`. You shall receive the following response:
```json
{
  "status": 403,
  "msg": "You don't have permissions to access this endpoint.",
  "args": {
    "code": "CORE_SESSION_FORBIDDEN",
    "call_id": "ruhx6t"
  }
}
```
Let's break down this response:
1. `status`: We have HTTP code 403 which means `FORBIDDEN`.
2. `msg`: Ir clearly says `"You don't have permissions to access this endpoint."`.
3. `args`:
   1. `code`: This new response argument is an indicator of the error type. Here you have `"CORE_SESSION_FORBIDDEN"`. These codes are good to handle errors based on different error responses. Any permission error in Nawah framework will always be having the code `CORE_SESSION_FORBIDDEN`.
   2. `call_id`: No description needed.

You understand now the little differences between a successful call response and a failed one. This will help you further know and interact with these responses in your front-end app. But, back to the response here. We received a permission error. Why is so? Let's understand what is going on:

#### Permissions Check Sequence
In Nawah framework, the internal sequence to checking current session user ability to access specific method, is called `Permissions Check Sequence`. This sequence is distributed over many parts of the framework, but the important part of it for developers is the Permissions Sets added to every method.

Going back to `blog.py` you should notice the following Permissions Sets for the different methods:
```python
        'read': {
            'permissions': [PERM(privilege='*')],
        },
        'create': {
            'permissions': [PERM(privilege='create')],
        },
        'update': {
            'permissions': [PERM(privilege='update')],
        },
        'delete': {
            'permissions': [PERM(privilege='delete')],
        },
```
Let's break down the two different approaches of Permissions Sets in these methods:
1. `read`: Permission Set of `read` method indicates it doesn't require any privileges. The indicator here is the asterisk (`*`) for the argument `privilege` of Permission Set. This means any user, authenticated or not, can access this method.
2. `create`: Permission Set of `create` method indicates it requires user with privilege `blog.create` to access it. The `blog.` suffix is omitted as unnecessary. This means only if either of these are positive, then user would have access to the method:
   1. Non-authenticated, namely `ANON` user, with `blog.create` set in `anon_privileges` App Config Attr.
   2. Authenticated user with `blog.create` set in `default_privileges` in App Config Attr.
   3. Authenticated user with `blog.create` set in user `privileges` own doc attr.
   4. Authenticated user belonging to a group with `blog.create` set in `Group` `privileges` doc attr.
   5. Authenticated user with all privileges, namely `ADMIN` user.

You understand now that controlling access to any method in any module in Nawah apps is very flexible to the point it can be controlled on user or group level. It can be set on app-level App Config Attrs or dynamic as part of every `User` and `Group` docs. You also understand now that getting the error `CORE_SESSION_FORBIDDEN` for `blog/create` method call earlier has happened because you tried accessing a method that requires a privilege non-authenticated user, `ANON`, doesn't have. To resolve this issue you can either give `ANON` user this privilege (by adding `blog.create` to `anon_privileges`), which then exposes this method to the world, or (doing the correct thing and) authenticating as `ADMIN` user, since it's the only user currently available in your app.

#### Authenticating as `ADMIN`
To authenticate in Nawah Sandbox, you will make use of `Auth` toolbox--Go to `Auth` toolbox and set the values as following:
1. Auth Var: The dropdown menu should have only `email` which is from `Init` toolbox Auth Attrs value. Make sure it's set to `email`.
2. Auth Val: Default email address from `ADMIN` user is `admin@app.nawah.localhost`. You can confirm it is the same in `admin_doc.email` App Config Attr in `nawah_app.py` file.
3. Password: There is no default password for `ADMIN`, rather it gets generated dynamically every time you create new app. Get it from `admin_password` App Config Attr in `nawah_app.py`.

Once you set the values, hit `auth()`. If you entered correct values you should get the following message in console:
```json
{
  "status": 200,
  "msg": "You were successfully authed.",
  "args": {
    "session": {
      "user": {
        "_id": "f00000000000000000000010",
        "name": {
          "ar_AE": "__ADMIN",
          "en_AE": "__ADMIN"
        },
        "locale": "ar_AE",
        "create_time": "2020-06-03T08:32:16.630383",
        "login_time": null,
        "groups": [
          "f00000000000000000000013"
        ],
        "privileges": {
          "*": [
            "*"
          ],
          "file": [
            "create"
          ],
          "setting": [
            "update"
          ]
        },
        "status": "active",
        "email": "admin@app.nawah.localhost"
      },
      "groups": [],
      "host_add": "172.23.0.1",
      "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:77.0) Gecko/20100101 Firefox/77.0",
      "expiry": "2020-07-28T07:14:43.446912",
      "_id": "5ef84363d4e47a9487415377",
      "token": "K6fqWwQt-iimeSQjqAh8HzYP2599INhsgyrzobBG0YY"
    },
    "call_id": "4s8txf"
  }
}
```
The message clearly indicates `"You were successfully authed."`. Going forward, if you need to close Nawah Sandbox and start it again, rather than entering the authentication details every time, you can just hit `checkAuth()` in `Auth` toolbox after successfully connecting to your app. `checkAuth()` button calls SDK `checkAuth` method which uses the cached session identifier (or, `SID`), and session token to facilitate easy and secure re-authentication sequence.
 
#### Troubleshooting Authentication
If you fail to authenticate and get the following error message:
```json
{
  "status": 403,
  "msg": "Wrong auth credentials.",
  "args": {
    "code": "CORE_SESSION_INVALID_CREDS",
    "call_id": "go0cki"
  }
}
```
Then you probably are trying to authenticate in an app using database that was created by another app. The methodology in Nawah is the app creates all required docs for its operations (aka, system docs) at first launch, and keeps updating the system docs for all subsequent launches. This, however, has an exception--`ADMIN` user doc doesn't get updated whatsoever. The reason is you should be able to modify `ADMIN` user doc from the app itself once you have already launched it. Basically, it means the default values, along the authentication attributes, such as email and password in this app, are meant to prepare the required `ADMIN` user doc for the app use, but changing any of these values are meant to be done by making calls which update `ADMIN` user doc attrs. Here does the issue where you might have created an app, then launched it. The app in context creates `ADMIN` user doc as itm is supposed to do in the default database `nawah_data`. You then decide to create another app, and forget to check `data_name`, so the new app again uses `nawah_app` database, but this time it doesn't create `ADMIN` user doc as it already exists. If this is what happened you can resolve the issue by any of the following solutions:
> Solutions prefixed with the warning sign (⚠️) are DESTRUCTIVE. It will either change your database partially or completely. Chose wisely.
1. Change `data_name` App Config Attr to a new name such as the project name followed by `_data`. 
2. [⚠️] Drop `nawah_data` database, then launch your new app again to get Nawah to create required system docs again.
3. [⚠️] Use Nawah CLI Arg `--force_admin_check` for `launch` command to force Nawah CLI to update `ADMIN` user doc. This effectively overwrites whatever values are present in the database with the values from your app App Config.

Once you chose which solution to go with, apply it, and try to authenticate again.

#### Again: Creating Blog Post
Now that we are authenticated as `ADMIN`, the user that has access to (almost*) all things in your Nawah app, you can again try your luck and call `blog/create` endpoint. This time you should get the following response:
```json
{
  "status": 400,
  "msg": "Missing attr 'title' for 'create' request on module 'MY_AWESOME_BLOG_BLOG'.",
  "args": {
    "code": "MY_AWESOME_BLOG_BLOG_MISSING_ATTR",
    "call_id": "o6inhv"
  }
}
```
An error--The status code indicates you get an error! Let's inspect the error message. It says `"Missing attr 'title' for 'create' request on module 'MY_AWESOME_BLOG_BLOG'."`. The error message explains that this method call on `MY_AWESOME_BLOG_BLOG` failed because an expected attr `title` was never passed. The naming scheme of the module name is `UPPER_CASE_PACKAGE_NAME_UPPER_CASE_MODULE_NAME`. This is a design decision which allows to easily detect errors and the erring module. In future when you build complex apps which makes internal calls before processing the last action, you might get an error at any step. Knowing exactly which module is complaining is like having a Dowsing method for your app that tells you where the errors are without having to manually dig all of your code.

Back to the error message. It should probably make sense to you as you tried to create a blog post by calling `blog/create` endpoint, but never specified any values. But, which values are you supposed to pass? Let's go back to your `Blog` module and check what `attrs` it does have. You have:
1. `'title': ATTR.LOCALE()`
2. `'content': ATTR.LOCALE()`

This means, your `Blog` modules needs values for two attrs, namely `title`, and `content`. Great! Next is knowing what values you can pass to both of these attrs. This is important because Nawah is very strict on data types--Attrs Types are strictly followed when creating and updating docs. Both the attrs are having the Attr Type `LOCALE`, which is an object taking all possible string values for every localisation supported in the app. These values are all optional except for the one set as `default_locale` App Config Attr. Since you created your app with `--default-config` CLI Arg, you would have following App Config Attrs values:
1. `locales=['ar_AE', 'en_AE']`
2. `locale='ar_AE'`

Previous App Config Attrs show that your app is supporting two localisations `ar_AE`, and `en_AE` and that `ar_AE` is the default. Now you know this, you understand that an attr with Attr Type `LOCALE` is expecting a value in the format of:
```json
{
  "ar_AE": "ar_AE locale value",
  "en_AE": "en_AE locale value"
}
```
or, at least:
```json
{
  "ar_AE": "ar_AE locale value"
}
```
Easy! Let's feed good values to Nawah Sandbox to get it to create the blog post. Head back to Nawah Sandbox `Call` toolbox. Below `Token` field you shall see a tab bar with two tabs: `Query`, and `Doc`. As explained earlier, values meant to specify which docs are affected by your call go into `query`, while actual values that go into the database go into `doc`. This explains we need to add the attrs to `Doc` editor--Set `Doc` editor to the following value:
```json
{
  "title": {
    "ar_AE": "ar_AE locale value",
    "en_AE": "en_AE locale value"
  }
}
```
You probably noticed that the previous `doc` value is specifying only one of the two attrs required by `Blog` module: `title`, and `content`. This is an exercise to see the response of your Nawah app API following specifying one attr. If you hit `call()` you will get:
```json
{
  "status": 400,
  "msg": "Missing attr 'content' for 'create' request on module 'MY_AWESOME_BLOG_BLOG'.",
  "args": {
    "code": "MY_AWESOME_BLOG_BLOG_MISSING_ATTR",
    "call_id": "k8ulfd"
  }
}
```
Very well! This shows different error message but consistent with what we were doing--Rather than complaining about missing `title`, it's now complaining about missing `content` value.

Let's update `doc` to:
```json
{
  "title": {
    "ar_AE": "ar_AE locale value",
    "en_AE": "en_AE locale value"
  },
  "content": {
    "en_AE": "en_AE locale value"
  },
  "create_time": 
}
```
This time we are passing both the attrs, but we are passing `en_AE` value for `content` attr only. Remember, the required value for `LOCALE` Attr Type is only one set as `default_locale` App Config Attr. This shows we are passing wrong value. This is another exercise to see what your Nawah app API would say about it. Hit `call()` and inspect your console for the following response:
```json
{
  "status": 400,
  "msg": "Invalid attr 'content' of type '<class 'dict'>' with required type 'LOCALE' for 'create' request on module 'MY_AWESOME_BLOG_BLOG'.",
  "args": {
    "code": "MY_AWESOME_BLOG_BLOG_INVALID_ATTR",
    "call_id": "mib0zn"
  }
}
```
The message is not of missing attr this time, but of invalid attr. This is expected as the value for `content` is sending only one localisation value, which is not the default localisation. Let's fix the error by changing `en_AE` in `content` to `ar_AE` to get `doc` to look like:
```json
{
  "title": {
    "ar_AE": "ar_AE locale value",
    "en_AE": "en_AE locale value"
  },
  "content": {
    "ar_AE": "ar_AE locale value"
  }
}
```
Finally! A 200-coded-response. You got:
```json
{
  "status": 200,
  "msg": "Created 1 docs.",
  "args": {
    "count": 1,
    "docs": [
      {
        "_id": "5ef859aed4e47a948741537a"
      }
    ],
    "call_id": "udchif"
  }
}
```
What you get when a doc is created is number of docs created in the message as well as in `args.count`. You will also get list of docs in `args.docs` but with `_id` value only. This gives you easy access to the `_id`, the unique identifier, of the docs created. In this case everything is telling you `"Created 1 docs."`, with one doc in `args.docs`. You have created your first blog post!

### Reading Blog Post
You already made `blog/read` call, but received zero-docs response. Give it another shot. This time you will get this:
```json
{
  "status": 200,
  "msg": "Found 1 docs.",
  "args": {
    "total": 1,
    "count": 1,
    "docs": [
      {
        "_id": "5ef859aed4e47a948741537a",
        "title": {
          "ar_AE": "ar_AE locale value",
          "en_AE": "en_AE locale value"
        },
        "content": {
          "ar_AE": "ar_AE locale value",
          "en_AE": "ar_AE locale value"
        },
        "create_time": "2020-06-03T08:32:16.630383",
      }
    ],
    "groups": {},
    "call_id": "0ntom9"
  }
}
```
We have `"Found 1 docs."` message this time. That is great because finally you are making data progress in your personal blog  app. One thing, you might have notices or not, is the value of `content` attr in the single doc in `args.docs`. The value is:
```json
"content": {
  "ar_AE": "ar_AE locale value",
  "en_AE": "ar_AE locale value"
}
```
So, although you passed only `ar_AE` in your `blog/create` call, you are still getting both the localisations in this response. However, the value for `en_AE` which you didn't pass is the same value of `ar_AE`. This is a measurement in Nawah where missing localisations values get to copy the default localisation value in order to keep your app consistent even if you add or modify the list of supported localisations in your app. Later you will learn how to modify this behaviour in your app to fall to `Null` value or a dynamic value generator, but, for now enjoy seeing your app returning consistent values that makes building your front-end less worrying.

One thing to point out is you are still authenticated as `ADMIN`. Make sure that you understand the response of your call is bound to the authenticated user. If you are using Nawah Sandbox to troubleshoot a problem, make sure you are making the calls as the same user or another user of similar groups and privileges. In this case, it would be wise to sign-out and try to make the same call again--Go to `Auth` toolbox and hit `signout()`. You will get the following in your console:
```json
{
  "status": 200,
  "msg": "You are successfully signed-out.",
  "args": {
    "session": {
      "_id": "f00000000000000000000012"
    },
    "call_id": "q1jzis"
  }
}
```
> You recall the note about `checkAuth()`? Will, it doesn't work any more. The reason is, `signout()` does clear the cache of the user-agent, as well as makes `session/signout` call that marks the session as deleted. The same session can't be authenticated again using `SID` and `token` and you will need to `auth()` again in order to be able to user `checkAuth()` for further uses of Nawah Sandbox.

Try making `blog/read` call again. You will get the same response. This is expected as `Blog.read` method is open to all. Remember, `privilege` argument of its Permission Set is `*`.

## Next
Now that you have your app incorporating the core blog functionality, you will begin with building front-end that allow you to present your blog posts to your audience.

[Continue to: 3. Building App Front-end](./3_building_app_front_end.md)

\* Even `ADMIN` user has access limitations--Some methods are of `system-only` nature, which is a naming scheme indicating these methods are available for sporting internal features of Nawah framework. In Nawah naming convention we use `__sys` as value of `privilege` argument of Permission Set to indicate this is a `system-only` method.