---
layout: post
title: What Happens When a Shitty Coder Builds Your Backend
permalink: /article/what-happens-when-shitty-coder-builds-your-backend
category: Blog
tags:
    - tamper-chrome
    - security
    - react
    - laravel
    - json-server
---

For security reasons, the actual web services involved in this will not be named, so for the sake of this article I will invent a fictional online game store, called Theseus Games (TG) (https://theseus-games.co.uk).

* * *

I recently tried to buy a new game from TG which unfortunately was only available to Premium members (TG has two membership levels, Basic and Premium), and the Premium membership was expensive as hell. I bit my nails for a while and wondered what I could do since my future happiness seriously depended on playing several hours of said game as soon as possible. Then I realized, shit, I'm a software engineer and I decided to see if I could just play around with TG's services for a while and just see how things worked.

I reloaded the game page with my Chrome DevTools open in order to see what requests the website makes, just to 'educate' myself. First thing I noticed was that TG uses AJAX to load some of its Javascript assets, about a dozen in number. Two of these scripts caught my eye: _user.js_ and _purchase.js_, and I swiftly opened them up to see what treasures were buried within.

#### Snippet from _https://theseus-games.co.uk/assets/js/user.js_
    
```javascript
componentDidMount() {
    this.retrieveContactInformation();
    this.retrieveUserInfo();
}

retrieveUserInfo() {
    axios.get(this.appUrl + '/user').then(
        function (response) {

            this.setState({
                application: response.data.application,
                active: response.data.active,
                isLoaded: true
            });

        }.bind(this)
    );
}
```    

Of course, I had built several apps with React and React Native and I immediately realized that the web frontend was built with React, communicating with a Laravel backend (I got this from the session cookies). If you know a little Javascript (or pretty much any web language haha), you can work your way through these snippets.

#### Snippet from _https://theseus-games.co.uk/assets/js/purchase.js_
    
```javascript
purchaseGame = (evt) => {
    evt.preventDefault();
    if (this.state.status == 2 || this.state.status == 33) {
        this.setState({
            isLoaded: false
        });

        var newGame = {
            gameId: this.state.gameId,
            session: this.state.session
        };
        
        axios({
                method: 'post',
                url: this.appUrl + "/purchaseGame",
                data: newGame
            }
        ).then(
            function (response) {
                this.setState({
                    isLoaded: true
                });
            }.bind(this)
        );
    } else {
        this.setState({
            message: 'Sorry! This game is only available to our premium members.'
        });
    }
}
```    

From _purchase.js_ (line 3) and _user.js_ (line 12) I discovered that apparently user membership information were indicated through an active field in a **User** entity, and only users where active was either 2 or 33 could be allowed to get the game. Of course the numbers must represent membership levels, and either 2 or 33 indicated Premium membership, and since I was getting the message: 'Sorry! This game is only available to our premium members.', my active field most definitely wasn't 2 or 33.

Futher down the line in the DevTools request tab, I saw the actual **GET** _/user_ API request, and I found this response:

#### Response for GET _https://theseus-games.co.uk/application/user_
 
```json
{
    "application": "182718048",
    "active":22,
    "userId":60088
}
```    

At this point, I realized that the gates of heaven would need just a little prodding before they were opened wide for me and I thought, there should be something I can do to tamper with this baby.

Immediately, I fired up Postman in order to make API requests to update my active field. From DevTools, I copied out my current session cookie and the '**X-XSRF-TOKEN**' header value as well into Postman, and I tried to make a **PUT** request to __ with payload:

#### Payload to PUT _https://theseus-games.co.uk/application/user_
    
```json
{
    "application": "182718048",
    "active":2,
    "userId":60088
}
```

But I immediately received a 405 (Method Not Allowed) error. Apparently, users are updated in a different route. In turn, I tried **POST** _/application/updateUser_, **POST** _/application/updateUserInfo_ and got 404s for both. At this point, I decided to abandon this approach entirely and try something else. (If I wanted to continue, I'd have inspected the other *.js files that were downloaded via AJAX to get the specific route for updating user, or just go to my account page and try to update my account, then retrieve the route; but there are many ways to skin an elephant haha.)

I have this cool Chrome extension called Tamper Chrome which is used to (surprise!) tamper with requests in the browser, and modify request headers, payloads, etc. The next approach I tried involved me tampering with the actual API requests, and there were two things I could easily try:

1\. Download a copy of __, and make no changes except in the if condition checking the active field and then place it in my local server (e.g. _/var/www/html/web/purchase.js_), then modify the outgoing request to __ and change the request path to __, so it serves my local version instead.  
For my local modification, I could simply change:
    
```javascript
if (this.state.status == 2 || this.state.status == 33) {...}
```
to:
    
```javascript
if (true) {...}
```

2\. In a similar manner to (1), I could serve a local version of __ and simply return a user object with the active field conveniently modified, then change the request path to my local version. I mean something like:

#### Response for GET _http://localhost/api/user.json_
    
```json
{
    "application": "182718048",
    "active":2,
    "userId":60088
}
```    

I opted for (2) and did this with the beautiful json-server node package. I created a simple _test.json_ file with contents:` `
    
```json
{
    "user": {
        "application":"182718048",
        "active":2,
        "userId":60088
    }
}
```    

Then I started json-server:

![Start Json-Server][1]  
_Started json-server_

  
Next, I activated Tamper Chrome in order to reroute all further requests from current tab:

![Activated Tamper Chrome][2]  
_Activated Tamper Chrome_

  
And I reloaded the page. Of course, the AJAX request for __ soon came up, and  I simply modified that to my local json-server:

![Tampering with Tamper Chrome][3]  
_Tampering with Tamper Chrome_

  
Of course, the request hit my json-server which happily responded with a few sweet bytes.

After this request, I decided to click the Purchase button again, and of course, all the frontend could see was that I'm a privileged TG member (haha!); there was a loading indicator and next thing, a popup thanking me for using Theseus Games (_My Pleasure, folks_), and the game was rapidly downloaded to my computer.

I swiftly abandoned all earthly concerns and dedicated the next few hours of my life to the game, and when I was mentally and psychologically exhausted, I lay on my bed in solemn contemplation of just what the unfortunate folks at TG could have done to further protect their wares.

The **Golden Rule** here, ladies and gentlemen, is to always do both a client-side and a server-side check of all sensitive components of your web applications. In TG's case, on the server side, at _****_, they could have simply verified my _**active**_ field again, to ensure that I was authorized to purchase said game, which is actually a trivial check to make. Unfortunately, this is a mistake several web developers (newbies _and even old-timers_) still make when building web services, and frequently fail to tighten up their security. Crafting secure, high-performance web services takes a bit of craftsmanship so if you own an IT company or build web services, please pay a little bit more attention in the future.

Thanks for reading folks. Much love.

* * *

Fortunately, the many clients that I've worked with over the years can rest easy because their applications are well protected on several levels from this kind of stuff. So do the smart thing and Hire Me! Let me help build and improve on your various frontend and backend web applications using various technologies (**React**, **Angular**, **Laravel**, **Symfony**, **Drupal**, **.NET Core** etc).

[1]: /assets/img/Screenshot%20from%202018-07-27%2018-53-33.png
[2]: /assets/img/Screenshot%20from%202018-07-27%2018-56-36.png
[3]: /assets/img/Screenshot%20from%202018-07-27%2018-58-32.png
