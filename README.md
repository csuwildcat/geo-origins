geo-origins
===========

Removing the friction from location-based triggering with origin-bound geofencing


## What are Location-Based Triggers?

Location-Based Triggers (iBeacons, RFID, etc.) are all the rage these days. Article after article touts the use of iBeacons, and other LBTs, to activate devices and apps base on your current, granular location. 

Good News: LBTs are a great idea, and offer user and developers a ton of functionality, and allow for the creation of immersive, contextually relevant user experiences.

Bad News: In my work with LTBs, it became immediately clear platforms like Android and iOS implement these features and APIs based on a system that is herently high-friction, largely tied to apps, and fraught with disjoint, lack-luster UX paradigms.

Here's the current platform paradigms for LBTs (note: models differ a bit, this is the general flow):

* Each app is required to listen for triggers _(usually via an SDK or Bluetooth LE APIs)_
* Triggers are used as devices that emit simple pings with almost no payload )(think GPS)_
* Usually third-party layer is required to associate trigger pings with meta data and context
* 

## Why did they implement LBTs this way?

I suspect the primary issue with LBTs, is spam. If there are 100 beacons in a small area, all activating your phone, you'd have the mobile platform equivalent of browser popups - obtrusive spam from all directions! To solve this in a quick and dirty way, platforms tied the ability to listen for and activate content based on LBTs to apps. Seems legit, right? You trust your apps, so why not let them act as trusted agents?

## Is anyone trying to fix this?

The above mentioned issues do seem to be something folks at places Google are aware of. Scott Jensen is one Googler who has put forth a model, the Physical Web - https://github.com/scottjenson/physical-web, that would remove the app barrier of current LBT platform implementations, and allow device platforms to directly open URLs they recieve from triggers. Both are great moves in the right direction. There is one major caveat with Scott's proposal: it forces the user to constantly deal with scanning, evaluating, and choosing what to engage  world around them to get notifications from triggers

## Geo-Origins to the rescue!

