# Checking the Url Without Angular

##### browser.getLocationAbs() is failing in IE8, so we need to write a method that checks the url with JavaScript and then successfully returns the promise or returns a failure

* browser.getLocationAbs() was originally being used to check that the correct url was navigated to after user logout:

##### logout method:
```coffeescript
global.logout = ->
  browser.ignoreSynchronization = true

  # Because IE runs in series, the local storage must be cleared before the spec runs
  # if browser.browserName is 'internet explorer'
  if browser.browserName.match(/ie/)
    browser.executeScript('window.sessionStorage.clear();')
    browser.executeScript('window.localStorage.clear();')

  browser.get "/apps/search/#/logout"

  fn = ->
    get_url()
    .then (url) ->
      return new RegExp('apps/login').test(url)

  promise = browser.wait(fn, 20000)
  .then ->
    browser.ignoreSynchronization = false
  , ->
    browser.ignoreSynchronization = false

  return promise
```

##### as it appears in the original (failing) test:

```coffeescript
it "should logout", ->
    logout()
    expect(browser.getLocationAbsUrl()).toContain "/apps/search/#/dashboard/recent"
```

* This fails `expect(browser.getLocationAbsUrl()).toContain "/apps/search/#/dashboard/recent"` because after logout(), Angular is not found on the page quickly enough.  The solution is to write a url-checking function using plain JavaScript:

```coffeescript
global.get_url = ->
  return browser.executeScript("return window.location.href;")

global.check_url = (regex) ->
fn = ->
  get_url()
  .then (url) ->
    return regex.test(url)

# if promise is returned but regex does not match, display an error
browser.wait(fn, 3000).then undefined, ->
  protractor.promise.rejected(new Error("Failed to match #{regex}"))
```

* The promise will return every time, so we need to use the Webdriver API to check the promise instance that's being returned against a regex.

##### here is the new test:

```coffeescript
it "should logout", ->
  logout()
  check_url(new RegExp('apps/login/#/'))
```
