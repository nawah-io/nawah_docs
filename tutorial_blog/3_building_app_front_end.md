[Back to Tutorial Index](./README.md)

# Tutorial: Personal Blog App

## 3. Building App Front-end
Nawah is a complete solution. That is why building the front-end is part of Nawah, and not a burden thrown on developers using Nawah. Next, you will learn how to use Nawah SDK for Angular (aka `ng-nawah`) to swiftly create a front-end for your app.

> The following steps assume you have most basic knowledge of building apps with Angular and that you have latest version of Angular CLI installed. If not, follow guide on [https://angular.io/guide/setup-local](https://angular.io/guide/setup-local) to install latest Angular CLI.

#### Monorepo Note
If you are a fan of monorepo approach for the projects, the advised best practice on this approach is to create your front-end in `frontend` folder. In some cases you will be having multiple front-ends targeting different platform categories, platforms, development tech, and concerns. In such cases you should having the front-end folder as the following scheme:
```
frontend[_PLATFORM_CATEGORY[_PLATFORM[_DEV_TECH[_CONCERN]]]]
```
which is broken down as:
* `frontend` is the folder name to begin with. If you are only having one front-end, use this only.
* `PLATFORM_CATEGORY` is the target platform category if you are targeting more than one platform category, such as: `web`, `mobile`, `desktop`, or `tv`.
* `PLATFORM` is the target platform in the category. Use the brand name of the platform, such as: `android`, `ios`, `electron`, or `tizen`. This is applicable for non-web front-ends only.
* `DEV_TECH` is the primary development tech used in the front-end if you are building multiple front-end with multiple techs, such as: `angular`, `vue`, `java`, `kotlin`, `objc`, or `swift`.
* `CONCERN` is the concern of the front-end if you are building two front-ends targeting same platform but separating the concerns of each, such as: `admin`, `user`, `lite`, ...etc.

### Building Front-end Shell
Begin with creating the front-end using Angular CLI:
```bash
ng new my-awesome-blog
```
and make sure you add routing. For the purpose of this tutorial use CSS for styling.

This will create new folder `my-awesome-blog` containing Angular workspace and project with the same name. If you are taking the monorepo approach and would like to get things done in one step use:
```bash
ng new my-awesome-blog --directory frontend
```
which would create the project workspace in `frontend` folder.

Rather than building the whole front-end in this tutorial, you will use one of the examples done by Bootstrap team. First, get your `index.html` ready to work with Bootstrap by updating it to look like:
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MyAwesomeBlog</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">

  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">

</head>
<body>
  <app-root></app-root>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.5.1/jquery.min.js" integrity="sha512-bLT0Qm9VnAYZDflyKcBaQ2gg0hSYNQrJ8RilYldYQ1FxQYoCLtUjuuRuZo+fjqhx/qtq/1itJ0C2ejDxltZVFg==" crossorigin="anonymous"></script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js" integrity="sha384-OgVRvuATP1z7JjHLkuOU7Xw704+h835Lr+6QL9UvYjZE3Ipu6Tp75j7Bh/kR0JKI" crossorigin="anonymous"></script>
</body>
</html>
```

Next you create home page component--Generate a component using:
```bash
ng g c home
```
Now, your component is ready but you need to bind it to a route--Update `app-routing.module.ts`, replacing `routes` definition with:
```typescript
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  { path: '', redirectTo: '/home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent }
];
```
then, replace the contents of `app.component.html` with:
```html
<router-outlet></router-outlet>
```

Lastly, copy the example `Blog` from [https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/blog/index.html](https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/blog/index.html), and replace the one-liner `home.component.html` with it. Repeat the same for the style file of the component with CSS file: [https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/blog/blog.css](https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/blog/blog.css).

> ⚠ Warning: When copying the example from the previous links, and any additional examples, you will encounter template tags bound to Bootstrap project using double curly braces (`{{ ... }}`). These are used in Angular for the same. To avoid errors about processing the templates after copying to your front-end, remove all double curly braces and the content contained within.

Begin the development server using:
```bash
ng serve
```
then, browse `http://localhost:4200` to be redirected to `/home` route which should present you with a page looks close to [https://getbootstrap.com/docs/4.5/examples/blog/](https://getbootstrap.com/docs/4.5/examples/blog/). Stop the development server for the next step.

### Show App Data on Front-end
Now, that you have the front-end shell ready, you can begin to make the front-end dynamic by making the content of it reflecting data fetched from your app API. For that task, you will need `ng-nawah`--Install Nawah SDK for Angular using:
```bash
npm i --save ng-nawah
```
and import it in your `app.module.ts` in `imports` section like:
```typescript
import { NgNawahModule } from 'ng-nawah';
// ...
  imports: [
    // ...
    NgNawahModule,
  ],
```

Your front-end is now ready to have the instructions to connect and read data from API. To recall, you used Nawah Sandbox to connect by initialising the connection first and then making a call. In your front-end you will repeat the same sequence of events. It's advised that you keep your SDK initialisation in `AppComponent` as it gets the connection made as soon as the app is ready. In your `app.component.ts`, add `OnInit` implementation to `AppComponent`, like:
```typescript
export class AppComponent implements OnInit
```
then, add following `constructor`, `ngOnInit` methods:
```typescript
  constructor(private nawah: NawahService) {}

  ngOnInit() {
    this.nawah.init({
      api: 'API_URI',
      anonToken: 'ANON_TOKEN',
      authAttrs: ['email'],
      appId: 'APP_ID',
    });
  }
```
then, refer to [./writing_blog_module.md#connecting-to-api](./writing_blog_module.md#connecting-to-api) to realise values for `API_URI`, `ANON_TOKEN`. Leave `APP_ID` as is for now. Save everything, start the development server again. Check your console and confirm no errors are showing, then check your app API console to confirm you are seeing the connection request such:
```log
2020-08-01 07:21:44,252  [DEBUG]  Websocket connection starting with client at '172.23.0.1'
2020-08-01 07:21:44,254  [DEBUG]  Websocket connection #'3' ready with client at '172.23.0.1'
2020-08-01 07:21:44,284  [DEBUG]  Received new message from session #'3': {"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbmRwb2ludCI6ImNvbm4vdmVyaWZ5IiwiZG9jIjp7ImFwcCI6IkFQUF9JRCJ9LCJzaWQiOiJmMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTIiLCJ0b2tlbiI6Il9fQU5PTl9UT0tFTl8zMTk3MDgwNTYwNTg5Njc3Mjg1NDczMjQiLCJxdWVyeSI6W10sImF3YWl0QXV0aCI6ZmFsc
2020-08-01 07:21:44,285  [DEBUG]  ip_quota on session #'3': {'172.23.0.1': {'counter': 499, 'last_check': datetime.datetime(2020, 8, 1, 7, 20, 54, 475683)}}
2020-08-01 07:21:44,285  [DEBUG]  session_quota: on session #'3': {'counter': 40, 'last_check': datetime.datetime(2020, 8, 1, 7, 21, 44, 254178)}
2020-08-01 07:21:44,285  [DEBUG]  Decoded request: {"endpoint": "conn/verify", "doc": {"app": "APP_ID"}, "sid": "f00000000000000000000012", "token": "__ANON_TOKEN_", "query": [], "awaitAuth": false, "call_id": "6ajmw", "iat": 1596266504, "exp": 1596352904}
2020-08-01 07:21:44,285  [DEBUG]  Connection on session #'3' is verified.
```

If your browser console is showing any errors, confirm whether these errors are related to `ng-nawah` or another element. If it's related to `ng-nawah` confirm again your `API_URI`, and `ANON_TOKEN` values.

You can now make calls as you did in Nawah Sandbox to read blog posts. To do so, go to `home.component.ts`, and add the following definitions methods:
```typescript
export class HomeComponent {

  blogs$: Observable<Res<any>> = this.nawah.call({
    endpoint: 'blog/read'
  });

  constructor(private nawah: NawahService) { }

}
```
which creates an `Observable`. As you can see, the only defined value in `callArgs` is `endpoint` which is the same you used in Sandbox to read the blog posts. Now you will subscribe to `blogs$ Observable` from the template with `async pipe`--Replace the three `div` elements with the class `blog-post` from `home.component.html` and replace them with:
```html
<!--
    This is being added after:
      <h3 class="pb-4 mb-4 font-italic border-bottom">
        From the Firehose
      </h3>
-->
      <article *ngFor="let blog of (blogs$ | async)?.args.docs" class="blog-post">
        <h2 class="blog-post-title">{{ blog.title.en_AE }}</h2>
        <p class="blog-post-meta">{{ blog.create_time }}</p>

        {{ blog.content.en_AE }}

      </article><!-- /.blog-post -->
```

What you see here is template subscribing to `blogs$ Observable`, which gets the SDK to call your app API and return the results in the same structure you came across earlier in Nawah Sandbox. However, this time you are binding the results to template for-loop which allows you to extract individual blog posts and show them. Since, you only have one blog post you will only get one in the page. Notice, 
What you see here is template subscribing to `blogs$ Observable`, which gets the SDK to call your app API and return the results in the same structure you came across earlier in Nawah Sandbox. However, this time you are binding the results to template for-loop which allows you to extract individual blog posts and show them. Since, you only have one blog post you will only get one in the page.

Pay attention to `en_AE` in `blog.title.en_AE`, and `blog.content.en_AE` which gives you an idea on how to read different localisations values for `attrs` with `LOCALE Attr Type`. Change both to `ar_AE` to see different values showing corresponding to `ar_AE` locale.

If you want to see more blog posts, you can go back to Nawah Sandbox and create another blog post, then come back to your front-end to see the post showing up.

### Build Admin Dashboard
However, in real life, we don't use consoles to manage apps, we use admin dashboards. For that, next, you will build simple admin dashboard that allows you to create, list and update blog posts.

You need to add some structure to the front-end--Generate the following component:
```bash
ng g c admin-container
```
and, replace the new component template with:
```html
<router-outlet></router-outlet>
```

You will use this component as container to admin section of your front-end. This is good practice for managing access and providing skeleton structure for the admin dashboard. But, since this is not the aspect of this tutorial, you will only get the basic setup for now--Update `routes` in `app-routing.module.ts` adding:
```typescript
  // This is being added after:
  // { path: 'home', component: HomeComponent },

  {
    path: 'admin', component: AdminContainerComponent, children: [
      { path: '', redirectTo: '/admin/login', pathMatch: 'full' },
    ]
  },
```

> The best practices for building a complete isolated section in your app is building a separate module managing those routes, then use lazy-loading in your routing module. This approach would complicate the process of this tutorial and thus ignored. This tutorial isn't necessarily advising on best practices for Angular.

#### Build Login Page
First part of the admin dashboard is building login page which allows you to authenticate similarly to what you did in Nawah Sandbox--Begin by creating `login` component:
```bash
ng g c admin-login
```
and, updating `routes` to include `login` path as a child as:
```typescript
  // This is being added after:
  // { path: '', redirectTo: '/admin/login', pathMatch: 'full' },

  { path: 'login', component: AdminLoginComponent },
```
and, lastly, copy the `Sign-in` example from Bootstrap from the link: [https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/sign-in/index.html](https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/sign-in/index.html), and the CSS file: [https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/sign-in/signin.css](https://raw.githubusercontent.com/twbs/bootstrap/main/site/content/docs/5.0/examples/sign-in/signin.css).

You are almost ready to add business logic required to process the authentication call. For that, you would need to consider creating another component serving as dashboard after successful `auth` call--Generate `admin-dashboard` component as:
```bash
ng g c admin-dashboard
```
and, add it to `routes` as:
```typescript
  // This is being added after:
  // { path: 'login', component: AdminLoginComponent },

  { path: 'dashboard', component: AdminDashboardComponent },
```

Back to `AdminLoginComponent`, update it to become:
```typescript
export class AdminLoginComponent {

  email: string = '';
  password: string = '';

  constructor(private nawah: NawahService, private router: Router) { }

  auth() {
    try {
      this.nawah.auth('email', this.email, this.password).subscribe({
        next: (res) => {
          alert('Authenticated!');
          this.router.navigate(['/admin', 'dashboard']);
        },
        error: (res: Res<Doc>) => {
          alert(res.msg || res);
        },
      });
    } catch (err) {
      alert(err);
    }
  }

}
```
then, in template, add event binding to form `submit` event, and `name` property and `ngModel` bindings to email, password inputs. So, your template would like like:
```html
<form class="form-signin" (submit)="auth()">
  <!-- ... -->
  <label for="inputEmail" class="visually-hidden">Email address</label>
  <input name="email" type="email" id="inputEmail" class="form-control" placeholder="Email address" required autofocus [(ngModel)]="email">
  <label for="inputPassword" class="visually-hidden">Password</label>
  <input name="password" type="password" id="inputPassword" class="form-control" placeholder="Password" required [(ngModel)]="password">
```
for `ngModel` not to complain, you would need to import `FormsModule` in `app.module.ts` as:
```typescript
import { FormsModule } from '@angular/forms';
// ...
  imports: [
    // ...
    FormsModule,
  ],
```

Now, is your moment of truth--Start the development server and navigate to `http://localhost:4200/admin` or equivalent. You shall be redirected to `/admin/login`, which looks like [https://getbootstrap.com/docs/4.5/examples/sign-in/](https://getbootstrap.com/docs/4.5/examples/sign-in/). Attempt to type any email address and the password `123456` and submit the form for testing it. You will see an alert message as:
```
Error: Password should be 8 chars, contains one lower-case char, one upper-case char, one number at least.
```
that's right! Nawah has API-level password control. This assures minimum level of security it met on all Nawah apps regardless of infosec-background of the developer. On code-side, this error is the one being caught at `catch` expression. The reason is this is an exception raised by SDK, and not error response returned by the API. This is why it's important to wrap `auth` calls with `try..catch` expression.

Now, try inputting valid password format, but still not the correct values from App Config and hit submit. You will see an alert message as:
```
Wrong auth credentials.
```
which is resulting from `error` handler of the `auth` call subscription.

So, you know now the two different errors bound to an `auth` call:
1. SDK errors related to password strength, and/or `auth` call `authVar` param, which are raised and should be caught in `catch` expression.
2. API errors related to failed attempt to authenticate, which are returned as part of the results of the call, and should be handled by `error` handler of the subscription.

Finally, you should test the valid credentials you already obtained when authenticating in Nawah Sandbox. You will see an alert message as:
```
Authenticated!
```
which is clearly coming from `next` handler of the `auth` call subscription. After closing the alert you shall be redirected to `/admin/dashboard` route.

Finally, you can implement `checkAuth` which allowed you to easily re-authenticate in Nawah Sandbox. You will implement `checkAuth` in `AdminContainerComponent` so it attempts to re-authenticate you at any given route--Update `AdminContainerComponent` to be:
```typescript
  constructor(private nawah: NawahService, private router: Router) { }

  ngOnInit() {
    try {
      this.nawah.checkAuth().subscribe({
        next: (res) => {
          console.log('re-authenticated!');
          if (this.router.url == '/admin/login') {
            this.router.navigate(['/admin', 'dashboard']);
          }
        },
        error: (res: Res<Doc>) => {
          if (this.router.url != '/admin/login') {
            this.router.navigate(['/admin']);
          }
        }
      });
    } catch (err) {
      if (this.router.url != '/admin/login') {
        this.router.navigate(['/admin']);
      }
    }
  }
```
and, let's break it down to understand what is going on:
1. You have `try..catch` expression wrapping `checkAuth` call. That is to catch SDk exception raised if no credentials are cached in order to perform `checkAuth` sequence. The body of `catch` expression tests if the current route is not `/admin/login`, meaning the user is not on the login page and re-route the user to login page.
2. You have `checkAuth` call which first checks for any cached credentials (SID and token) and then attempt to call `reauth` internally.
3. `reach` call results will be returned as `checkAuth` results.
4. If `reauth` call is successful, `next` handler of the subscription will be called, logging `'re-authenticated!'` and re-routing the user to `/admin/dashboard` if user is on `/admin/login` route, ultimately replicating the behaviour of auto redirection on saved login.
5. If `reauth` call is failed, `error` handler of the subscription will be called, re-routing the user to `/admin` if user is not on `/admin/login` route.

> The best practices for behaviour related to locking access to routes is by using Angular Route Guards. Since setting-up a structure for the same is beyond the aspect of this tutorial it has not been done that way. This tutorial isn't necessarily advising on best practices for Angular.

To test the behaviour, without closing the current browser session, navigate to `/admin` forcing a page reload. You will be re-routed to `/admin/dashboard` as soon as the page loads. To test the alternative behaviour where you get re-routed to login from other pages if you aren't on `/admin/login` route, add a button to `admin-dashboard.component.html` as:
```html
<button class="btn btn-danger" (click)="signout()">Sign-out</button>
```
and, add the corresponding method to `admin-dashboard.component.ts`, after injecting `NawahService`, and `Router`:
```typescript
  signout(): void {
    this.nawah.signout().subscribe({
      next: (res) => {
        this.router.navigate(['/admin']);
      }
    })
  }
```

if you click the new button on dashboard page you should be re-routed to `/admin/login` as a result successful `signout` call, and `next` handler of `signout` subscription. Now, navigate to `/admin/dashboard` _without authenticating_, and you shall be re-routed to `/admin/login` as results of `catch` expression in `AdminContainerComponent.ngOnInit` due to the exception raised by SDK when no cached credentials were found to attempt `checkAuth` call.

You do now have a quick authentication system in place. A lot of improvements can be added here, but for now that is what you need to begin with building front-end for your Nawah app. One last fun thing to do, is showing the user name on dashboard as a greeting--Update `AdminDashboardComponent` adding the getter:
```typescript
  get user(): Doc {
    return this.nawah.session?.user;
  }
```
and on template side, replace default template text, but not Sign-out button, with:
```html
Howdy, {{ user?.name.en_AE }}!
```

If you try to authenticate now you will be re-routed to dashboard page and greeted with:
```
Howdy, __ADMIN!
```
which is the default name for ADMIN user of Nawah.

Although, the time required to process the previous steps is not little, but it's only due to fact you are only getting started with Nawah and its SDK usage. Once you are familiar with the previous, implementing the same (or, even better with global `authed$` and `inited$` subscriptions, and Route Guards) should be a task that requires less than 30 minutes per project.

#### New Blog Post Page
Now, you can authenticate and even get greeted. The next step is to create component required to create new blog post--Generate following component:
```bash
ng g c admin-blog-new
```
and, update `routes` as:
```typescript
  // This is being added after:
  // { path: 'dashboard', component: AdminDashboardComponent },

  { path: 'blog-new', component: AdminBlogNewComponent },
```

You need now a form to capture user input, the simplest is the following, which you need to add it to `admin-blog-new.component.html`:
```html
<h1>New Blog Post</h1>
<a [routerLink]="['/admin', 'dashboard']" class="btn btn-primary text-white">Back to Dashboard</a>
<form (submit)="createBlogPost()">
  <input name="title_ar" class="form-control" placeholder="Title (Arabic)" [(ngModel)]="doc.title.ar_AE">
  <input name="title_en" class="form-control" placeholder="Title (English)" [(ngModel)]="doc.title.en_AE">

  <textarea name="content_ar" class="form-control" placeholder="Content (Arabic)" [(ngModel)]="doc.content.ar_AE"></textarea>
  <textarea name="content_en" class="form-control" placeholder="Content (English)" [(ngModel)]="doc.content.en_AE"></textarea>

  <button type="submit" class="btn btn-primary" [disabled]="msg.type == 'success'">Submit</button>

  <div *ngIf="msg.type == 'success'" class="alert alert-success">
      {{ msg.content }}
  </div>
  <div *ngIf="msg.type == 'fail'" class="alert alert-warning">
    {{ msg.content }}
  </div>
</form>
```
and, the following in `admin-blog-new.component.ts`:
```typescript
export class AdminBlogNewComponent {

  doc: {
    title: {
      ar_AE: string;
      en_AE: string;
    },
    content: {
      ar_AE: string;
      en_AE: string;
    },
  } = {
    title: {
      ar_AE: undefined,
      en_AE: undefined,
    },
    content: {
      ar_AE: undefined,
      en_AE: undefined,
    },
  };

  msg: {
    type?: 'success' | 'fail';
    content?: string; 
  } = { };


  constructor(private nawah: NawahService) { }

  createBlogPost(): void {
    this.msg = { };
    this.nawah.call({
      endpoint: 'blog/create',
      doc: this.doc
    }).subscribe({
      next: (res) => {
        this.msg = {
          type: 'success',
          content: res.msg
        };
      },
      error: (res: Res<Doc>) => {
        this.msg = {
          type: 'fail',
          content: res.msg || (res as any)
        };
      }
    });
  }

}
```

This is mere Angular work, but let's break it down:
1. The template is beginning with a link to dashboard so user can go back to dashboard if not intending to create blog post.
2. Form begins with event handler binding for `submit` event with method `createBlogPost` which will be described later.
3. Inputs for each of the two attrs for `Blog` module, `title`, and `content`, each for the two localisations supported by the app, `ar_AE` and `en_AE`, are present on the form and bound using `ngModel` to corresponding values of `doc` property of `AdminBlogNewComponent`.
4. Submit button is present. Button would be disabled if a successful `blog/create` call has occurred to prevent duplicate data insertion.
5. Two div elements implementing Bootstrap Alert component to show messages on the status of the call, bound to `msg` property, which uses simple structure to determine current message and its content.
6. On code side, `createBlogPost` method does the following:
   1. Reset status of `msg` property in order to hide any possible `fail`-type messages.
   2. Make `blog/create` call similar to one you did in Nawah Sandbox, with `doc` property passed as `doc` in `callArgs`.
   3. Note that, there's no any data verification occurring here.
   4. Call subscription has `next` and `error` handlers to update `msg` property accordingly.


You can begin to test your form by navigating to `/admin/blog-new` and then trying to submit the form with no data at all, which would result in error referring to missing value for `title` attr. Then, you can submit form with only `title` and no `content`, and you would get error referring to missing value for `content`. After confirming the error conditions are occurring, you can submit complete (or, down to the default localisation only) values and get green message on successful call. Once call is successful, Submit button would be disabled to prevent duplicate insertion. You can then Navigate back to home page of the front-end to see you new blog post.

#### List All Blog Posts
You have at least two blog posts now. You probably want a list these blog posts so you can implement editing and deleting. To begin, generate the following component:
```bash
ng g c admin-blog-list
```
then, update `routes`, adding:
```typescript
  // This is being added after:
  // { path: 'blog-new', component: AdminBlogNewComponent },

  { path: 'blog-list', component: AdminBlogListComponent },
```
then, use the following template:
```html
<h1>Blog Posts</h1>
<a [routerLink]="['/admin', 'dashboard']" class="btn btn-primary text-white">Back to Dashboard</a>
<table class="table">
  <thead>
    <tr>
      <th>Title (Arabic)</th>
      <th>Title (English)</th>
      <th>Create Time</th>
      <th>Operations</th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let blog of blogs">
      <td>{{ blog.title.ar_AE }}</td>
      <td>{{ blog.title.en_AE }}</td>
      <td>{{ blog.create_time }}</td>
      <td>
        <a [routerLink]="['/admin', 'blog-edit', blog._id]" class="btn btn-primary">Edit</a>
        <button class="btn btn-danger" (click)="deletePost(blog._id)">Delete</button>
      </td>
    </tr>
  </tbody>
</table>
```
and, the following for your code:
```typescript
export class AdminBlogListComponent implements OnInit {

  blogs: Array<Doc> = [];

  constructor(private nawah: NawahService) { }

  ngOnInit() {
    this.readPosts();
  }

  readPosts(): void {
    this.nawah.call({endpoint: 'blog/read'}).subscribe({
      next: (res) => {
        this.blogs = res.args.docs;
      },
      error: (err: Res<Doc>) => {
        alert(`Unexpected error occurred: ${err.msg || err}`);
      }
    });
  }

  deletePost(_id: string): void {
    if (confirm('Are you sure you want to delete this blog post?')) {
      this.nawah.call({
        endpoint: 'blog/delete',
        query: [{
          _id: _id
        }]
      }).subscribe({
        next: (res) => {
          this.readPosts();
        },
        error: (err: Res<Doc>) => {
          alert(`Unexpected error occurred: ${err.msg || err}`);
        }
      });
    }
  }

}
```

Let's break it down:
1. Similar to New Blog Post page you have a header indicating the page, and a button to return to dashboard.
2. You have a regular table structure, except it uses `table` class of Bootstrap to make it looks better.
3. The table is having a `*ngFor` expression that loops over `blogs` property which is list of docs. This is the results of `blog/read` call. Unlike home page, you aren't getting the fancy template subscription treatment here as you will need to refresh the list later.
4. On the last column of each row in the table, you have `Operations` which are Edit and Delete. Edit is not implemented, but Delete is--Delete button is bound to `deletePost` method with `click` event.
5. On code side, you have `ngOnInit` method which is called when the component is initialised. The method does only one thing, which is calling `readPosts`.
6. `readPosts` is regular call to `blog/read` endpoint, but with the results being stored in `blogs` property.
7. `deletePost` method is defined with `_id` param. This is the unique key of the blog post the user would delete. It begins with a confirmation box, which is if returned `true`, it would make the call to `blog/delete` endpoint with `query` in `callArgs` stating the `_id` of the doc to be deleted. Note the structure of the `query` object.
8. When successful, `next` handler of the subscription would call `readPosts` again, making the list of docs getting refreshed with the new data provided by the API.

You can now delete previous blog posts, create new fancy ones, and check them on your front-end.

#### Editing Blog Posts
> ⚠ Warning: This section does violate DRY approach as you will be implementing edit feature without re-using current elements. This is plain anti best practice. Make sure to consider better approach for production apps.

Now that you began writing your blog posts, you probably want to update some of them at some point. For this purpose you will reuse your New Blog section of your frontend. Begin with generating the component:
```bash
ng g c admin-blog-edit
```
then, update `routes`, adding:
```typescript
  // This is being added after:
  // { path: 'blog-list', component: AdminBlogListComponent },

  { path: 'blog-edit/:_id', component: AdminBlogEditComponent },
```

As you can tell from the route, you will be passing the blog post `_id` to `AdminBlogEditComponent` which then shall process it and get it from the endpoint and prepares it for editing. To achieve this, copy over your `AdminBlogNewComponent` but replace the following changed portion:
```typescript
export class AdminBlogEditComponent implements OnInit {

	// doc: { ...

	// msg: { ...


	constructor(private nawah: NawahService, private route: ActivatedRoute) { }

	ngOnInit() {
		this.nawah.call({
			endpoint: 'blog/read',
			query: [{ _id: this.route.snapshot.params._id }]
		}).subscribe({
			next: (res: Res<Doc>) => {
				this.doc.title = res.args.docs[0].title;
				this.doc.content = res.args.docs[0].content;
			}
		});
	}

	updateBlogPost(): void {
		this.msg = {};
		this.nawah.call({
			endpoint: 'blog/update',
			query: [{ _id: this.route.snapshot.params._id }],
			doc: this.doc
		}).subscribe({
			next: (res) => {
				this.msg = {
					type: 'success',
					content: res.msg
				};
			},
			error: (res: Res<Doc>) => {
				this.msg = {
					type: 'fail',
					content: res.msg || (res as any)
				};
			}
		});
	}

}
```
and, copy over the template from the earlier component but make sure to change the title to `Edit Blog Post`, and change the method bound to `submit` event according to your class method name.

To test this, navigate to `/admin/blog-list` and click `Edit` button for any of your blog posts, you will be routed and your blog post title and content in Arabic and English will be there for you to update it.

## Next
Now that you have your blog available for your audience, you might like to provide them with an interactive space, where they can comment and reply to comments. Next, you will create `Comment` module which provides this functionality.

[Continue to: 4. Adding Comment Functionality](./adding_comment_functionality.md)