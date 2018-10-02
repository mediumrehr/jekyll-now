---
layout: post
title: DEF CON China (Beta) and Hacking a Boxing Game
date: 2018-06-07 00:00:00 -0000
categories: [hacking, defcon, travel]
---

#### Pre Con

This was the first (beta) year of DEF CON China, and it took place in the city of Beijing. I was lucky enough to attend and represent the [Hardware Hacking Village](https://dchhv.org), along with my long-time friend, [Grungy](https://twitter.com/Grngy). We didn't really know what to expect (I don't think anyone did), but with the help of some of the other HHV members, a month before the event we submitted a list of tools/materials we wanted to have available for attendees (soldering supplies, select tools, and beginner friendly soldering kits).

We arrived on Tuesday in an effort to adapt to the time change before the con, as well as to have time to check out some of the tourist areas before we were consumed by the con. We lucked out and ran into [Jayson Street](https://twitter.com/jaysonstreet) and not only did he recommend how to go about visiting the Summer Palace (which we visited on Wednesday), he also organzied a trip for a few of us foreigners to visit the Great Wall Thursday morning.

![]({{site.baseurl}}/images/dc_china_beta/great_wall.jpg)

#### Who Let the Attendees Out?!

We weren't able to start setting up the space until early Friday morning, but thanksfully some student helpers came by and gave us a hand. We originally set up about a dozen soldering stations, but we quickly learned that twelve irons were not enough. We ended up setting up all 20 irons and they remained occupied from opening (930a) until close (530-6p). We had to kill the power after announcing closing because people wanted to keep soldering!

The HHV was just as busy, if not more, Saturday. A few of the student helpers had stuck around Friday and returned Saturday. They helped us hand out alarm clock soldering kits to attendees, and after some time, they became familiar enough with the kit that they began to recognize and understanding the common errors attendees would run into when assembling. It was neat to see the helpers go from complete unfamiliarity with hardware/soldering, to a level where they were able to help attendees debug and understand what was wrong with their assembled kits. I was surprised how easy it became to teach and communicate without words, and instead through actions and demonstration.

#### Let the Games Begin

Saturday night was the big DEF CON party. It was fun, but it ended early (10p) and a few of us wanted to keep going, so we collected whatever beer we had (someone even went on a beer run) and continued hanging in the con space.

DEF CON had rented a couple arcade machines for attendees to play, and one particular cabinet caught the attention of many passerbys, [World Boxing Championship](https://youtu.be/mwpsUotTKU4). Everyone wanted to show off; trying to throw the hardest punches. The game was played by hitting a punching pad as hard as you could, three separate times. Each punch, a score was given based on the force calculated by the game, and the final score was the sum of the three attempts. The highest score stayed shown on the cabinet, glowing in all of it's red, segmented display glory.

![]({{site.baseurl}}/images/dc_china_beta/world_boxing_champ.jpg)

While hanging in the con area, Grungy mentioned wanting to see what was inside the game's cabinet. With a little bit of force, we were able to get the back of the cabinet open and we started snooping around. We identified some key components (power supply, processing board, light controllers). We also noticed a few wires running along the inside of the cabinet that looked like they were coming from something relating to the punching pad. To test this, after hitting the ```Start``` button, I disconnected the wires from the processor and we gave the pad a punch. Nothing happened in the game. These wires seemed like a fun place to start investigating.

At this point, Grungy headed off to bed, but I continued poking around after grabbing my portable oscilloscope from my room (I'm not good at traveling light). After some sniffing, I identified two signals that were affected by the punching pad's position/angle. The first signal was grounded until the punching pad was moved from its resting position; at which point it became floating. The second signal was floating until the punching pad went back almost as far as it could go, and at that point it became grounded. Judging by the simplicity of the system, it didn't seem far-fetched to think that the game system was measuring the time between the two signals changing and using that as its "punch force" calculation, rather than a force sensor.


![]({{site.baseurl}}/images/dc_china_beta/sniffing_around.jpg)

By this time, I was feeling tired, so I paused my investigation and went to bed.

#### Finish Him

I woke up early Sunday morning to my brain already forming a plan of attack for fooling the punching game into thinking I had thrown god-like punches (something I wouldn't be able to do physically). Funny enough, I ran into Zoz at breakfast, and he said he also got up early thinking about the same thing. He had been popping in and out the night before, following my knockout investigation.

My plan was to break out the two signals I had identified and route them to two buttons that I could push and simulate the punching pad getting hit. To my convenience, the default states (ground and floating) of these two signals were all that I'd need. I just had to route the signals to the buttons to match the default conditions, and then change the output to the opposite state when the button was pressed.

As soon as the HHV was up and running, I ducked out with Zoz and walked over to World Boxing Championship to setup the attack. Once I connected the "punch spoofer", Zoz tapped the ```Start``` button and I attempted to simulate a punch, quickly pressing the first button, and then the second. It worked! Although, it only resulted in a score of 400kg, the lowest possible punch force. I tried it again, 400kg... I began to fear my goal of having the highest possible score was only going to lead me to the all-time lowest.


![]({{site.baseurl}}/images/dc_china_beta/punch_spoofer.jpg)

After a few rounds, and a little practice, I was registering mid 400's; still far from the highscore. At this point Zoz wanted to have a try, so we switched roles and I took over starting a new game and reading off scores (I guess I could have added a third button for triggering a new game from the back). Zoz immediately identified that the second button/signal wasn't necessary. All that mattered was how long the first button was pressed. Using a quick, yet delicate, technique to press the button, he registered some of the hardest punches that machine had likely ever seen. Although we wer unable to earn a perfect score, we were only 10kg off and the score was unbeatable by anyone playing the old-fashioned way.

#### Winning my First Contest

News quickly spread of our achievement. I had returned to the HHV, but Zoz came by and told me Dark Tangent wanted to see me. At frist I was worried that I was in trouble for hacking something that didn't belong to me or DEF CON (I mean, we were a little rough with the cabinet), but instead when I showed up DT shook my hand and congratulated me on what I'd done. Turns out, he was hoping attendees would start hacking things, and he wanted to make an example of me by bringing me up on stage at the closing ceremony and announcing a new contest, a contest that Zoz and I had apparently won by hacking World Boxing Championship.

I didn't receive a black badge or anything as a result, but it felt awesome to have such a large (and hopefully positive) impact on the first ever DEF CON China, both through my efforts in the Hardware Hacking Village, and by finding a way to get the highscore on the most popular game at the conference. I always walk away from DEF CON so inspired by what others do and teach, and to be able to return the favor to some other attendees really meant a lot.

DEF CON China Beta will always have a special place in my heart, from the amazing people I met, to all of the exciting things I was able to do and see. I really hope I'll be able to return next year!


![]({{site.baseurl}}/images/dc_china_beta/champions.jpg)