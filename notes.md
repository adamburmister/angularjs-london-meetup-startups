The following is my sketch of speaking points for the talk... more a stream of consciousness than readable text.

*Introduction: Who am I?*
- Hi, I'm Adam
- Front end engineer, developer, generalist
- Ex-BBC, Ex-Xero, 3 years of own startups
- I'm presenting today not because I'm an expert, but because I love Angular, and really enjoy these meetups
- Here are my contact details. I'm probably not sticking around after this talk as I have to be up at 4am to get to Berlin, so feel free to drop me a line.
- I like questions, so if you would like to ask something during this please do. I'll offer my opinion and them maybe we can pull in the other guys too to correct me.

*Seneca.io*
The content I'll be covering today are a series of small learnings I've made over the development of what is becoming a reasonably sizable code base.

Seneca is an early-stage portfolio monitoring startup. At a high level we streamline portfolio companies reporting to their investors.
We're a team of 4, working out of London and Berlin.

I'm talking today so that you can take what you like, and so we can discuss it, build upon it, and maintain a collective momentum which we can all benefit from.

*My proviso*
I do not claim to be the greatest, or even a good, engineer, tester, designer, product manager in the world - so I'll leave it to you to make your own choices, and in fact, I'll even be pointing out some of the things I've already done wrong.

What I do have is experience of working with AngularJS in the trenches over the past couple of months, so that's what I'll be talking about today: AngularJS within a startup environment.

So, my topic today is working with AngularJS in startups. I also love startups.

Startup's, under my definition, are things that take risk, unknowns, and hypothesis to solve and manage them, and convert them into known entities.

We take a hypothesis, come up with a solution, iterate developmental designs on those, and produce a final product, and test the result.

Of course, one of the big risks of startups is just not having enough runway to get to the testing-it-out point... so our technical decisions are largely based on one thing:

[Q: Which is what?]

*Speed*
Speeeeeeeeed.

Startup CTOs are the Jeremey Clarksons of the software world.

Speed is one area where I think Angular is a great fit for startups.

My background is design and HCI, so we're strong believers in a design-lead development process.
We've found that we can develop with a 1 day lag time: designs from yesterday are implementable today. This is an amazing help to sketching out ideas and evaluating them quickly. Nothing shows flaws like real-working interfaces.

[DEMO]

So, it is *redonkulously* fast to get stuff written.
JS is usually pretty quick to write anyhow, even if you're from a jquery background, but I've found development of complex behaviour in AngularJS to be far far quicker.

There are a couple reasons for this, from my experience:
1. *Dependency injection* makes it stupidly easy to get up and writing complex code quicker.
2. *2 way data binding*, without having to wrap JSON objects in model class instances, makes maintaining state stupidly easy... this solves a lot of flow control issues within an interface
3. The *toolset* around AngularJS is good. Things are still in flux, and I have found testing to be more painful than I'd have liked so far, but they're getting better.

Here's my suggestion for a toolkit for getting you started.

*Yeoman*
A lot of you probably already use this.

Use yeoman to get started quickly.

They've recently upgrades to version 1, and there are a good number of breaking changes, so if you haven't looked at it in a while I suggest doing so.

Yo makes starting new projects easy. Bower makes managing 3rd party libraries relatively painless.

*Angular-ui and Angular-bootstrap*
You'll notice here that we've also included the angular ui library.
There's a good bit of reusable code there, as well as in the angular-bootstrap project. You probably already know Bootstrap, so it gives you a really quick path to implementing HTML-wireframes.

With all those in place, you're is a really good position to get going.

*What's missing*

A whole bunch of framework helpers

I'd like to see inheritence done like ExtJS, since we're already using pseudo namespaces.

I don't feel there's enough convention over layout of apps yet. I've seen quite a few... how do we split things up? Is it by widget, functionality? Do we store views and css and resources and controllers and models all in the same folder? Do we split them up? Does that hurt reuse? Can we even really re-use code like that?

The community has a bunch of great stuff, but it's still spread all out over the place, a lot of it in plunkr/gist type services. Which means you have to know who to ask... the IRC channel is a good place to ask questions.

My prediction is there's another layer of abstraction still to come. It'll be more like the Rails of AngularJS; a framework for building single page apps, and heavily opinionated. I even have the name: "Parallels"

*Architecture*

**Constants**
Combined with Dependency Injection constants are great - we can simply pass around a config object - I'm using this alot for enums, referring to nodes within a Settings JSON object, rather than hard coding strings. This gives me flexability to rename things and I finalise my sketched designs, and makes refactoring easier.

**Global scope var**
Initially adding a single model to my root scope made complete sense. Angular, is through it's design, encouraging of this "global" variable appraoch, as your childscopes can inherit this, or you can access it from root scope.
In retrospect this is something that doesn't sit right. I'm going to probably refactor this into a service... it's possibly better for testing purposes, but also other reasons I will explain why soon.

**Filters**
They're really cleaned up my code. Initially I started adding methods to controllers or ngResources which did manipulations of the models for view displays. Then I started diving into filters, and found them to be a real godsend for cleaning up views, making things reusable.
Show percentageOf, roleDisplay, money filters.


*Gotcha*
**ng-resource**
  ***Intro***
  I see this all the time... don't forget to add it to your page :)

  Next most common one: why's this not updating?

  ***URLs***

  ***Resource indexes***
  I found this one confusing. Imagine I have a resource, which exists on the following URL

  It is also worth knowing how this stuff works: the result of the get call will be a blank reference initially. After it's loaded, the promise will update the reference to the correct value.
  But it isn't returning a chainable promise, so you can't .success / .error the method, instead you have to pass methods as second and third params... which is how you can do error handling.
  Personally, and against the recommendations within the docs, I always pass callbacks - so I can disable isProcessing state variables and handle errors.

  ***Final thoughts***
    Is this really the best approach?
    Doesn't this just take away the advantages of AngularJS working with plain JSON objects?

    As my models are getting more complex, and nested, I'm having to marshal more and more of those child nodes into resource instances so I can access their custom functions. This sucks.

    Also: if I need to refresh my core model, like I do after some actions, I want to call the currentUser.$get method, which will reload the resource from the server, but it will also replace the currentUser scope var with the JSON doc, not the marshalled version. Fuck. I've just lost custom methods on all child elements.

    I <em>think</em> I probably want to be able to call methods without dealing with marshalling. That means a DAL-service approach, or maybe if there was a way to intercept HTTP event callbacks before they're passed back.

*ng-include*
  ng-include is awesome - partials! loaded on demand

  Combine with a switch to load only the stuff you need, easily, and cleanly.

  You'll want to use an interceptor though to show that the page is loading views in the bg. (show example)

  Also: note the trap: They create a new scope, so you cannot set a scope variable and have it reflect up to your parent controller.
  You'll have to prefix it with $parent to access the parent scope.
  $parent.model.field 

*Filters*
I actually love filters. They've made my views far more readable, are quick to write and easy to test.
If you're repeating yourself in your views, I'd ask "can I make this a filter".
Examples: truncate, roles, money.
These should be really sharable between projects. I'd like to see a good collection of these built up.

*Constants*
One of my first thoughts was how to implement development environment configuration, much like you'd have in Rails.
Here's how I've done it... [code walk through]

*GLobals*
Globals, as we know, are evil. But I'm a bad ass.
There's a strong temptation to use a global object with AngularJS because of the scope inheritence.

*Directives*
Not as useful as I thought, yet. I thought I'd be using them for everything, but only really using a few so far.
My suggestion is to start without them, move pain-points into directives as you reach them.



---


show the route.secure and .title stuff

page title manager

money filter

why sn-login directive is the wrong way to go

using events instead of singletons

---

return objects from modals - handle models within the caller
cherry picking - added benefit for editing
callbacks on resources, isProcessing

ng-include is awesome
$parent.model.field

Checkboxes:
{
  "key": true/false
}
