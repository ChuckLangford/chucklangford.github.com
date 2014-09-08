---
layout: post
title: Introduction to Protractor
---
I'm currently working on a project wherein my team has received code from another company.  Ultimately, we're working to replace the legacy code but in the meantime we still have to work with and support it.

The project started off well enough, however, a few weeks in we discovered that our inherited code base is a beast, and it bites.  More than once, one of the programmers would be working on a feature, meet the acceptance criteria, turn in his work and then discover that his work created a new bug.  After being frustrated one too many times, we decided end to end testing was in order.  Enter Protractor.

### Where to Start

For anyone that doesn't know, Protractor is an end to end (e2e) testing solution specificly tuned to Angular applications.  It uses Selenium, and Jasmine as it's two primary technologies and allows you to automate launching a browser, going to a specific webpage, and programmatically interacting with items on the page.  Then you can tell Protractor "Ok Protractor, after all the interaction is done, you should see item x display content y."  It's super handy.

To get started, you really only need to go here: [https://github.com/angular/protractor/blob/master/docs/tutorial.md](https://github.com/angular/protractor/blob/master/docs/tutorial.md)

The tutorial is clear and straightforward and applying Protractor to your Angular application is incredibly intuitive.  We had a login/logout test running in about 5 minutes.

Happy Coding.