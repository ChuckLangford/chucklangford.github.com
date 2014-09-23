---
layout: post
title: Waiting for User Input
---

I love it when things are crazy simple and this is something I want to remember.

My current project has a form that can be filtered with user input via a textbox.  Pretty simple AJAX stuff, nothing too fancy.  Another developer outside the team even coded it for us so hurray for freebies.  He even went as far as implementing a pretty simple system to keep the input from overloading the server with search queries.

The problem is, it wasn't quite enough.  We noticed that just simply typing two or three characters resulted in a minimum of two queries sent to the server.  That's nothing too worrying, however, one of my designers was trying to implement animations on the search results to help the user understand that something is happening while they're typing and multiple searches were killing the animation's ability to keep up.  This resulted in a "janky" animation.

We decided to implement this code in order to make the search wait until the user stops typing:

	var keywait;
	$scope.searchChanged = function () {
		clearTimeout(keywait);
		keywait = setTimeout(function () {
			if ($scope.querying) {
				$scope.queryOnReturn = true;
				return;
			}

			getActivities();
		}, 240);
	};

The relevant bits here are the clearTimeout call and the setTimeout call.  The 240 millisecond timeout is completely based on how fast I type.

Happy Coding.