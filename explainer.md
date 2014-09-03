## What are Location-Based Triggers?

Location-Based Triggers (iBeacons, RFID, etc.) are all the rage these days. Article after article touts the use of iBeacons, and other LBTs, to activate devices and apps base on your current, granular location. Beacons, among other LBTs, are able to transmit small amounts of data to devices listening for triggers. A device or app listening for these triggers is able to show content based on the payload it recieves.

![What are LBTs? (beacons, RFID, etc.)](http://i.imgur.com/DO5bRrt.jpg)

Good News: LBTs are a great idea, and offer developers a ton of functionality they can use to create immersive, contextually relevant, user experiences.

Bad News: In my work with LBTs, it became immediately clear that platforms like Android and iOS implement these features in a way that is inherently high-friction, largely tied to apps, and fraught with disjoint, lacklustre UX paradigms.

Here is the current scheme platforms have concocted to provide LBT features (note: there are differences between them, but this is the general flow):

* Each app is required to listen for triggers _(usually via an SDK or Bluetooth LE APIs)_
* Triggers are used to emit simple pings with almost no payload _(think GPS)_
* Another user-defined layer is required to associate trigger pings with context and content
* Apps launch their own UI and content based on whatever triggers they recieve and choose to activate on

## Why did platforms implement LBTs this way?

The primary LBT issue platforms grapple with, is notification spam. If there are 100 beacons in a small area, all able to unilaterally activate your phone, you'd experience the mobile equivalent of endless browser popups - massive spam from all direction. To solve this in a quick and dirty way, platforms tied activation based on LBTs, to apps. Seems legit, right? You trust your apps, so why not let them act as your trusted agents?

The choice to use apps as the trust layer brings with it a host of other problems that equally degrade the value proposition of LBTs. Let's take a look at what happens when you go the app route:

* Every piece of LBT-driven content requires the user to have some app installed that is listening for it.
* Apps all set their own tolerances and parameters for trigger detection, often handled by a 3rd party SDK
* Large scale implementations involving diverse content (inside retail stores, supermarkets, etc.) are difficult because you need to either funnel all LBT activations through one app (that users may not have installed), or you are forced to manage a huge number of app partnerships.

## Is anyone trying to fix this?

The above mentioned issues are grabbing the attention of the right folks. Scott Jensen is one Googler who has put forth a model, the Physical Web - https://github.com/scottjenson/physical-web, that would remove the app barrier of current LBT platform implementations, and allow a device's platform/OS to directly open LBT content URLs. Both are great moves in the right direction.

There is one major caveat with Scott's Physical Web proposal: it eliminates *proactive, push notifications* from LBTs in order to block spam. As a result, the Physical Web model would require user to deal with scanning, evaluating, and choosing what to engage with in world around them. At scale, with beacons everywhere, the UX around content selection becomes an issue unto itself, as Scott notes in his proposal. This doesn't mean the model is bad, or unhelpful. In open, public areas (streets, parks, etc.), Scott's model is great. It allows explicit user access to content, while mitigating spam. Walking down the street, you could come across hundreds of beacons in a day, so you want a system that requires some explicit user action before allowing those beacons to display content on the user's device.

So is that the best we can do? There are times when the ability for beacons to proactively push content is far more desirable - is there no way to allow for this? I believe there is, and it takes shape as an additional system that compliments the Physical Web model.

## Geo-Origins to the rescue!

As I've noted above, losing the ability to do proactive, push notifications from LBTs is a significant blow to many use-cases. Consider the case of a retail store:

![Retail Store with Beacons](http://i.imgur.com/824MOY5.png)

You walk inside the store and are greeted with aisles of merchandise, separated by sections. The store may contain LBTs that offer specific content for each section and aisle, or even very short-range triggers that broadcast a few feet to provided item-level content. UX is obviously a concern here, because regardless of the system you choose, you don't want to spam users - but not spamming customers is in the best interest of the store anyway. You probably want a system that permits the store to broker proactive, LBT push notifications, so long as there's an ability to revoke that permission.

### IFrame : Web Page :: Place : World

To understand Geo-Origins, it will help to think of the physical world around you as a giant web page. Imagine all the stores in this web page are iframes. In a web page, all frames are tied to an origin; this is done to separate code and execution contexts. In an frame, you know that all the code and content within that window is subject to the web's same-origin security policy. It is possible to create a trust layer in the physical world using these same principles.

### Connecting the dots

Imagine beacons in stores as emitters of POST requests. Under the Geo-Origin model, beacons in an origin-tied location, like a Target store, can proactively push content to users as long as the content is coming from [target.com](http://www.target.com). This geo-origin policy is enforced by the User Agent - platforms like Android/iOS, or browsers like Firefox and Chrome. The funny thing, is that Google already has all the requisite pieces in place to enforce geo-origins - in fact, they're (kinda) already doing it without knowing:

![Google Maps Panel](http://i.imgur.com/TjZw9o3.png)

#####(Notice this SF location is already linked to the target.com website, as identified at the bottom of the screenshot)

### Linking physical locations to web origins

The one thing missing is a declaritive way to tell platforms like Google what locations your domain is tied to. I envision this being provided via a declaritive descriptor file, `entity.json`. This file would live in a predictable place, on the domain root, and be accessible to the User Agent. Here's a strawman for what it might look like:

```
{
  "name": "Target",
  "legal-name": "Target Corporation",
  "description": "Target is your preferred shopping destination - Expect More. Pay Less.®",
  "locations": [
    {
      "address": "5001 Junipero Serra Blvd, Colma, CA 94014",
      "geo-origin-policy": "*.target.com"
    }
  ]
}
```

### The User Experience

The first time you enter a location that is tied to a geo-origin and recieve a ping from a beacon or other form of LBT, you would be asked by the User Agent whether you wanted to allow content to be pushed to you by that location's linked entity. It would look something like the geolocation permission prompt you see on many web pages today.

Once you've opted in, your device will automatically listen for LBT triggers while within the bounds of that origin-tied location. Once a trigger has been recieved, the User Agent checks to ensure the content URL being passed to it adheres to the geo-origin policy specified by that location's entity.json. When the content URL has been screened by the UA, it is shown directly to the user without the need for any manual actions or app coordination!

Below is a mockup showing how LBT push content pings would be handled in the following 4 use-cases:

- User **has _not_ allowed** LBTs for the entity, the content the entity pushes is denied. (**red**)
- User **has allowed** LBTs for the entity, but the content the entity pushes is denied because the user is outside of the entity's geo-fenced bounds. (**blue**)
- User **has allowed** LBTs for the entity, but the content the entity pushes is denied because it points to a domain that is blocked by the `geo-origin-policy` value in its `entity.json` descriptor file.  (**orange**)
- User **has allowed** LBTs for the entity, and recieves the content the entity pushes because the user is within the geo-fenced bounds of the entity, and the domain is authorized by the `geo-origin-policy` value in its `entity.json` descriptor file.  (**green**)

![Mock of Technical User-Stories](http://i.imgur.com/1pRtgvO.png)

##### What about public spaces?

In open spaces like streets or parks, Scott's Physical Web model would be the one that controls user access to LBT content. This is because no one effectively "owns" the origin of an open, public place. Scott's model is incredibly useful when an owning domain cannot be associated with a location.

