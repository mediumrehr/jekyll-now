---
layout: post
title: Adventures at Thotcon 0x9
date: 2018-05-31 00:00:00 -0000
image: /assets/adventures_at_thotcon_0x9/badge_back.jpg
categories: [hacking, thotcon, chicago]
youtubeId: v3yVL2lNKM4
---

i’ve been missing out on [thotcon](https://thotcon.org/) the past few years while living in chicago, so i was happy to finally attend for the 9th installment. my goal from the start was to compete in the badge ctf in hopes of winning a coveted gold badge (admission to thotcon for life). i’m sad to say i missed most of the talks, but i had a blast crunching through the puzzles cooked up by [sakebomb](https://twitter.com/sakebomb).

once i arrived and received my badge and program i started searching through the materials for a starting point. on page 27 on the program i found five numbered puzzles. they were pretty straight forward; hex to ascii, cryptogram, morse code, number to letter, cryptogram, and a pattern. there was also an unmarked puzzle in red, a pigpen cipher that pointed towards badge ctf webpage once deciphered. unfortunately, at the time of solving this, the site wasn’t live yet, so i moved on to the badge.

![pamphlet puzzles](/assets/adventures_at_thotcon_0x9/pamphlet_puzzles.png){:width="700px"}

the badge hardware consisted of an esp8266 (and eeprom) with a screen and three buttons. the binary for the badge had been released the night before, so i had already looked it over in [Hex Fiend](https://ridiculousfish.com/hexfiend/). a few cipher looking strings displayed on boot, but none of them seemed significant, only coming up to variations of “THOTCON” once deciphered. the initial firmware featured a thotcoin (the official coin of thotcon) mining game, a store to buy thotcoin mining upgrades, and space invaders. to mine thotcoins, you needed to rapidly press the center button on the badge. this seemed tedious, so i probed the center button to find that it was just pulling the GPIO low, so in an attempt to save myself from developing arthritis in my thumb by the end of the con, i attached an arduino pro mini to the center button GPIO and i programmed it to simulate button presses at the rate of 1 press/millisecond.

$$$

i began raking in the coins, and i bought all the upgrades, only to accidentally find a bug that left me locked out of the store with a low quality pickaxe (your pickaxe level determines how many coins you can mine at a time). despite having enough thotcoins to buy out the thotcon mining franchise, i had no way to upgrade my pickaxe in the game.

i figured pickaxe value was stored in the eeprom because pickaxe level persisted through reboots and firmware reflashes. i wrote a quick script to dump the eeprom the analyze the registers. i also dumped a friend’s badge who had a level 4 pickaxe to compare. i was correct, the pickaxe level was being stored at address 0x14. i ALSO discovered coin count was also stored in the eeprom, starting at address 0x0.

$$$JACKPOT$$$

![thotcon miner](/assets/adventures_at_thotcon_0x9/thotcon_miner.jpg){:width="700px"}

at this point, it seemed i had made it as far as i could at the time on the badge since the next firmware upgrade was still being developed, so i checked back with the badge ctf website. the site was finally live! i submitted flags for the ciphers i had solved in the con booklet, and a few other challenges (ranging 5-30 points each) local to the challenge site; finding a flag in an image’s exif data, analyzing an ftp stream capture to track down a flag, finding another flag in an image’s binary, and correcting the header on a file to reveal the flag.

i also visited the ctf table and solved the Rubik’s cube as a challenge (30 points). i told sakebomb about my adventures restoring the pickaxe level and upping my coin count. he then offered me a new challenge, “hack the badge to do something cool”, worth a whopping 150 points!

i quickly attached an LED to the badge and made it blink. that was nowhere near enough. my next idea was to generate a basic audio tone. i attached an amplifier and speaker to a PWM-able GPIO to create a basic tone that ramped up in volume. still too simple for the challenge. this was going to take more thought and time, but day 1 was ending.

that night, i solved a few more cipher challenges (20-30 points) on the badge ctf site and i was able to pull into the top 3. if i wanted to get the first spot, i had a lot of work ahead of me.

![badge back](/assets/adventures_at_thotcon_0x9/badge_back.jpg){:width="700px"}

day 2

i woke up early and cleaned my badge hack in my lab. i had the idea to implement a morse code repeater as my badge hack. it’d allow me to use the speaker and LED i’d already attached, and it’d be more than just an LED blinking or a tone sounding.

i arrived at the con an hour late and immediately got to work finishing up the firmware for my badge hack (posted to [github](https://github.com/mediumrehr/thotcon0x9-Morse), if you’re curious). i was able to get it to transmit ‘SOS’ in morse code (transmitting ‘thotcon’ in the video below), and earn sakebombs approval (150 points).

{% include youtubePlayer.html id=page.youtubeId %}

that morning i also completed two other challenges. first, i tuned into the radio station sakebomb was broadcasting at 3.1m wavelength (aka 96.7 Hz). there were five or so songs he kept repeating, and it took me a bit to figure out the flag was part of one of the songs, dido’s black flag. “there will be no black flag above my door…”. this might have been my favorite flag due to the creativity of it. he had written on the badge ctf site that flags would follow the formate ‘flag {…}’, so the flag was 'above my door’ (75 points). the second set of points came from making a new friend who solved the Rubik’s cube on my behalf faster than 1min 30sec (30 points).

time was ticking, so i returned to the badge, which now had a new challenge available. the new challenge cycled through displaying three parts of a qr code. i took a picture of each display, recorded the bit orders, and then generated cleaner versions. i was a little suspicious from the start that there were only 3 different sections of the qr code displayed, and after recreating and piecing them together, it was clear they were quarters and i was still missing a quarter. for a 50 point challenge, i didn’t expect i’d have to reverse a qr code, so i asked the badge creators if this was intentional. turns out it was not. they reflashed my badge, i recreated the last quarter of the qr code, it was a link to a badge firmware, and i solved the challenge by entering the name of the binary(50 points). (sidenote: i noticed there was a binary with an odd filename uploaded to the badge firmware website the night before. turns of the name of that binary was the flag. could have guessed and saved some time…oops)

![qr code conversion](/assets/adventures_at_thotcon_0x9/qr_convert.png){:width="700px"}

the new firmware unlocked a new challenge that listed a half-dozen commands, up, down, left, right, A, B. my mind immediately jumped to the Konami code (the code was also shown in the con booklet), so i used the commands to enter it in. success! a qr code was displayed that pointed me to a webpage with a barcode.

![toaster drm barcode](/assets/adventures_at_thotcon_0x9/barcode.png){:width="700px"}

the challenge with the highest point bounty was still unsolved by anyone, so i wondered if the barcode i just uncovered was related to that. the challenge was to crack the drm on a toaster to toast a piece of bread. this required scanning the barcode for a compatible bread, and then scanning a barcode to pass the drm check. i had gotten lucky with my timing because when i showed up to the toaster, lt. dan (another attendee competing in the ctf) was working on the challenge as well. he had solved the fist part of the problem, and had the barcode for the bread. we agreed to combine our solutions to both solve the challenge. after some help from a friendly bartender, i was able to secure two pieces of white bread for the toaster. barcodes scanned, bread toasted, flag acquired (250 points!).

at this point, i was in first, but team yes took back the lead shortly after. my goal was to reclaim first place by completing the final two badge related challenges, both worth a few hundred points, but they still were being developed and weren’t ready start. i was a little worried at this point because i had under two hours left, and i knew they’d be tougher than what I had already encountered. while waiting, i solved the remaining low point problems i had left on the badge ctf site. i then started on the tougher cipher problems, but wasn’t able to make much traction before the new badge firmware was ready with less than an hour left.

the badge guys loaded the firmware onto my badge and told me “it’s now broadcasting”. since they weren’t uploading the binary, i figured i’d have to dump it in order to find a way to connect to the badge. luckily, [esptool](https://github.com/espressif/esptool) has a feature to read the flash from the esp8266. unfortunately, this feature didn’t seem to be working for me. it’d run fine in the beginning, and i’d see the activity as the flash was read, but the binary file was never created on my machine. as desperation kicked in, and as badge guys were also unable to dump the flash with esptool to prove it was possible, i asked them for a copy of the binary so i could move forward in the challenge. with 10 minutes left, i feverishly sifted through the file in Hex Fiend and with strings command. nothing stuck out to me as the next step in the puzzle, and unfortunately, 10 minutes quickly passed as the ctf closed.

i placed second overall, but i was proud to be the only single person team in the top 3. i didn’t end up winning a gold badge, but i did get some neat prizes for second, and i had a blast competing!

shout outs to sakebomb for putting together a challenging and fun ctf, [irl space](https://twitter.com/irlspace), [workshop88](http://workshop88.com/), and spacelab for designing the badge and working hard to keep up with thotcons attack on it, lt dan for doing the work to figure out what bread the toaster would only accept, [drew](https://twitter.com/pdp7) for trusting me enough to leave me with his badge so i could dump the eeprom, and all the new friends i made along the way!
