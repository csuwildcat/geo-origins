Geo-Origins Explainer
===========

## What are Location-Based Triggers?

Location-Based Triggers (iBeacons, RFID, etc.) are all the rage these days. Article after article touts the use of iBeacons, and other LBTs, to activate devices and apps base on your current, granular location. 

Good News: LBTs are a great idea, and offer developers a ton of functionality, and allow for the creation of immersive, contextually relevant, user experiences.

Bad News: In my work with LTBs, it became immediately clear platforms like Android and iOS implement these features and APIs based on a system that is inherently high-friction, largely tied to apps, and fraught with disjoint, lacklustre UX paradigms.

Here is the current scheme platforms have concocted to provide LBT features (note: there are differences between them, but this is the general flow):

* Each app is required to listen for triggers _(usually via an SDK or Bluetooth LE APIs)_
* Triggers are used to emit simple pings with almost no payload _(think GPS)_
* Another user-defined layer is required to associate trigger pings with meta data and context
* Apps launch their own UI based on whatever triggers they recieve and choose to activate on

## Why did platforms implement LBTs this way?

The primary LBT issue platforms grapple with, is notification spam. If there are 100 beacons in a small area, all able to unilaterally activate your phone, you'd experience the mobile platform equivalent of endless browser popups - massive spam from all direction. To solve this in a quick and dirty way, platforms tied activation based on LBTs, to apps. Seems legit, right? You trust your apps, so why not let them act as your trusted agents?

The choice to use apps as the trust layer brings with it a host of other problems that equally degrade the value proposition of LBTs. Let's take a look at what happens when you go the app route:

* Every piece of LBT-driven content requires the user to have some app installed that is listening for it.
* Apps all set their own tolerances and parameters for trigger detection, often handled by a 3rd party SDK
* Large scale implementations involving diverse content (inside retail stores, supermarkets, etc.) are difficult because you need to either funnel all LBT activations through one app (that users may not have installed), or you are forced to manage a huge number of app partnerships.

## Is anyone trying to fix this?

The above mentioned issues are grabbing the attention of the right folks. Scott Jensen is one Googler who has put forth a model, the Physical Web - https://github.com/scottjenson/physical-web, that would remove the app barrier of current LBT platform implementations, and allow a device's platform/OS to directly open URLs it receives from triggers. Both are great moves in the right direction. There is one major caveat with Scott's proposal: it forces the user to constantly deal with scanning, evaluating, and choosing what to engage with in world around them. The Physical Web enforces a model that allows for *no proactive notifications*. 

In open, public areas (streets, parks, etc.), Scott's model is really helpful. It allows explicit user access to content, while mitigating spam. Walking down the street, you could come across hundreds of beacons in a day, so you want a system that requires some explicit user action before allowing those beacons to display content on your device.

So is that the best we can do? There are times when the ability for beacons to proactively push content is far more desirable - is there no way to allow for this? I believe there is.

## Geo-Origins to the rescue!

