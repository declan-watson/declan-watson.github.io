---
title:  "If you can remember all your passwords, they're not strong enough"
layout: post
categories: password managers
---

## Introduction

There are multiple ways people can improve digital security in their lives. These range from not clicking on potentially dodgy links to using specifically hardened operating systems and using multi-factor authentication with phyisical security keys (such as [yubi key](https://www.yubico.com/)). Each of these methods can also be seen in the context of the amount of benefit it provides for that particular person's security needs [^1] and the amount of effort that method takes to set up and, more importantly, use.

Fortunately, adding a password manager falls in the section of having a great return on invested effort from a security perspective, but also requiring little technical expertise or ongoing tinkering. The best (in my opinion[^2]) password manager around is also free!

If you just came for a recommendation then the one I'm currently recommending is [Bitwarden](https://bitwarden.com/), the later sections will discuss the reasons why.

<img src="/assets/lockandwires.png" alt="A single padlock holding together rusted wire">

## Background on Passwords

We all know the intended functionality of a password; when we combine our username with a password we can log in to a service. This is what we see as the 'front end' to the login, however, in the background a lookup is taking place to compare the password you've entered to the password stored against your username in the site's massive table of users. When this has been properly implemented, the table isn't storing the password itself, it's storing a [hash](https://www.techtarget.com/searchdatamanagement/definition/hashing) of the password where it has been transformed to a different jumble of letters and numbers. This is done so that if the password table is taken, your password still isn't entirely visible. This sounds great, right? Unfortunately this doesn't solve all the problems.
```
(Original password) password123 --> MD5 Hash function --> 161bcd8552b9944d9bbf3e9b05961881 (Hashed password)
```
If someone sees that the hash of your password is `161bcd8552b9944d9bbf3e9b05961881`, then they don't know your password, so why does that matter? It matters because the original password used in this case is a very common one and the hash value has already been calculated so there is a known match between the two. Essentially, they _do_ know what your password is because they know that hashing `password123` will give that answer. This is known primarily due to password tables that have been hacked that have not been hashed, or stored 'in the clear'. Attackers can then separately hash these passwords, which they know to be common, then they're able to see if any passwords in a new hashed password list match these common hashed passwords. One of the largest (32.6m passwords) and most famous of these 'in the clear' dumps was from a 2009 breach of a company called [RockYou](https://techcrunch.com/2009/12/14/rockyou-hack-security-myspace-facebook-passwords/). This hack provided fascinating insights in to the way users think about and create passwords.

Here's the top 20 most common passwords in the dump:
1. 123456
2. 12345
3. 123456789
4. password
5. iloveyou
6. princess
7. 1234567
8. rockyou
9. 12345678
10. abc123
11. nicole
12. daniel
13. babygirl
14. monkey
15. lovely
16. jessica
17. 654321
18. michael
19. ashley
20. qwerty

You can definitely see a common theme among the passwords people tend to use. Typically the passwords are short, sequential and/or a single dictionary word; a disastrous combination from a security perspective _but_ easy to remember. This RockYou breach was from 2009, you might think we've come a long way since then but the fact that only 0.51% of passwords remained uncracked from this [relatively simple cracking test from 2018](https://www.researchgate.net/profile/Leon-Bosnjak/publication/326700354_Brute-force_and_dictionary_attack_on_hashed_real-world_passwords/links/5c6b6504a6fdcc404ebadec1/Brute-force-and-dictionary-attack-on-hashed-real-world-passwords.pdf) suggests we still have a long way to go. 85% of the cracked passwords in this study had 6 characters or less, and all of the passwords with 6 or less characters were successfully cracked. This brings us to the next problem with using short passwords like the common ones listed here; they can be cracked very easily. Even if your password has not yet been pre-hashed there are algorithims that can easily brute-force a password by checking the hash of each combination of characters one by one to see if it matches[^3]. This [table from Hive Systems](https://www.hivesystems.io/blog/are-your-passwords-in-the-green) shows how easy it is to brute-force diffrerent passwords based on length and character mix:

[<img src="/assets/password-table.png" alt="A table showing passwords with 9 characters or less are in the red">](https://www.hivesystems.io/blog/are-your-passwords-in-the-green)

---

## What kind of password?

As you can see, any passwords on the shorter side are almost useless when it comes to protection; this means you need a long password. What about upper case, lower case, numbers, symbols? The research suggests that adding to the length, rather than adding complexity, is better for security. This is also the view of the [National Institute of Standards and Technology (NIST)](https://auth0.com/blog/dont-pass-on-the-new-nist-password-guidelines/) who set password guidelines for the U.S. governemnt. For example, adding a symbol to a 9 character password takes the estimated cracking time from 3 days to 3 weeks, but adding an extra character (while keeping the complexity the same) shoots this up to 7 months.

There are a few methods of generating passwords that fit this requirement but the main one is [combining a group of dictionary words](https://xkcd.com/936/). You can do this with word generators like [Diceware](https://diceware.dmuth.org/) that make a passphrase instead of a password; the difference being the length and amount of words. An example of a passphrase is `correct-horse-battery-staple`, the length of the password is much longer than one word that has had random adjustments like capitals and symbols applied to it, making it much harder to crack.

## Do you still need a password manager?

The short answer is yes. The average person [has around 100 different password protected accounts](https://tech.co/password-managers/how-many-passwords-average-person). Even if you have a lot less than this that is still a whole lot to remember if they need to be different for every account. That's where a central repository (password manager) comes in. Because the alternative is re-using passwords, a password manager is the lesser evil in this case. Most companies will be breached at some point and not reusing passwords decreases the impact that an individual breach can have. Ideally we would love to be able to rely on our memory, but that just isn't the possible for the amount of passwords our online lives require.

## What if the password manager is compromised?

This is a completely valid question and is exactly why it's the _lesser_ evil. This is also known as a key barrier to entry as to why people don't use password managers in general.[^4] As it turns out, [65% of Americans don't trust password managers](https://www.passwordmanager.com/password-manager-trust-survey/). A single point of failure is bad, there's no other way to put it, but it's better than the alternative because of the additional security steps that good password managers implement.

This blog isn't meant to be overly technical so this description will be kept at a summarised level, but one of the key security concepts of password managers is the iteration of keys based on your password so it goes through the hash multiple times. This is usually done in the form of [Password Based Key Derivation Function 2 (or PBKDF2)](https://cryptobook.nakov.com/mac-and-key-derivation/pbkdf2)[^5].

In simple terms, the hash function is applied to your original password with a 'salt' (a set string of characters to help obscure it). Then the salt is added again, then hashed again. This hash is applied over and over again with the salt added each time. The amount of times the output goes through this function adds to the complexity of trying to crack it. If the iteration count is set to 2, it will look something like this:

---

| **Stage**                  | **Result**                              |
|----------------------------|-----------------------------------------|
| Original Password          | password123                             |
| Salted Password            | password123abcdefg                      |
| Hashed Salted Password (1) | ddb0944902e7b1b420aca866258b95c5        |
| Salt Hashed Password       | ddb0944902e7b1b420aca866258b95c5abcdefg |
| Hashed Salted Password (2) | ff4a581909054a6977e76419dce7ff85        |

This is probably incorrect to some degree (partially due to ignorance and partially due to the need to be concise) but the general concept remains true. This is a feature in password managers that works to make them more secure than typical logins so even shorter passwords are much harder to compromise.

The reason why this expands the security of a password vault so much is because you cannot jump from iteration 5 to iteration 10 or from iteration 5 to iteration 10,000; each of these stages have to computed sequentially by the nature of the mechanism. How does this help? It means that in addition to trying to brute force a hash of your password by testing `a` then `b` then `c`...then `aa` and `ab` and so on, a potential attacker has to spend extra computing time iterating the hash of each of those another multiple of times depending on how many iterations the function has been set to. This has minimal impact on the end user, at more than 1,500,000 my password manager is still near-instant at login through desktop and takes only a couple of seconds on mobile. Compare this to someone who has no idea what the password is and it quickly becomes cost (and time) prohibative to calculate all of the iterations required. This even provides a little extra protection even if our password can be found in a dictionary list or a common password list by still increasing the amount of time it would take to get through. Does this mean the password to our password manager doesn't have to be complicated? Not really, it's always better to have defense in depth, multiple layers of good security practices rather than relying on just one.

The recommendation from the Open Web Application Security Project (OWASP) is to have at least 310,000 iterations on top of the core password. This is where things get tricky and some of the differences between different password managers arise.

## Which password manager?

The recent [events over at LastPass](https://blog.lastpass.com/2022/12/notice-of-recent-security-incident/), a very well known password manager, have cast a shadow over the industry in general due to specific bad practices that are not necessarily reflective of the software as a concept. Recently, a hacker managed to gain access to a bunch of password vaults (including enterprise vaults). From the discussion going around, it doesn't seem like anyone is blaming them for being hacked, this is likely to happen to most companies at some point, it's more to do with the multiple failures and decisions along the way.

Where did LastPass go wrong specifically?
- Not all of the information was encrpyted
  - This means that information such as website was left in the clear so an attacker would have more information than they should when trying to break in
- The number of PBKDF2 iterations is defaulted to 100,100
  - **this is also the case for Bitwarden, make sure you change this if you use or start using Bitwarden**
  - This is well below the recommended number of iterations suggested (310,000)
  - Again, having this set much higher only has a negligible impact on the end user but makes it a lot harder to breach for an attacker
- This default is relatively new, prior to this it was much lower (some as low as 5 or even 1 depending on how far back you go) and the keys have not been regenerated to keep up with the new iteration default
  - The failure to update the iterations was one of the major security oversights by LastPass, as well as allowing such low counts to begin with
- Questionable timing in notifying users of the breach
- It has previously allowed 8 character passwords with no notification after being set that this is now out of date (from a security perspecitve) and should be updated
- [Here is a full blog post](https://palant.info/2022/12/26/whats-in-a-pr-statement-lastpass-breach-explained/) tearing apart, for lack of a better word, the PR response to the breach and detailing the multiple company failures

So, the above suggests a lot of reasons to avoid LastPass, but why recommend Bitwarden?
- It's open source
  - The source code can be checked to make sure that the things it says are encrypted are actually encrypted
- It's free
  - For example, it has unlimited passwords across unlimited devices
  - A lot of other password managers require a monthly or yearly fee for basic features that Bitwarden has for free, for example it may cost to be able to log in on multiple devices
- It has a web and mobile app with a relatively easy to use user interface (some improvements could be made, but it's easy enough)
- It's automatically synced across devices
  - Other options like [Keepass](https://keepass.info/), while being a great option, require a bit more manual set up for using passwords across devices
- If you don't trust your vault being hosted by Bitwarden, you have the option to (because it's open source) host the bitwarden software yourself although this is a far more technical and advanced route
  - This also means you would be responsible for securing the host machine, most people would not be up for this challenge
- It has a built in password and passphrase generator

## Conclusion

There are a lot of options out there, and without the time to test them all, personally Bitwarden ticks all of the boxes. Obviously at the end of the day this comes down to personal preference and security requirements. Whichever password manager you use, make sure you have a strong password behind it, make sure that it supports full encryption and the key iterations are set to well over the OWASP recommended number (310,000).

Use a password manager though, if you can remember all your passwords then they aren't strong enough.

Until next time,

Declan


<!-- https://www.usenix.org/system/files/soups2019-pearman.pdf 
https://www.security.org/digital-safety/password-manager-annual-report/

[some alarming statistics](https://bitwarden.com/resources/world-password-day-global-survey-full-report/) from Propeller Insights who surveyed 2,000 global internet users 
-->

---
_Notes_

[^1]: The security needs of someone just trying to protect their passwords from hackers trawling through random sites looking for open databases will be very different to someone who is specifically being targeted by a hacking group or a nation state.
[^2]: My opinion is based on a few factors which I'll discuss but it's generally based on the use case of someone who, as mentioned before, isn't looking to protect against targeted hacking or nation state actors; people in this situation would likely need something more intense.
[^3]: This takes me back to my university that _only_ allowed passwords of 8 characters, I can't remember if they allowed symbols or not. Even at the time, I knew this was awful practice and I sure hope they've changed since then.
[^4]: University of Tennessee - https://userlab.utk.edu/files/papers/oesch2022observational.pdf
[^5]: Although there are other methods around, there doesn't seem to be any great benefit of these over PBKDF2 given that the users themselves (i.e. weak passwords) are usually the point of failure.
