# Sending Data with Fetch

## Learning Goals

- Use fetch() to send data to a remote host

## Introduction

If you think about it, `fetch()` is a little browser in your browser. You
tell `fetch()` to go to a URL by passing it an argument e.g.
`fetch("https://flatironschool.com")` and it makes a network request. You can
then chain calls to `then()`, where each call takes a callback function as argument
and get the content out somewhere where we can edit it or see it.

This is a lot like browsing the web: you change the URL in the URL bar, or you
follow a link and those actions tell the browser to go somewhere else and get
the data. A technical way to describe that is: "The browser implements an HTTP
`GET` to retrieve the content at a URL." It's also 100% technically
correct to say "`fetch()` uses an HTTP `GET` to retrieve the content specified by a
URL."

The browser also provides a helpful model for understanding what _sending_ data
from the browser looks like. We know this as an HTML _form_. Technically speaking,
HTML forms "use an HTTP `POST` to send content gathered in `<input>`
elements to a specified URL" It's also 100% technically correct to say "`fetch()`
uses an HTTP `POST` to send content gathered in a JavaScript `Object` to a specified
URL.

Our focus this lesson will be to learning how to emulate an HTML `<form>` with
`fetch()`.

### Analyze Data Sent in an HTML Form

Let's take a look at a `<form>` (_see `sample_form.html` in this repo_):

```html
<form action="http://example.com/dog_logger" method="POST">
  <label> Dog Name: <input type="text" name="dogName" id="dogName" value="" /></label><br/>
  <label> Dog Breed: <input type="text" name="dogBreed" id="dogBreed" value="" /></label><br/>
  <input type="submit" name="submit" id="submit" value="Submit" />
</form>
```

The key components as far as sending data to the server are:

* The destination URL as defined in the `action` attribute of the `<form>` tag
* The HTTP verb to use as defined in the `method` attribute of the `<form>` tag
* The key / value data about the inputs in the fields `dogName` and `dogBreed`

We should expect that our "mini-browser," `fetch()` will need those same bits
of information in order to send data to the server. Let's place this data
inside our `form()` skeleton.

### Make POST Request using `fetch()`

The most basic `fetch()` call is _slightly_ more complicated than we've seen to this
point. It still takes a `String` representing the desintation URL as the first argument,
as always. But now we see that `fetch()` can also take a JavaScript `Object` (`{}`) as
the _second_ argument. This `Object` can be given certain [properties][p] with certain values
in order to change `fetch()`'s default behavior.

[p]: https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters

```js
fetch(destinationURL, configurationObject)
  .then(...)
  .then(...)
```

To emulate the HTML form field example above, we fill out `fetch()` likes so:

```js
fetch("http://example.com/dog_logger" , {
  method: "POST",
  body: {
    "dogName": "Byron",
    "dogBreed": "Poodle"
  }
})
  .then(...)
  .then(...)
```

Obviously, we don't have to define everything inside of one anonymous `Object`.
We could also write (they're exactly the same!):

```js
let formData = {
  "dogName": "Byron",
  "dogBreed": "Poodle"
};

let configObj = {
  method: "POST",
  body: formData
}

fetch("http://example.com/dog_logger" , configObj)
  .then(...)
  .then(...)
```

### Handling What Happens After

Just like when we use `fetch()` to retrieve (or, `GET`) data, we have to handle responses to
`fetch()` sending data with a series of calls to `then()` which are given
function _callbacks_.

Building on the previous implementation we might:

```js
fetch("http://example.com/dog_logger" , configObj)
  .then(function(response) {
    return response.text();
  })
  .then(function(content){
    if (response == "OK") {
      console.log("Added a new dog!");
    }
  })
```

### When Things Go Wrong: Using `catch()`

When something goes wrong in a `fetch()` request, JavaScript will look down the
chain of `.then()` calls for something very similar to a `then()` called a
`catch()`. When something goes wrong in a `fetch()`, the next `catch()` is
called so that error work can be performed. Let's imagine that someone makes a
typo and changes the URL to be: `"http://example.com/log_dogger"`  instead of
`dog_logger`. It's always a good idea to provide a `catch()` statement so that
JavaScript doesn't fail silently. The code should look something like this:

```js
fetch("http://example.com/log_dogger" , configObj)
  .then(function(response) {
    return response.text()
  })
  .then(function(content){
    if (response == "OK") {
      console.log("Added a new dog!")
    }
  })
  .catch(function(error){
    alert("Bad things! Ragnarők!");
    return "This is an error message. There are many like it, but this one is
for log_dogger";

  })
```

That's it!

## Challenge

In this lab, you need to satisfy the tests that will make sure your `fetch()`
skills are ready to go.

This lab refers to an API endpoint at `guestbook.example.com`. This is not a real
website. Instead the tests have configured a mocked version of `fetch()` that will
return a response for you. Run the tests to see it in action.

### Test 1

You need to write a `fetch()` requests that sends your first name as
`firstName` and `registryMessage` to an API located at
`http://guestbook.example.com/register`. If you perform the request correctly,
the server will respond with a JSON object that has a key called `message`. You
should use `then()` calls to extract and, in the last callback, return the
`message` value.

You should write this code inside the `registerSelf()` function.

### Test 2

To practice returning errors from `fetch()` you should write a `fetch()`
requests that sends your first name as `firstName` and `registryMessage` to an
API located at `http://guestbook.example.com/register-error`. This endpoint
does not exist! However, we can define a `.catch()` call that will run its
callback in the event that the server request fails (which it will). We should
return the string `":("`.

You should write this code inside the `errorSelf()` function.

## Conclusion

Congratulations! You can now use `fetch()`: the browser inside your browser's
JavaScript environment to both:

* READ data using HTTP GET  (whose response you can put into the DOM)
* SEND data using HTTP POST (whose response you can put into the DOM)

With this we're ready to to stitch server updates (reads **and** updates) with
DOM updating and event handling. We're almost ready to build the "Simple
Looker" from scratch!
