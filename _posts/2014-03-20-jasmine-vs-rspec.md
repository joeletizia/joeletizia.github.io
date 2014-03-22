---
layout: post
title:  "Jasmine vs RSpec Cheatsheet"
date:   2014-03-20 09:00:00
categories: jekyll update
---
Jasmine and RSpec are both test frameworks for JavaScript and Ruby respectively. While working on a web project in a test driven environment, you're sure to run into both a lot; RSpec more so if you work on primarilly back end code, but a full stack developer is expected to know both.

Jasmine and RSpec have different ways of accomplishing essentially the same things, particularly mocks and stubs. This article is a simple example of those differences.

## RSpec

RSpec accomplishes stubbing and mocking via two different but related methods, `#stub` and `#double`. 

`#double` is used to create a mock object. This mock can be used as a directly passed collaborator to a method you are testing, or you can have this double be returned by a collaborator within the method you are testing. 

A double is typically created in the following fashion

{% highlight ruby %}
describe ClassUnderTest do
  describe "#method_to_test" do
    let(:mock_object) { double(:mock_object) }

    it "this is my test" do
      ...
    end
  end
end
{% endhighlight %} 

`#stub` is used to override the behavior of a method by replacing its definition and simply returning a predetermined value. This is useful when you want to simply assert that a method is called on a collaborator, or to have that collaborator return a planned value. 

Generally, you can stub methods on mocks in order to guide your code under test down a specific path in order to test that it works properly. 

`#stub` is used in the following fashion.

{% highlight ruby %}
describe ClassUnderTest do
  describe "#method_to_test" do
    let(:mock_object) { double(:mock_object) }

    before do 
      mock_object.stub(:method_1).and_return(5)
    end

    it "this is my test" do
      mock_object.method_1.should == 5
    end
  end
end
{% endhighlight %} 

This can also be accomplished via:

{% highlight ruby %}
describe ClassUnderTest do
  describe "#method_to_test" do
    let(:mock_object) { double(:mock_object, method_1: 5) }

    it "this is my test" do
      mock_object.method_1.should == 5
    end
  end
end
{% endhighlight %} 

The second example sets the return value of `method_1` on `mock_object` to 5. Both the second example and the third example are relatively the same. It is a matter of preference. 

As well as stubbing return values from methods on mocked collaborators, you can assert that a particular method was called on the double in the following way.

{% highlight ruby %}
describe ClassUnderTest do
  describe "#method_to_test" do
    let(:mock_object) { double(:mock_object) }

    it "this is my test" do
      mock_object.should_receive(:method_1)
      ClassUnderTest.new.method_to_test
    end
  end
end
{% endhighlight %} 

One thing to note, the `#should_receive` method creates an implicit stub on the method. If this method is tested more than once, you would be well advised to do the following:

{% highlight ruby %}
describe ClassUnderTest do
  describe "#method_to_test" do
    let(:mock_object) { double(:mock_object) }

    before do
      mock_object.stub(:method_1)
    end

    it "this is my test" do
      ClassUnderTest.new.method_to_test
      mock_object.should have_received(:method_1)
    end
  end
end
{% endhighlight %} 

Another nice scenario for stubbing is to stub conditional values to direct logic flow in your method under test. Take the following method for example:

{% highlight powershell %}
class ClassUnderTest
  def initialize(checker)
    @checker = checker
  end

  def method_to_test
    if checker.do_something
      "it was truthy"
    else
      "it was falsy"
    end
  end
end

describe ClassUnderTest do
  describe "#method_to_test" do
    let(:checker) { double(:checker, do_something: do_something_value}

    context "when the checker returns true" do
      let(:do_something_value) { true }

      it "returns 'it was truthy'" do
        ClassUnderTest.new(checker).method_to_test.should == "it was truthy"
      end
    end

    context "when the checker returns false" do
      let(:do_something_value) { false }

      it "returns 'it was falsy'" do
        ClassUnderTest.new(checker).method_to_test.should == "it was falsy"
      end
    end
  end
end
{% endhighlight %} 

Since we have stubed `checker.do_something` to return true or false in different contexts, we can verify the return value of the method `method_to_test`.

## Jasmine

Jasmine accomplishes stubbing and mocking via similar means. The typical stub method in Jasmine is `spyOn`. `spyOn` can be called on any object to stub out methods on that object. The stubbed method will not execute the implementation code, which is one of the main points of stubbing. In the case below, an alert will not be raised since the method is stubbed.

{% highlight ruby %}
describe("object-under-test", function() { 
  var spy, object;

  beforeEach(function(){
    object = {
      methodToTest: function(){ alert("hello"); }
    };

    spy = spyOn(object, "alert");
  });

  it("this is a test", function(){
    object.alert();
    expect(spy).toHaveBeenCalled();
  });
});
{% endhighlight %} 


In order to create a mock, analogous to a `double` in RSpec, one would do the following:

{% highlight ruby %}
describe("class-under-test", function() { 
  var mock, object;

  beforeEach(function(){
    object = {
      methodToTest: function(collaborator){ collaborator.methodToWatch("hello"); }
    };

    mock = jasmine.createSpyObj("nameOfSpy", ["methodToWatch"]);
  });

  it("this is a test", function(){
    object.methodToTest(mock);
    expect(mock.methodToWatch).toHaveBeenCalledWith("hello");
  });
});
{% endhighlight %} 


This is similar and different from RSpec in a few ways.

* Similarities
  * The method being stubbed does not call through to the original declaration of the method. 
  * Injecting a mock into an object under test in both frameworks allows you to assert that a method was called on that mock.

* Differences
  * In Jasmine, `spyOn` is called independent of the method you are stubbing, not directly on the object like in RSpec.
  * The expectation `toHaveBeenCalled` is checked after the invocation of the method. This has since been added in RSpec but legacy RSpecs are full of expectations prior to invocation of the method under test.


