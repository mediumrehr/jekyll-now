---
layout: post
title: Malörtware Thotcon 0xA Digerati's Atlas Write-up
date: 2019-05-26 00:00:00 -0500
image: /assets/thotcon_0xA/goldbadge2.jpg
categories: [hacking, thotcon, chicago]
youtubeId: yE6hTh4mn40
---

Malörtware, the mash-up of malware and [Malört](http://www.jeppsonsmalort.com/jm.html) (Chicago's gift to the world!), was our team name for [Thotcon's](https://thotcon.org/) Digerati’s Atlas badge challenge/CTF (created by [Sakebomb](https://twitter.com/Sakebomb), [Jay](https://twitter.com/jaymargalus), and [Rudy](https://twitter.com/rarsec)). If you recall, last year I participated in the badge challenge and came up just short of winning at the very end. This year, I wanted to bring some friends (new and old) along to help me out. Our team grew to include [Ben](https://twitter.com/BenLGardiner), Solomon, [czerny](https://twitter.com/heyczerny), [noonker](https://twitter.com/noonker_), [dev oopes](https://twitter.com/devoopes), [murriel](https://twitter.com/xmurriel), and myself. We were lucky to have a wide range of skills within the group, and without everyone we would not have been able to win the contest and a coveted Thotcon Gold (Lifetime) Badge!

![](/assets/thotcon_0xA/goldbadge1.jpg)

The following is a _brief_ write-up of the challenges we solved (not in order) and how we solved them.

#### The Start

The entrance to the contest began at the bottom of page 12 of the conference program with the following puzzle:

![](/assets/thotcon_0xA/start_hex.png)

This is an ascii string converted to hex, and once converted back to ascii, it reads `Sakebomb has something in his pocket you can scan, perhaps a key or rfid`.

The games have begun! ...but not so fast as we didn’t get a key from that. Upon talking to Sakebomb, he pulled out an RFID card and when scanned, it read `Please check out irc.thotcon.org for all your puzzle needs! key{...oh wait find the real fox on 146.565 mhz`.

Foiled again! But really this pointed us to where we could submit our flags (irc.thotcon.org), and where we would find a flag (fox hunting looking for a target on 146.565 MHz). Unfortunately, the fox kept going down, so we were not able to find it, but we were still able to use our scanning tools for a later challenge.

#### Badge1 - Look at the hardware

![](/assets/thotcon_0xA/badge1.png)

This challenge took us some time to start, mostly because none of us wanted to deal with it. haha There was a data matrix on the back of the badge, but it was not bordered, so barcode scanners did not pick it up. This challenge could either be solved by cropping a high quality photo of the barcode to include the left and bottom edges (colored as the badge) and scanning that, OR you could take the more difficult approach that we took, where we recreated the data matrix by recording the bit patterns and entering that into a python script I had written last year for a challenge to generate a clean data matrix image. Either way, scanning the code reveals `key{ANRKEY}` which ended up being a mistype for `key{anrky}` (we learned that most all uppercase keys were often meant to be submitted as all lowercase).

#### Badge3 - Look at the firmware

There were two ways to solve this one, you could either play the game Simon (a featured mode of the badge) and if you were able to make it to level 10, `key{sh0w_m3_y0ur_m3d4ll10n}` would be printed out over serial, or you could dump the badge firmware with [esptool](https://github.com/espressif/esptool) and run `strings | grep key` to get the key.

#### Badge4 - You know where you are. Figure out what it is.

This might have been the most difficult challenge we faced. It certainly took the most time and effort from our group.

When we were initially playing with the badge, we noticed a mode that played what sounded like [NATO Phonetic Alphabet](https://en.wikipedia.org/wiki/NATO_phonetic_alphabet) via text-to-speech over the speaker. Not only was it extremely difficult to understand (low volume and distortion by the speaker), it was also played at a fast speed. In an effort to interpret the audio, we wanted to record it, so we attempted to do so two different ways; we replaced the speaker on one of our badges with a 4-ring 3.5mm male connector with the audio out connected to the mic line (so it could be plugged into a computer as a mic in and recorded), and we replaced another badge's speaker with a better quality speaker. In the end, we were able to get the best quality with the latter option.

<audio src="/assets/thotcon_0xA/badge4_audio.wav" controls preload></audio>

After listening to this many times slowed down, we deciphered the audio as `C Q C Q Kilo Nine Delta Lima Girl Foxtrot Sierra Papa India Mike Echo Delta Oscar Tango Charlie Oscar Mike Sierra Lima Alpha Sierra Henry Kilo Nine Delta Lima Girl Foxtrot`, which can be further deciphered as `CQ CQ K9DLGF SPIME.COM/K9DLGF`. We weren't sure if it was intentional or not, but Spime's (one of the Thotcon badge contributors) website is `spimeinc.com` and the call sign `K9DLGF` is supposed to be `KD9LGF`, the call sign of Jay, one of the badge creators (we later learned this was unintentional and we gained a couple points for discovering and reporting this).

Navigating to spimeinc.com/kd9lgf/ we found the text `CQ CQ this is KD9LGF Kilo Delta Nine Lima Golf Foxtrot calling CQ badgenet #W9GN and standing by at 1556989200 GL SK`. Converting the numbers at the end from epoch timestamp to date reveals the time to standby, May 4, 2019 at 12:00p.

(waiting on another member for the second half of this puzzle write-up)

#### Programming1 - git gud

This challenge was meant to be simple, but it took us a surprising amount of time due to lack of description. There was a command in the irc room, `!gitstring` to generate an md5 hash. We first though we needed to generate a git commit collision with this hash, but that seemed uncharacteristically difficult. We then searched a few Thotcon related git repos for this hash, but didn’t find anything there. Finally, we asked Sakebomb and he explained that we simply needed to commit a file to a repository with the hash included. We did that, ran `!gitflag https://github.com/mediumrehr/thotcon-0xa-bruh` and received `key{l00k_wh0s3_c0d1ng}`.

#### Programming3 - et tu brute

One of the last challenges we tacked. With less than an hour left to go, we first tried to access this challenge by visiting http://irc.thotcon.org:10000/, only to find out it was not an http socket. Instead, we were able to connect using netcat `nc irc.thotcon.org 10000` and we were presented with the prompt, `Would you like to play a game?`. If you enter `yes`, you’re given the following rules: 

```
Great! Here's how you solve this
1. Crack the encrypted text
2. Obtain the Quotee
3. Send it back to the server
4. Do this 5 times correctly in a row in 5 seconds
5. Obtain your Key
Got it! Good.
Press "enter" to continue.
```

To solve this challenge, we quickly put together a script for connecting to the port, rotating the alphabetic characters of the string, checking with each rotation if the string contained English, and if it did, send the author back to the host. While one of us whipped up a socket connecting and Caesar shifting script, another team member wrote a basic function checked if a string contained any of a handful of the most common English words with 3 or more characters.
After adding the two parts together, we ran the script and received `key{what_you_seek_is_on_tcp_35k53}`.

Our script is posted [in this gist](https://gist.github.com/mediumrehr/53c1956db19934fe304fe629e6df4e1f)

#### Anarchy1 - This is disorder at its finest. Check TCP/1864

Checking TCP/1864 as the puzzle recommends, we visited http://irc.thotcon.org:1864/. On the page, there was an anarchist bisected flag and a download link for `State_of_Disorder.exe`. We downloaded the exe and opened it in [Hex Fiend](https://github.com/ridiculousfish/HexFiend) to find `key{Th3_F1ght}`

![](/assets/thotcon_0xA/anarchy1.png)

#### Anarchy2 - You have no authority here

While solving Anarchy1, we noticed `State_of_Disorder.exe` was formatted to resemble a torrent file, so we recreated the torrent file in hopes of downloading `The_Fight.pdf` (the file the torrent was setup to download). We were on the right track, but the internet was being unreliable, and Sakebomb was having difficulty seeding, so he pointed us to another location to download the file.
In `The_Fight.pdf` there was an encrypted block of text was at the end, followed by a Base64 encoded string `-QVJDRk9VUg==`. That string decodes to `ARCFOUR` which is a reference to the RC4 cipher.

```
386dad7816e5326c43d97e0e3c1f048dd2b5d341f9c8b795a2260235f1a7ef17
e8acde525aa513d86c52dc7ff8fc1f55fb18efe3b4ea91892a13c1c66fc67241
b4b9eca364139942709507179dd48e708dadaef17a56c0033f1e33a3888f94c1
5cae10a307de0374965d9db6d644da755819f80c3f2696f7a7a37c940e47eaa7
33255e88f10f74826dcb4fbeb91e8ff29a970c36ef1810f1b2009f4fef01068a
5140cd4a2d8dea9c2a9fdea98eeb629c905317fabf880cf3ef3d62fa59f76611
5a3a4480d8e83680a93178afa09e2dd6515d785fe765431285aa34e8131b8f16
99c6188fa0e4d4f1b42ab28b3709f4f430318a00c2619c69e30951774f7fa029
ac910ec0f5c017d28e4c685d9f16399c1aaa59b89f55dea49113b17e58067f45
77ac12a87d3afb203ce77201ee0e75d959d4b05264171a82432259c4965c199f
3491d8dc840d9ae866734c90d9623842373627e29224f876ee8bd51dff2d727d
422286175b5d8bfb7489cc247089ed4b57b24e4bba8ad9372a22294a4b6a2333
12429404f34858c99bc621691faf143fa6f70116b8f15f9b730ae46b70a13f10
11d32c98a945aa75e2a899f09b1570db4adb1953330d6204682383f01f241532
ade3d98db3471bef71c9c8b4daa1203430f6d3e4b561eb7936ed8f75b474202e
0d124d22e67574b7b6638ac8bb53b7b2
```

This RC4 cipher can be brute forced via the `rockyou.txt` wordlist (we did this with a custom Java method using the javax.crypto package), resulting in:

```
Decrypted Data: GET / HTTP/1.1
Host: 24gly2spgm2jz6gh7vp5ajjyqwvstuviidettffvg4zzszmdm36phfid.onion
User-Agent: Lynx/2.8.5rel.1 libwww-FM/2.15FC SSL-MM/1.4.1c OpenSSL/0.9.7e-dev
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us
Key: anomie
```

So we have: `key{anomie}`

#### Crypto1 - Read the program

![](/assets/thotcon_0xA/crypto1.png)

Scan the barcode on the back cover of the program to reveal `key{gimme_gimme_free_points}`

#### Crypto2 - Read the program

![](/assets/thotcon_0xA/crypto2.png)

The QR code on the back cover of the program has incomplete position markers. Add the center squares back and scan to reveal a link `https://www.youtube.com/watch?v=2Z4m4lnjxkY#key{trololo}`. The end of the link contains the key: `key{trololo}`

#### Crypto3 - Read the program

At the bottom of page 10 of the program is the following string `R3JrZCB6YnlxYmt3IG5vdm9kb24gZHJvIE1ib296b2IgZ3lidz8=`. Decode the string as Base64 to get `Grkd zbyqbkw novodon dro Mboozob gybw?`. Rotation Shift that by 16 to get `what program deleted the creeper worm?` And the answer to that is reaper! `key{reaper}`

#### Crypto4 - Read the program

On the back cover, beneath the barcode from Crypto1 is the string `vngg ckjn, fnnl sl rbn akkc vkpf! fny{qshq_p_xsj}`. This cipher is a permutation of the a..z alphabet, not a shift. We can get a partial mapping with some assumptions as a result of knowledge gained from the previous challenges:

```
fny = key
vngg ckjn = well done
fnnl = keep 
vkpf = work
```

So we now have `well done, keep up the good work! key{?u??_r_?un}`. We’ll assume the last part ‘is fun’ for r_fun. This our partial map is now `v=w n=e g=l c=d k=o j=n n=e f=k l=p p=r r=t b=h a=g y=y x=f`. Plugging this info into https://quipqiup.com/ we get 26 possibilities but only one makes sense and refers to the type of cipher, substitution, `key{subs_r_fun}`

#### Radio1 - I like to ride my bicycle[DELETE]ham radio

![](/assets/thotcon_0xA/radio1.png)

At the bottom-right of page 3 is what appears to be Morse code. Once deciphered, you get the string `TO QRV OR QRX THAN IS THE QUESTION <3 K4RUK`. It seems to be a [HAM radio](https://en.wikipedia.org/wiki/Amateur_radio) challenge, especially given the HAM radio symbol next to the Morse code. QRV and QRX are both Q-code questions, and I’ll admit, I didn’t know how exactly to answer that as a riddle, but after trying a couple of responses, `key{qsl_k4ruk}` did the trick.


#### Radio3

We came across this challenge while fox hunting around the con floor. We should have thought to tune in to THOT-FM sooner, since it contained a flag last year, but thankfully we still came across 93.7 while scanning around at the right time. One of our members captured the following shot, showing some odd behavior on the band.

![](/assets/thotcon_0xA/radio3_1.png)

After that, we did a little searching and made a guess that it was a slow-scan TV (SSTV) signal. Since one of our members was scanning using his phone, he recorded and piped the SSTV signal into his laptop and was able to decode it using [RX-SSTV](http://users.belgacom.net/hamradio/rxsstv.htm). The result, shown below, is a spin on the popular zero wing ‘all your base are belong to us’ meme, but with the text `HACKER: ALL YOUR KEY{Z3R0_W1NG} ARE BELONG…` and there’s our key!

![](/assets/thotcon_0xA/radio3_2.jpg)

#### Extra

While solving Badge4, we discovered a typo in the URL the HAM command audio directs to where two of the character positions were flipped (still can't believe we found that). As a result of being good sports and reporting this, we got a small bonus (5 points or something). The key to redeem those points was `key{sc0re!}`

#### Backdoor

As I found out last year, there's always a hidden challenge to modify/hack the badge to do something new (bonus points for adding new hardware). This year, we added an accelerometer (specifically an IMU: [Sparkfun Razor IMU](https://www.sparkfun.com/products/14001)) to give the badge another input mode. We also developed new firmware for the badge for a game we call Thot-it!, a game similar to [Bop-it!](https://en.wikipedia.org/wiki/Bop_It) The user must tilt the badge in the direction dictated by the LEDs and audio commands. The audio commands are "Hack it", "Crack it", "Thot it", and "Attack it", which correspond to tilting the badge forward, left, backward, and right, respectively.

[Badge Hack repo](https://github.com/mediumrehr/thotcon-0xA-badge-hack) (firmware and documentation)

{% include youtubePlayer.html id=page.youtubeId %}

#### Conclusion

The final hours of the contest were close, and we had a few teams nipping at our heels at times, but in the end, we were able to secure the win!

![](/assets/thotcon_0xA/leaderboard.png)

All in all, we had a great time, and I look forward to many more CTFs with the Malörtware team! Thank you to Sakebomb, Jay, and Rudy for designing a great set of challenges that pushed us to new limits and led us to learn new skills and new friendships!

![](/assets/thotcon_0xA/goldbadge2.jpg)