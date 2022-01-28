# Practice: Thunk Action Creators

In this practice, you will write thunk action creators that you will use later
with Redux.

## Set up

Clone the starter repo accessible from the `Download Project` button at the
bottom of this page.

1. Run `npm install` in the __backend__ directory to install dependencies.
2. Run `npm run db:setup` to set up the database.
3. Run `npm start` to start the backend server.

In a different terminal, `cd` into the __frontend__ directory of the starter.

1. Run `npm install` in the __frontend__ directory.
2. Run `npm start` in the __frontend__ directory to start the server.

## Installing thunk middleware

Action creators are functions that create and return an action, which is just a
regular JavaScript object. _Thunk action creators_ are functions that return a
_thunk action_, which is a function instead of an object.

Accordingly, for your thunk actions and thunk action creators to work correctly,
you first need to install thunk middleware that will intercept every action and
check to see whether or not it is a function. If the action is a thunk action /
function, the middleware will invoke it; if the action is a regular JS object,
the middleware will pass it on to the reducers.

To install the thunk middleware, open the __index.js__ file in your
__frontend/src/store__ directory. At the top of the file import `thunk` from
`'redux-thunk'`. (It is a default export.) Next, add `thunk` as the first
argument to `applyMiddleware` when you are setting up the `enhancer`. The code
currently does not set up the `enhancer` in production mode, but you need the
thunk middleware in place even in production. Accordingly, after the `if`-clause
checking whether or not `process.env.NODE_ENV !== production`, add an `else`
that sets the `enhancer` equal to `applyMiddleware(thunk)`. The relevant portion
of the file should now look something like this:

```js
if (process.env.NODE_ENV !== 'production') {
  const logger = require('redux-logger').default;
  const composeEnhancers =
    typeof window === 'object' && window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({ trace: true }) : compose;
  enhancer = composeEnhancers(applyMiddleware(thunk, logger));
} else {
  enhancer = applyMiddleware(thunk);
}
```

Your thunk middleware is now installed and ready to go!

## Thunk action creators

Now it's time to write some thunk actions and thunk action creators. A thunk
action--or simply __thunk__--accepts the `dispatch` and `getState` methods
available on the store and can `dispatch` another action after potentially doing
some asynchronous logic.

Because a thunk action creator is just a function that returns a thunk (i.e., a
function) that takes in `dispatch` and `getState` as parameters, most of the
time, you'll see it structured like this:

```js
const thunkActionCreator = () => dispatch => {
  // Thunk logic here
};
```

> Note: The `getState` parameter is often omitted when it is not needed.

This translates to function declaration syntax like so:

```js
function thunkActionCreator() {
  return function thunk(dispatch) {
    // Thunk logic here
  };
}
```

## `fetchArticles`

In the __frontend/src/store/articleReducer.js__ file, write a `fetchArticles`
thunk action creator that uses the [`Fetch` API][fetch] to request articles at
the relative path `/api/articles`. `dispatch` the returned action from the
`loadArticles` action creator. You will want to rewrite the definition of the
`loadArticles` function so that it can accept an `articles` argument, which you
will get by calling the asynchronous `.json()` method on the response you got
back from the `fetch` call. (You no longer need to import `articles` from
__data/data.json__!) Go ahead and `export` the function.

Your thunk action creator might look like this:

```js
export const fetchArticles = () => async dispatch => {
  const response = await fetch('/api/articles');
  const articles = await response.json();
  dispatch(loadArticles(articles));
};
```

Remember to update the definition of the `loadArticles` function.

> Note: Updating the `loadArticles` function will break your app because
> `ArticleList`--as currently configured--will no longer be able to get the list
> of articles to display. Do not worry about this now; you will fix it in the
> next practice!

Time to test! Since the app is not functioning at the moment, you will need to
test your thunk action creator by putting it on the window. In your root
__index.js__, change the `import` from `'./store/articleReducer'` to import
everything from the file as `articleActions`. Then add `articleActions` to the
window.

In the DevTools console of your browser, run this command:

```js
store.dispatch(articleActions.fetchArticles())
```

Check the logger result to make sure that `articleState.entries` has updated!

## `writeArticle`

Now it's your turn! Write a `writeArticle` thunk action creator in the
__frontend/src/store/articleReducer.js__ file that takes in a `payload`, makes a
`POST` request to `/api/articles`, and, if the request is successful, calls
`dispatch` on the return value of `addArticle` passing in the new article from
the response.

**Hint: Remember to use `.json()` to parse your data before dispatching.**

To test your `writeArticle`, run the following commands in your browser console:

```js
let article = {title: 'b2', body: 'sunset', imageUrl: 'https://fancycrave.com/wp-content/uploads/2019/02/Stunning-Orange-and-Purple-Sunset-above-the-Sea-in-Thailand.jpg'}
store.dispatch(articleActions.writeArticle(article))
```

Once again, the logger results should confirm whether or not your article was
created and added to the Redux state.

In the next practice, you will learn how to `dispatch` these thunk action
creators.

## What you have learned

**Congratulations!** In this practice you have learned how to

1. Write thunk action creators

[fetch]: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API