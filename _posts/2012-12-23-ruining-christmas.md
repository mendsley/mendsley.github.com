---
layout: post
title: "Amazon Game Connect or: How I Almost Ruined Christmas"
image: /images/ruiningchristmas/calendar.png
icon: matt
location: Seattle
---

It's a quiet Sunday evening, and I'm using the time to perform a lengthy
overhaul of [AirMech's](http://airmech.com) many chat systems. Why there is more
than one chat system is a topic for another day. Right now I'm going through the
process of unifying them so things like, ummm, color of player names, are
consistent in the party chat window, regardless of whether you're in the
matchmaking queue or just hanging out with friends. Seriously, do you know how
often people ask why color disappears/changes/bugs-out when they enter the
queue?

Anyway, chat is one of those systems I have the privilege of owning. And the
idiosyncrasies it's accumlated as I piled layer apon layer of cruft -- read
features -- have been driving me nuts.

Quirky girlfriend? Awesome.
Quirky chat system? Rage and flip desks.

So I set aside a nice Sunday evening to wrangle the mess.

I'm refactoring away -- "in the zone" -- with everything going great until this
little innocent guy shows up on our internal [Amazon Game
Connect](http://www.amazon.com/gp/feature.html?ie=UTF8&docId=1000816301) (AGC)
thread...

![Amazon user error?!](/images/ruiningchristmas/usererror.png)

Earlier this morning... after *extensive* testing, we had finally
[launched](http://www.amazon.com/Carbon-Games-AirMech-Game-Connect/dp/B00AQ7YCEC/)
AirMech on AGC. Based strictly on the wording of the email, my first thought is:
"Ugh, I don't want to spend my night on this. I'm **not** in a customer support
mood." Unfortunately, when you're part of a company with less than 10 people,
sometimes you **have** to be in a customer support mood.  And somtimes you have
to be the one that looks through piles of monkey poop to figure out what your
server code ate for breakfast.

Also, billing happens to be *another* one of those systems I own.

A year and a half of AirMech triage instincts kick in... SSH into the billing
server and spelunk....errr...grep through the logs. At least I had the sense
when building this to tag everything related to AGC with something searchable.
I'm expecting to find somethign about how this n00b can't find the power button
on his mache. The results, however, are a bit unsettling:

<pre><code class="long">2012/12/23 08:32:38 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 08:33:01 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: FAIL_ALREADY_OWNED
2012/12/23 09:15:05 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 09:15:55 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 09:16:41 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 09:22:11 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 09:22:48 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 09:26:06 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: FAIL_ALREADY_OWNED
2012/12/23 09:26:59 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 09:30:20 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 09:33:08 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: FAIL_ALREADY_OWNED
2012/12/23 09:43:05 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 09:44:07 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 09:45:19 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: FAIL_ALREADY_OWNED
2012/12/23 10:01:10 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 10:16:32 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 10:17:46 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 10:18:54 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 10:20:06 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 10:23:02 Linking 7*************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
2012/12/23 10:24:11 AGC purchase request da39a3ee5e6b4b0d3255bfef95601890afd80709 GiftPackSmall: OK
2012/12/23 10:31:56 Linking *************** to AGC account da39a3ee5e6b4b0d3255bfef95601890afd80709
</code></pre>

Amazon needs an identifier for the account, so when a link request is received I
generate an account token for amazon to use as a reference. Instead of sending
the account's email address, I send the SHA-1 of that email. I then store a
mapping of the SHA-1 hash back to the email address. This way I don't send
personal account information off to a third party.

Back to the log file.

So, those `Linking` entries ... they're supposed to show the account email
(redacted) along with the SHA-1 token I generate. Unfortunately every account is
generating the same token. But they're generated from the SHA-1 of the email.
They **CAN'T** be the **SAME**.

# HOW ARE THEY ALL THE SAME?!?!???!!!??!!!1111

![f7u12](/images/ruiningchristmas/f7u12.png)

Wait.... That number.... `da39a3ee5e6b...` Yeah, that one. It looks so familiar... Where have I
seen it before?

![f7u12](/images/ruiningchristmas/thinking.png)

	$ echo -n "" | sha1sum
	da39a3ee5e6b4b0d3255bfef95601890afd80709  -

Oh.

# OH.

<div class="syntax">
<pre><code class="long"> <span class="gu">@@ -19,7 +20,7 @@ func registerEmail(email string) (token string, err error) {</span>
        email = strings.ToLower(email)

        hash := sha1.New()
<span class="gd">-       hash.Sum([]byte(email))</span>
<span class="gi">+       hash.Write([]byte(email))</span>
        token = hex.EncodeToString(hash.Sum(nil))
</code></pre>
</div>

I guess it helps to actually write the email address to the SHA-1 hash. Maybe it
also helps to test on something other than a handful of test accounts, one at a
time in isolation, before shipping too.

Also, adding token generation to the test cases instead of just assuming it was
rock solid on acount of it being mind-numbingly simple is probably a good idea
too.

/facepalm

---

Now that the bug is fixed and deployed, it's time to figure out WTF kind of
magical garbage poop trinkets have been left for me on the server and how to
clean them up.

Looking through the logs again reveals a fair amount of purchase requests
actually completed. More specifically, anytime a `link` request is directly
followed by a `purchase` request, the customer had the item delivered to the
account properly.  That's the first good news of the evening; I haven't screwed
everyone on this new platform.

See, the link request sets the account id for the `da39a4` token. When a
purchase request comes in, I use the token to lookup the account id, then
credit that account. As long as another link request doesn't come along and
overwrite the account id, we're fine.

But overall, we're not fine. Traffic on launch day was high enough that we had
multiple instances of a `link-link-purchase-purchase` flow. When this
happens, the second link stomps over the first link's account id, causing the
first purchase to be credited to the second account! >:( Luckily, when the
second purchase request comes in, our server detects that the item exists on the
account and returns the `FAIL_ALREADY_OWNED` error. This gives us a way of
tracking how many accounts are missing items. The **second** piece of good
news!

	$ ... | grep 'FAIL_ALREADY_OWNED' | wc -l
	34

Ok. 34 people got hosed. This is manageable. But because of the async nature
between the link and purchase requests, I can't be 100% sure who got screwed
just by looking at the log.

Well, what's the size of the entire population of linked accounts? How many of
those accounts got their item correctly?

	$ ... | grep 'Linking' | wc -l
	144

	$ ... | grep 'OK' | wc -l
	90

Alright, so there's 144 people from AGC. 90 of them are setup correctly, 34 are
broken and 20 (144-90-34) haven't actually tried purchasing anything yet.

At this point I decide that I'm not going to be able to tell the difference
between the 34 people that need to get `GiftPackSmall` and the 20 people that
didn't actually purchase it. So, I'm going to take the hit and just give the
Holiday Pack to all 54 people. If you happened to sign up today via AGC without
buying anything, consider it a present :)

Oh, there's a slight problem. I know **how many** people I need to give the Holiday
Pack, but I don't know **who** they are. The bug that caused this mess has, by
definition, destroyed that information.

The good news is `GiftPackSmall` is a standard (non-rebuyable) bundle, and in
AirMech adding a standard bundle to an account that already owns it is a no-op.
So all I need to do is dump out the 144 emails, and run them through our bulk
account fixup tool. (which coincidentally just went online last night!)

	$ ... | grep 'Linking' | awk '{print $3}' > ~/emails_to_fix.txt

Now... back to that annoying player-color-chat-bug nonsense....
