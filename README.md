## Privacy-centered contact tracing with purpose-built wearables
This repo is for describing an idea for a privacy-concerned contact tracing approach without the use of smartphones. A lot of this is opinion based, so assume half the sentences you read starts with "in my opinion".

Ever since Google and Apple announced they're releasing an API for contact tracing, I've been terrified of the idea. I won't point my reasons, but for a detailed explanation read it from EFF:

[The Challenge of Proximity Apps For COVID-19 Contact Tracing
](https://www.eff.org/deeplinks/2020/04/challenge-proximity-apps-covid-19-contact-tracing)

The problems that I have with such an API are pretty much based on one fundamental concept: *Consent*. This is beautifully explained in EFF's blog under the -surprise, surprise- *Consent* paragraph. The rest of the arguments can be built on that. Below are 3 core ideas that I will reference throughout this document.

**P: I should have the ability to pull the plug, whenever I want or whatever the global or personal conditions are**.

>It is our fundamental right to opt-in or opt-out of tracing. End of story. If I choose to opt-out, feel free to call me an asshole but that should be the extent of what "authorities" can do about it.

**C: My contact history should only be shared with my explicit consent in the case that I mark myself as "infected".**

>The implication of the above is that, any repository created to store traces shouldn't be usable for arbitrary tracing. And no one should be able to mark me as "infected".

**A: Such method should be accesible and inclusive.**
>Collective and individual health benefits should not require an expensive smartphone in your pocket, or being a citizen of a high-GDP country.

<br />

-----------

<br />

I will now try to describe a technological solution that is not only privacy centered but also:

1. does not employ smartphones
2. is cheaper to produce so nonsmartphone users can be reachable
3. is easy to pull the plug, easy to replace
4. is potentially decentralised

You may see some of these claims above are also each other's reasons for being so.

### Core idea
To be honest the core idea is pretty similar to the original contact tracing approach. I would like to think that we're closing the privacy gaps here. So here goes nothing:

What if, instead of using smartphones, we used dedicated wearables (bracelets, rings, watches) with tiny and cheap BLE/Wifi/LoRa/802.15.4 enabled microcontrollers for this purpose. 

The main problem I have with using smartphones is that once this solution is injected into our operating systems, there is effectively no taking it back<sup>1</sup>. But instead, if it was a cheap accessory that *my work, social and love life* was less dependent on, I would have zero trouble binning it (in accordance with *Idea P*). And this is where we start from.

Secondly, as EFF article also mentions, we do not take our smartphones everywhere. The reasons vary from joining private meetings to simply forgetting your phone at home to do groceries, or perhaps going on a bike ride without the weight of a phone. In comparison, an accessory like a bracelet or a ring would be much lighter, much less intrusive, much less forgettable. It could even be made waterproof so we can even go to the pool or the sea (maybe not, waterproofing for sea is hard).

Thirdly, Bluetooth is not a technology built for this purpose. Even though the advancements in BT tech has certainly increased its capabilities such as with BLE, it would be much naive to think that such technology could reach the necessary saturation ([60% by some estimates<sup>2<sup>](https://venturebeat.com/2020/04/21/european-scientists-and-researchers-raise-privacy-concerns-over-coronavirus-contact-tracing-apps/)) for the original approach to be effective. Hence, a purpose built device, with the right wireless technology could potentially be much more effective.

### Unique-but-rotating beacon IDs
I like the idea of rotating IDs, but even I was able to see the leak in that proposal. This brings in 2 problems; 

1. Once you release your IDs you effectively release all your association data (also mentioned in EFF article). 
2. The fact that any guessable algorithm can also be reverse-engineered by some pattern analysis, which has severe privacy implications (i.e. metadata analysis). 

    - Let me give you an example; I am currently in isolation with my brother and say we turned our contact tracing on. From my point of view, every 30 minutes or so, an ID would disappear and a new one would show up. Now, without even a proper analysis, one would be able to tie those IDs to each other. This creates a huge risk.

3. Smartphone apps or stationary scanners that has no/little relevance to the cause could also try and mine these IDs since we'll be broadcasting them. They should be obfuscated, secured and carry as little metadata as much as possible.

My theoretical approach to this problem (in accordance with *Idea C*) formed itself in this way:

**IDs should rotate in such a way that it should be near-impossible to tell ID rotation from incoming/outgoing contacts**

To realise that; I propose:

1. broadcasting multiple IDs from a single beacon
2. randomising the intervals for each ID rotation
3. randomly dropping existing broadcast IDs and appending new broadcast IDs

This would effectively make it much more difficult for a metadata analyser to tell if I sat home with my brother all day, or if I went out and did shopping. I will now admit that the average number of keys would still be minable to give an idea about how many people I've been in contact with. I am definitely open here to constructive critism *(I mean I'm open at every part of this doc, but here especially)*.

>This could also be impossible to implement with a smartphone since they can probably emit only 1 ID. So using a specialised embedded system for this has its side advantages.

### Self-infection notification

Assumption is that, these devices would only hold last 2 weeks of contacts, and locally. If a person is infected, they and only they should have the choice to share their contact history. Also with accordance to *Idea C*, there is no need to upload this data to a repository until then. My suggestion is that contact history would only be uploaded once a person marks themselves as infected.
Once the contact history is uploaded it would be done so without the source ID, limiting the amount of association. And in fact, this upload would simply be an append operation to a list of *"been in contact with an infected person"* with maybe a time/datestamp of contact <sup>3</sup>.

At this point all we have is a list of IDs that have *-potentially-* been in contact with an infected person. A list does not have the capacity to notify anyone since there is absolutely no contact information attached. So, at this point it falls onto the user to download the list of IDs periodically and locally check against their own IDs to see if they have been in contact. Or alternatively they can use a trusted proxy/provider to store their IDs to automatically check and be notified. But the important thing here is to use a proxy and which proxy to use is a choice<sup>4</sup>.

At this point, a user who now knows they've been in contact with an infected person also has a choice. Either mark themselves as "been in contact" *(maybe followed by an indicator that this is a multiple level contact)* or do nothing (in accordance with *Idea C*).


### Automating iterative consent on each "hop" *(with a tradeoff)*
I've already explained some of these to a couple people in IT sector. And one of their initial responses was *-apart from "why are you so paranoid"-* that a consent requirement on each "hop" could incapacitate the system rendering it ineffective, especially considering most people would give consent anyways. 

And I agree with them to an extent since it requires human interaction. My brother (he works in PR) also pointed out that most people would rather have a more autonomous system, then to download a list everyday and manually check against. So in this section I will offer amendments to my above proposal with a potential trade-off from privacy. But keeping the above option as a choice nevertheless, which is the important thing.

Above section mentioned that the contacted IDs would only be uploaded if the user marked themselves as "infected" or "been in contact". We will now give the users an option to "automatically upload contact history in case of contact".

The good thing about this approach is that, they can use their devices, or their trusted proxies to handle this option, or not use this option at all.

The bad thing about this approach is that the contact tracing would still require a collective upload action from edge devices to be carried out. On top of that, bad actors can make this an *opt-out* rather than *opt-in*. So transparency is at utmost importance when/if a proxy is used.

Now, when a user mark themselves as infected, the distributed system of wearables start tracing the contacts who have been in contact with the "infected" person. The system all-together "automagically" traces contacts, with a consent check at each user's device.

### Repository options

Ideally, there should be a decentralised, intamperable, single storage sharing the same API for the entire world (*right, I hate it but it already sounds like we're talking about blockchain*). If this can be done with a blockchain ledger or not is beyond my knowledge but here are my thoughts on this fictional repository and its requirements.

1. National repositories will not suffice as they will fail to track international contacts.
2. The repository should be decentralised and intamperable in case of bad actors trying to tamper with contact histories to either mimic good results or bad results for whatever reason.
3. For efficiency purposes, this repository should be differentially synchronisable, because not everyone has 8MBit internet access, let alone any internet access (in accordance with *Idea A*).
4. Bonus: repository should be accesible via *interesting options*: In countries with poorer internet access: GSM, SMS, AM/FM, amateur radio bands could be thought for distribution. Synchronisation hubs could be set-up in regions with poor or no internet access so synchronisation can be performed with physical access<sup>5</sup>.

### Challenges

Writing *poorly* from the comfort of my home is easy. Doing is something else. There are already many challenges that even I can spot right now. And I'd rather talk about them too, and offer answers if I can.

#### Technological

To be completely honest, technological challenges are probably the least of the worries. A lot of them are side-products of dumping smartphones for this approach. Here are 3 of them that I think will require serious thought:

 - Power/Battery efficiency: these devices will need to recharge. And unfortunately the task they'll be doing requires wireless connectivity, which is never an power-efficient task. The fact they're battery operated also puts an expiry date on them.
 - The wireless tech to be used should be able to create multiple beacons, and scan at the same time. It should also be able to address EFF's concerns about false positives and false negatives *(to an extent, this aint an ideal world but we work towards it)*.
 - Open source and embedded systems dont play well together as cross-compilation is not an easy task for general public. Problem is, any device that's manufactured and sold could've been injected with rogue code. These devices should be flashable by user if needed. And in fact, entire manufacturing process should be open-source and reproducable.

#### Financial

In an ideal world, these devices would be distributed to citizens free of charge. Some financial protections could be implemented like promises to return, replacements costs etc. The main problem would still be the initial production and distribution costs.

- First off, these devices need to be cheap enough that their benefits shall overcome the financial cost (*unfortunately, there IS a price for human life*). Not much to say here. This could be put into the technological challenges too.

 - This approach requires complete transparency in hardware, software and distribution. This unfortunately leaves very little margin for making profits, and hence even less incentive for companies/corporates to pick it up even the benefits will surpass costs<sup>6</sup>. So perhaps, governments *-where possible-* could institute incentives for companies/corporates to pick up this work.

 - At certain states, we can also forget about governments for funding or for incentives. Some of them would rather implement the easy option and reap the benefits of citizen-tracking as a bonus. In these countries unfortunately, this leaves the financial burden of production and distribution to non-profits and non-governments or worse individual users. 

So perhaps a non-global funding is not the way to go here. As proven, this is a globally shared problem and we can only come out of it united<sup>7</sup>. 

Perhaps a global funding needs to be set-up or tapped into for this vision to be realised. Perhaps this could be the task of an R&D branch of an entity that organises the "germ-games" Bill Gates mentions in his [now-infamous 2015 speech](https://www.ted.com/talks/bill_gates_the_next_outbreak_we_re_not_ready?language=en).


#### Distribution

Of course, the first question I've been challenged with was: 

**"There are already 3.5 billion smartphones distributed, ready to work and fight the good fight. How are you gonna produce and deliver these within time?"**

I've put that in bold, because it's indeed a valid question. Unfortunately my answer won't be that satisfying. Honestly, a vaccine could come faster than this approach. We might kill this thing off for good within this year. A lot of good things can happen. But I'd like to focus on contingencies, because plans fail.

In my humble opinion, the proposal here is a plan Z, one that we should also start working on right now. Because and most importantly; this may not be seasonal thing but a continuous wave of resurgence.

[We're not going back to normal](https://www.technologyreview.com/2020/03/17/905264/coronavirus-pandemic-social-distancing-18-months/)

So in 5 years time, this may be still going on and we may have accidentally handed our privacy to Apple, Google, our governments etc. And it may not even be working. And because why? We thought this was gonna go away? That we could pull the plug once this is over?

I'll be the pessimist: That's just wishful thinking layered with impossible chains of "*what if*"s.

Now, onto the actual problem of distribution then. Once the first version is ready, a unit's production cost should be as cheap as a cheap mp3 player. Because, it's like there isn't a place of the world those things cannot go to or be sold. Production would be picked up by companies, whose products are either to be bought by "authorities" or to be shipped and sent to be sold by distributors. 

Some of the distribution could be done via governmental postal entities *(some are already being used to deliver masks to general public)*, or some could be off-loaded to cargo companies and e-commerce sites.

In countries where postal infrastructure is less than ideal, the aforementioned *synchronisation hubs* should/could be set up to provide access to these devices. And in fact just like we do today with some services,  *"mobile synchronisation hub"*s could be used for delivery to less inhabited areas and for the synchronisation as well *(which would be less frequent than other places, but just by definition these places would be at lesser risk)*.

I will offer one middle-way solution to speeding up the distribution. The framework proposed here could be easily implemented to work within smartphones first<sup>8</sup>. And the only physical trade-off in doing so would be limiting ourselves to the Bluetooth technology. At the same time purpose-specific devices could be developed and produced to either replace smartphone apps, or work with them.

>I genuinely envision a world where we can pick these up from a small grocery shop if needed just like I can buy a flash drive from a cosmetics shop for no reason.

### Final remarks

Look, a lot of the stuff I talk about here, I'm not sure about their feasibility. This is merely a compilation of ideas, based on my personal engineering knowledge and experience, and most importantly written with good intentions. I can only claim to know one thing, the world is about to have a steep change and we need to adapt, together, for the better. And for what it's worth, I'm willing to put my share of the work needed for this goal (mostly system design and embedded coding!!). 

With steady progress starting now, we can have the tools to fight this virus (and the futures ones), with a united global approach and without handing our privacy to "authorities". I genuinely find that utopia that I mentioned in <sup>5</sup> is plausible and an OK place to live in. And in case you're wondering, we're already living in a what people would call an apocalyptic utopia 10 years ago.

<br />

-------

#### Footnotes
<sup>1</sup>[This is the research article](https://science.sciencemag.org/content/early/2020/04/09/science.abb6936/tab-article-info) I think is used as a reference, but honestly no number as 60% is actually mentioned. If anyone can find the where that number popped from I'd be glad, cuz there's multiple references to it from multiple publications (some of them sister publications).

<sup>2</sup>I dont have to take Google's, Apple's, my government's or *that app developer*'s word for it that it's been removed/deactivated.

<sup>3</sup>These appends can be done via trusted proxies/providers in a burst-write manner to limit the amount of metadata that can be mined by an append operation, like a TOR network approach.

<sup>4</sup>You can think of these proxies/providers analogous to online  cryptocoin-wallet services. You may choose to trust them, or you can use your own. For the majority of the public existence of multiple proxies/providers would make this system much easier to use and hence increase the efficiency of the complete approach.

<sup>5</sup>Slightly utopic but think of a world where you go to your corner shop, give them your bracelet so they can check for you if you've been in contact with an infected person. It's not that far-fetched if you ask me, especially considering the world we're living in right now.

<sup>6</sup>I dont think people at Google and Apple didnt think of the things I wrote here. No, I find it more plausible that someone up who cares more about profits told them to forget about it.

<sup>7</sup>Maybe we need a new messiah or an alien attack to unite, since a global pandemic with a disease that we have no cure or no vaccine is not enough yet? I mean come'on, this is right there with the rest of apocalypse movie tropes...

<sup>8</sup>There is a reason I did not offer this from the beginning. Any smartphone app would also be able to tie your personal information already residing in your smartphone to 1. your personal IDs, 2. IDs you've been in contact with.
