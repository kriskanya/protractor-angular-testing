# Protractor: Selectors

### General Tips:
* Be sure to check out the Protractor [API Docs](http://angular.github.io/protractor/#/api).

* Definitely load up Protractor's *element explorer* to help write selectors.  
  1. Start Selenium standalone server
  2. Start protractor: *./protractor/bin/elementexplorer.js*


* For some basic tips on CSS selectors, go to [Sauce Labs](https://saucelabs.com/resources/selenium/css-selectors)

* If you want to grab the current element which is being inspected, type `$0` in the console.

The following are CoffeeScript Selenium selectors, which may be useful mainly to those
writing end-to-end tests using Protractor.

### By.xpath:

How to test out an xpath selector in the *Chrome console*:

`$x('//select[contains(@class,"filter-item")]’)`

The following uses xpath to find a **select** with class "filter-item", and then finds
an **option** element nested underneath it with text "Overdue".  The text() method
is an extremely useful part of xpath, and is not present if you use css.

`element(By.xpath('//select[contains(@class, "filter-item")]/option[text()="Overdue"]'))`

### By.repeater

The following selector finds all iterative angular elements named "provider in providers"
(where provider is just a variable name for each item in providers), and *get(0)*
returns the first item in the array.  It then finds an element with class "button" with
text "Select".  Note that you should use **element.all** for By.repeater to work properly.

`element.all(By.repeater("provider in providers")).get(0).element(By.cssContainingText('.button', 'Select'))`

### .getAttribute()

The following grabs the element's **value** by using the *By.model* selector; *.getAttribute* returns a promise, which we need to stick into the By.css("option"...) selector
so that we can select the entire element and call **getText()** with Protractor.

###### HTML:
```
<input ng-model="patient.insuredDob">
  <option value="0">Assurant Health</option>
</input>
```

###### CoffeeScript:
```
element(By.model("patient.insuranceCompanyId")).getAttribute('value').then (value) ->
  return element(By.css("option[value~=\"#{value}\"]")).getText()
```
