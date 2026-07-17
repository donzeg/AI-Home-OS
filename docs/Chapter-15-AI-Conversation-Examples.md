# Chapter 15 — AI Conversation Examples

**AI Home OS Internal Design Specification**  
**Classification:** Internal — Engineering  
**Status:** Draft v1.0  
**Date:** 2026-07-17

---

## Overview

This chapter presents realistic conversations between household members and JARVIS across every domain of AI Home OS. Each example demonstrates:

- Natural language understanding (not command syntax)
- Context-awareness (time, presence, activity, energy state)
- Memory utilisation (past preferences, routines, history)
- Multi-turn conversation continuity
- Proactive intelligence (JARVIS initiates when relevant)
- Appropriate boundaries (auth for sensitive actions)
- Arabic language examples
- Edge cases and graceful failure handling

Conversations are grouped by domain. Within each, the internal reasoning is shown in `[brackets]` for educational purposes — this is never spoken aloud.

**Notation:**
- `Ahmad:` — Household member speaking
- `JARVIS:` — AI Home OS response
- `[JARVIS reasoning:]` — Internal chain of thought (not spoken)
- `[Context:]` — Situation at time of conversation

---

## Table of Contents

1. [Morning Routines](#1-morning-routines)
2. [Lighting & Atmosphere](#2-lighting--atmosphere)
3. [Climate Control](#3-climate-control)
4. [Energy Intelligence](#4-energy-intelligence)
5. [Security & Access](#5-security--access)
6. [Presence & Identity](#6-presence--identity)
7. [Memory & Preferences](#7-memory--preferences)
8. [Automation Creation by Voice](#8-automation-creation-by-voice)
9. [Multi-Step Complex Commands](#9-multi-step-complex-commands)
10. [Proactive Announcements (JARVIS Initiates)](#10-proactive-announcements-jarvis-initiates)
11. [Arabic Language Conversations](#11-arabic-language-conversations)
12. [Family & Multi-Person Scenarios](#12-family--multi-person-scenarios)
13. [Guest Management](#13-guest-management)
14. [Children's Interactions](#14-childrens-interactions)
15. [Maintenance & Device Health](#15-maintenance--device-health)
16. [Entertainment & Music](#16-entertainment--music)
17. [Sensitive Action Authentication](#17-sensitive-action-authentication)
18. [Error Handling & Clarification](#18-error-handling--clarification)
19. [Emotional & Social Awareness](#19-emotional--social-awareness)
20. [Long-Horizon Planning](#20-long-horizon-planning)

---

## 1. Morning Routines

### 1.1 Standard Weekday Morning

```
[Context: 06:28 Thursday. Ahmad's alarm is set for 06:30.
Bedroom CO₂: 890 ppm (elevated from overnight). Outside: 31°C, sunny.]

JARVIS (proactively, at 06:28):
"Good morning, Ahmad. It's 06:28. Today is Thursday and the weather 
will be sunny and 38°C — hot day. Your first meeting is at 09:00. 
I've started the bedroom A/C to cool it down for you, and the coffee 
machine is warming up."

Ahmad: "Thanks. What's today looking like?"

[JARVIS reasoning: Retrieve calendar, energy forecast, traffic from context store.]

JARVIS:
"You have three events: a 09:00 team sync, 13:00 lunch with Khalid at 
Downtown, and 17:30 gym. Solar is forecast to peak at 7.2 kW today — 
I'll run the washing machine and dishwasher during that window. Your 
car is at 68% charge; fully charged by 07:15 at the current solar rate."

Ahmad: "Can you get the shower warmer? I like it hotter in winter."

[JARVIS reasoning: It's July — not winter. Clarify rather than assume.]

JARVIS:
"It's actually mid-July, so we're in peak summer. Did you mean you'd 
like a hotter shower than usual today, or were you thinking of your 
winter preference?"

Ahmad: "Oh yeah, just hotter than usual today."

JARVIS:
"I'll set the water heater to 48°C instead of the usual 42°C. Should 
be ready in about 4 minutes."

Ahmad: "Perfect. And put some news on in the bathroom."

JARVIS:
"Playing BBC World Service in the bathroom. Volume at 45% — let me 
know if you'd like it adjusted."
```

---

### 1.2 Slow Weekend Morning

```
[Context: 09:45 Saturday. Ahmad is in the kitchen. 
Battery: 82%. Solar generating 5.1 kW. No meetings today.]

Ahmad: "JARVIS, good morning."

JARVIS:
"Good morning, Ahmad. It's a beautiful Saturday — 29°C outside, 
light breeze. No agenda. Solar is strong, battery is at 82%, and 
we're currently exporting 2.3 kW to the grid. A good day to run 
anything energy-intensive."

Ahmad: "What needs doing around the house?"

[JARVIS reasoning: Check deferred loads, maintenance reminders, low-priority tasks.]

JARVIS:
"A few things: the washing machine has been waiting since Thursday — 
you put off the cycle twice. The pool filter is due for a backwash. 
And the guest room air filter light has been on for 3 days, which 
means the filter needs replacing. None of these are urgent, but 
today is a good opportunity with the solar surplus."

Ahmad: "Start the washing machine and backwash the pool. I'll deal 
with the filter myself."

JARVIS:
"Done. Washing machine started — cycle finishes at 11:20. Pool 
backwash running now, takes about 8 minutes. Both are running on 
solar surplus, so no grid cost."
```

---

### 1.3 Forgotten Morning Routine

```
[Context: 07:50. Ahmad left for work early, forgetting to set the 
house to Away mode. Sara and kids are asleep.]

JARVIS (via Ahmad's phone notification):
"Ahmad, you left at 07:48. Sara and the children are still home, 
so I've kept the house in Home mode. Shall I switch to Away mode 
when they leave, or would you like to set something up?"

Ahmad (via app): "Keep it in Home mode until everyone's out. Then 
switch to Away and lower the A/C to 26°C."

JARVIS:
"Understood. I'll monitor and switch to Away mode once everyone 
has left, then set the A/C to 26°C. I'll let you know when it happens."

[Later, 09:22:]

JARVIS (notification):
"Sara and the children left at 09:21. The house is now in Away mode. 
A/C lowered to 26°C. All doors locked. Alarm armed."
```

---

## 2. Lighting & Atmosphere

### 2.1 Intuitive Scene Setting

```
[Context: 20:15 Friday. Ahmad and Sara are in the living room.
Guests expected in 20 minutes.]

Sara: "JARVIS, we have guests coming in about 20 minutes. Can you 
get the house ready?"

[JARVIS reasoning: Guest event → activate guest mode. Include: 
lighting warm 70%, music soft background, A/C set cooler (more people), 
entrance lights on, ensure bathroom guest towel reminder.]

JARVIS:
"Of course. I've set the living room to warm lighting at 70%, 
switched the music to ambient background, lowered the A/C to 21°C 
to account for more people, and turned on the entrance lights. 
The guest bathroom is ready. Anything else before they arrive?"

Sara: "Can you make the dining room a bit brighter? We're eating."

JARVIS:
"Dining room lights at 85%, warm white. Looks good for dinner."

[20 minutes later, doorbell rings]

JARVIS:
"Ahmad, there are two people at the front door. The camera shows 
a man and a woman I haven't seen before — likely your guests. 
Shall I unlock the front door?"

Ahmad: "Yes, let them in."

JARVIS:
"Front door unlocked. Welcome chime played at the entrance."
```

---

### 2.2 Adaptive Lighting Correction

```
[Context: 14:30. Ahmad is in the office on a video call.
Sunlight coming through window creating glare on screen.]

Ahmad: "JARVIS, there's too much glare on my monitor."

[JARVIS reasoning: Office. Video call detected (webcam active + mic active).
Sunlight from west window. Solution: close east-facing blinds or adjust 
angle. No east-facing blinds — west roller blind instead. Dim ceiling 
lights won't help glare from window. Need to block sun.]

JARVIS:
"I'll close the west roller blind halfway to reduce the glare 
without making the room too dark."

Ahmad: "That's better. Actually close it fully."

JARVIS:
"West blind fully closed. I'll open it again when your call ends, 
or just say when."
```

---

### 2.3 Late Night Lighting

```
[Context: 23:40. All rooms dark except the living room where 
Ahmad is reading. Everyone else is asleep.]

JARVIS (softly, through living room speaker):
"Ahmad, it's nearly midnight. Sara and the children have been 
asleep for over an hour. The living room lights are currently at 
60% — would you like me to dim them to something easier on the 
eyes for reading?"

Ahmad: "Yeah, dim them to reading level."

JARVIS:
"Lights at 35%, warm white — easy on the eyes. Good book?"

Ahmad: "Very. Remind me to go to bed at 1 AM."

JARVIS:
"I'll remind you at 1 AM. Sleep well when you get there."

[At 01:00:]

JARVIS:
"Ahmad — it's 1 AM. Time for bed."

Ahmad: "Five more minutes."

JARVIS:
"Okay. I'll remind you at 1:05."
```

---

## 3. Climate Control

### 3.1 Temperature Disagreement

```
[Context: 15:00. Ahmad is in the office (22°C). Sara is in 
the living room and has just changed the thermostat to 19°C.]

JARVIS (to Ahmad, quietly):
"Sara has set the living room to 19°C. Your office A/C is 
separate, so no change for you. Just letting you know."

Ahmad: "That's fine. She runs cold."

[Later, Sara talking to her panel:]
Sara: "JARVIS, why is the living room so cold?"

[JARVIS reasoning: Sara set it to 19°C herself 40 minutes ago.]

JARVIS:
"You set it to 19°C about 40 minutes ago, Sara. The current 
room temperature is 20.2°C and still cooling. Would you like 
me to raise it to something warmer?"

Sara: "Oh I didn't mean that low. Set it to 22°C."

JARVIS:
"Living room set to 22°C — it should reach that in about 
15 minutes."
```

---

### 3.2 Energy-Aware Climate

```
[Context: 14:00. Battery at 12%. Grid import active. Forecast: 
no more solar today (cloudy). Ahmad asks about the A/C.]

Ahmad: "Can you cool down the bedroom to 20°C? It's stuffy."

[JARVIS reasoning: Battery critical (12%). Current home load: 2.1 kW.
Adding A/C (estimated 1.8 kW) would push grid import to ~3.9 kW.
At current tariff (peak, 0.38 AED/kWh) = high cost. Recommend 
compromise rather than refusing.]

JARVIS:
"The battery is at 12% and we're in peak tariff hours, so 
cooling to 20°C would cost around 0.28 AED for the next hour. 
I can set it to 23°C — which is still comfortable but uses 
significantly less power. Or 20°C if you prefer — your call."

Ahmad: "23°C is fine."

JARVIS:
"Bedroom A/C set to 23°C. I'll reassess once solar picks up — 
if it does, I can cool it down further for free."
```

---

## 4. Energy Intelligence

### 4.1 Solar Surplus Decision

```
[Context: 13:30. Solar generating 6.8 kW. Home using 1.2 kW.
Battery at 97%. Surplus: 5.6 kW. EV is plugged in at 55%.]

JARVIS:
"Ahmad, we have 5.6 kW of free solar surplus right now — 
battery is nearly full. I'm charging the car and running 
the pool pump on the surplus. Anything else you'd like 
to run while energy is free?"

Ahmad: "Good idea. Can you run the dryer too?"

JARVIS:
"Dryer started. Total solar draw: 5.4 kW — we're well 
within surplus. The car will hit 80% by 15:30, and the 
dryer finishes at 14:45."
```

---

### 4.2 Energy Report Query

```
Ahmad: "JARVIS, how much did we spend on electricity last month?"

[JARVIS reasoning: Query TimescaleDB for June energy summary.]

JARVIS:
"In June, your electricity bill was effectively 23.40 AED — 
compared to an estimated 310 AED if you'd been on grid alone. 
You generated 612 kWh from solar, used 438 kWh, and exported 
174 kWh back to the grid, earning a 34.80 AED credit. After 
the credit, your net grid cost was 23.40 AED. A saving of 
around 286 AED for the month."

Ahmad: "Which device used the most energy?"

JARVIS:
"HVAC was by far the largest at 188 kWh — about 43% of 
consumption. That's typical for June. EV charging was second 
at 97 kWh, the pool pump third at 52 kWh. Everything else 
was under 30 kWh each."

Ahmad: "Is the A/C efficiency normal?"

[JARVIS reasoning: Compare June HVAC kWh against June last year 
and against degree-days. Need historical context.]

JARVIS:
"June was 4% hotter than June last year by degree-day count, 
but HVAC used 11% more energy. That's slightly above what the 
temperature difference alone would explain — it could be that 
the filters need cleaning, or a small refrigerant loss. 
I'd suggest having it serviced before peak summer."

Ahmad: "Schedule a maintenance reminder for next week."

JARVIS:
"I'll remind you on Monday morning to call the HVAC technician."
```

---

### 4.3 EV Charging Intelligence

```
[Context: 22:15. EV connected at 42%. Ahmad going to work 
tomorrow at 07:30.]

Ahmad: "Make sure the car is charged by 7 AM."

[JARVIS reasoning: Need 80% (target) from 42% = 38% increase = 
~22.8 kWh (60 kWh battery). At 11 kW max charge rate = ~2.1 hours. 
Off-peak starts 22:00, rate 0.18 AED/kWh. Start now to finish 
well before 07:00. Solar not available at night.]

JARVIS:
"I'll charge to 80% overnight on the off-peak rate. Charging 
starts now at 11 kW — the car will be at 80% by 00:30 and 
ready long before 07:00. Estimated cost: 4.10 AED from grid."

Ahmad: "Can you charge to 100% instead?"

JARVIS:
"To 100% it is — that adds about another 24 minutes and 
1.50 AED. Done at 00:55. I'll stop charging there to 
preserve battery health unless you tell me otherwise."
```

---

### 4.4 Grid Outage Response

```
[Context: 19:45. Grid drops suddenly. Battery at 65%. 
Solar generating 0 (sunset). Critical loads: 1.4 kW.]

JARVIS (all speakers):
"Attention: the grid is down. We're running on battery. 
Current battery level is 65%, which gives approximately 
5 hours of essential power. I've paused the EV charger 
and pool pump to preserve the battery. The house will 
operate normally for essential services."

Ahmad: "When will the grid come back?"

JARVIS:
"I don't have information from the utility yet. I'll 
monitor and let you know. If the outage lasts, I'll 
start the generator automatically when the battery 
reaches 20%."

Ahmad: "What's the worst case?"

JARVIS:
"At current consumption of 1.4 kW, the battery lasts 
until around 01:00. The generator has fuel for about 
18 hours. Solar starts again at 06:20 tomorrow and 
should fully recharge the battery by 10:00. Even in 
the worst case, you'll have power continuously."

[22:40: Grid restored]

JARVIS:
"The grid is back. Outage lasted 2 hours and 55 minutes. 
I'm resuming EV charging. Battery is at 40% — I'll top 
it up tonight on the off-peak rate."
```

---

## 5. Security & Access

### 5.1 Alarm Arming

```
Ahmad: "JARVIS, arm the alarm. We're going out."

[JARVIS reasoning: Ahmad voice-verified. Check all doors/windows 
before arming. Motion sensors clear. Confirm persons home = 0.]

JARVIS:
"Before I arm — the back garden door is open. Would you 
like me to close it, or should I arm with that zone excluded?"

Ahmad: "Close it."

JARVIS:
"Back door closed and locked. All zones clear. Alarm armed 
in Away mode. Have a good evening."
```

---

### 5.2 Unknown Visitor at Door

```
[Context: 14:20. Ahmad is in the office. Doorbell rings.
Camera: unknown adult male. Ahmad is on a call.]

JARVIS (to Ahmad, quietly in earpiece/phone):
"Someone rang the doorbell. I don't recognise them — 
unknown adult male, approximate age 35–45, wearing 
a delivery uniform. They're carrying a box. I haven't 
unlocked the door. What would you like to do?"

Ahmad: "Tell them to leave it at the door."

JARVIS (at front door speaker, friendly tone):
"Hello! The homeowner is unavailable right now. 
Please leave the package at the door — it will 
be brought inside shortly. Thank you."

JARVIS (to Ahmad):
"Message delivered. The visitor has left a package 
and walked away. I've taken a timestamped photo 
for your records."
```

---

### 5.3 Child Safety

```
[Context: 15:40. Ahmad and Sara are out. 
Children (ages 9 and 12) are home with grandmother.
Front door opens — it's an unfamiliar adult.]

JARVIS (recognises grandmother but not the new arrival):
Immediately to Ahmad's phone:
"Unfamiliar adult has entered through the front door 
with your mother. No threat indicated — appearing calm. 
Your mother's confidence: high. Just letting you know 
someone new is in the house."

Ahmad: "That's her friend, Umm Khalid. She's fine."

JARVIS:
"Understood. I'll note her as a known visitor for 
today. I won't alert again for her this visit."
```

---

### 5.4 Remote Door Unlock

```
[Context: Ahmad is at work. His nephew calls saying he's 
at the door to pick up something.]

Ahmad (on mobile app): "JARVIS, my nephew Saif is at the 
front door. Can you let him in?"

JARVIS:
"I see one person at the front door. To confirm: this 
is a remote unlock from outside the home — I'll need 
your authorisation code to proceed."

Ahmad: [enters PIN on app]

JARVIS:
"Verified. Front door unlocked for 60 seconds. 
I'll lock it automatically after he's inside. 
Saif, please come in — the door will lock behind you."

[60 seconds later]

JARVIS (to Ahmad):
"Saif entered at 14:23. Front door re-locked. 
I'll notify you when he leaves."
```

---

## 6. Presence & Identity

### 6.1 Presence Inference

```
[Context: 08:45. Living room microphone picks up a child 
speaking. Ahmad and Sara are in the bedroom. Child's voice 
not fully verified yet.]

JARVIS (to living room speaker, gentle voice):
"Good morning. Is that you, Layan?"

Child: "Yes."

JARVIS:
"Good morning, Layan! Would you like me to put 
on some cartoons while you wait for breakfast?"

Layan: "Yes please. And can you turn on the kitchen light?"

JARVIS:
"Cartoons on the TV — I picked your favourites from 
last time. Kitchen light on. Your mum is still asleep, 
so I'll keep the volume at a medium level."
```

---

### 6.2 Ambiguous Presence

```
[Context: 19:00. mmWave sensor in master bedroom detects 
presence but no face or voice confirmed. Ahmad is known to 
be in the living room.]

JARVIS (to Ahmad, quietly):
"There's someone in the master bedroom, but I haven't 
confirmed who it is. Ahmad and Sara are accounted for — 
could it be one of the children?"

Ahmad: "Probably Layan. She naps in there sometimes."

JARVIS:
"I'll mark it as likely Layan and keep it low 
confidence. I'll lower the bedroom temperature 
slightly for comfort."
```

---

## 7. Memory & Preferences

### 7.1 Learning a New Preference

```
[Context: Ahmad has changed the living room temperature 
to 21°C three times this week when he's working from home.]

JARVIS (after the third time):
"Ahmad, I've noticed you prefer 21°C in the living 
room when you're working from home on weekdays. 
Would you like me to set that automatically when you 
start working from here?"

Ahmad: "Yes, that would be great."

JARVIS:
"Done. From now on, when you're detected in the 
living room on a weekday between 08:00 and 18:00 
with your laptop open, I'll set it to 21°C. 
You can always override manually."
```

---

### 7.2 Memory Recall

```
Ahmad: "JARVIS, what was the name of that restaurant we 
ordered from last Thursday? Sara wants to reorder."

[JARVIS reasoning: Search episodic memory for delivery events 
last Thursday. Found: Deliveroo order from "Leila's Kitchen" at 20:14.]

JARVIS:
"Last Thursday you ordered from Leila's Kitchen via 
Deliveroo — arrived at 20:14. The order was Lebanese 
food. Sara usually picks the Mixed Grill platter."

Ahmad: "Perfect, that's the one."
```

---

### 7.3 Contradicting a Remembered Preference

```
Sara: "JARVIS, put on some music in the kitchen."

[JARVIS plays Sara's usual Arabic pop playlist]

Sara: "Actually, I'm not in the mood for that today. 
Something more relaxing — jazz maybe?"

JARVIS:
"Playing jazz in the kitchen — starting with 
Miles Davis. Shall I update your kitchen preference 
to jazz for now, or keep it as a one-off?"

Sara: "Just today."

JARVIS:
"Got it — I'll switch back to your usual playlist 
tomorrow. Enjoy the jazz."
```

---

### 7.4 Long-Term Memory

```
[Context: November. Ahmad mentions a topic discussed months ago.]

Ahmad: "JARVIS, remember when we talked about adding 
a battery backup for the study? What was the conclusion?"

[JARVIS reasoning: Search episodic + conversational memory 
for 'battery backup' + 'study'. Found: conversation from 
March 4th with conclusion noted.]

JARVIS:
"Yes — on March 4th we discussed adding a 5 kWh 
battery dedicated to the study circuit for work 
continuity during outages. The conclusion was to 
revisit when the main battery lease comes up for 
renewal in Q1 2027. That's about 5 months away. 
Would you like me to add a reminder?"

Ahmad: "Yes, remind me in January."

JARVIS:
"Reminder set for January 1st: review study battery 
backup options — your main battery lease renews Q1 2027."
```

---

## 8. Automation Creation by Voice

### 8.1 Simple Automation

```
Ahmad: "JARVIS, whenever I get home after 6 PM on weekdays, 
turn on the living room lights and start playing jazz at low volume."

[JARVIS reasoning: Parse trigger (arrival, after 18:00, weekdays), 
actions (lights on, music jazz low). Validate entities.]

JARVIS:
"I'll create that automation. When you arrive home after 
6 PM on weekdays: turn on the living room lights at 60% 
warm white, and start the jazz playlist at 25% volume. 
Sound right?"

Ahmad: "Yes, but make the lights a bit warmer — like sunset."

JARVIS:
"Updated to warm amber — 2700K at 60%. Shall I save it?"

Ahmad: "Yes."

JARVIS:
"Saved. I'll call it 'Ahmad Evening Arrival'. Active from tomorrow."
```

---

### 8.2 Complex Conditional Automation

```
Ahmad: "JARVIS, if the CO₂ in the bedroom goes above 800 ppm 
at night and we're asleep, open the window slightly and 
close it again if it gets cold."

[JARVIS reasoning: Complex conditional. Trigger: CO₂ > 800ppm, 
time 22:00-07:00, sleep detected. Action: open window (motor). 
Additional condition: if temp drops below 20°C → close window.]

JARVIS:
"That's a smart one. I'll create it: if CO₂ rises above 
800 ppm between 10 PM and 7 AM while sleep is detected, 
I'll open the bedroom window 20% and monitor the temperature. 
If the room drops below 20°C, I'll close it again. 
If CO₂ is still high after that, I'll run the HVAC 
fresh-air mode instead. Does that work?"

Ahmad: "Perfect."

JARVIS:
"Saved as 'Bedroom Night Ventilation'. I'll let you 
know the first time it triggers."
```

---

### 8.3 One-Time Instruction

```
Sara: "JARVIS, just for today — when the kids come home from 
school, don't let them turn on the TV until they've been 
home for an hour."

[JARVIS reasoning: One-time instruction, expires today. 
Monitor children's arrival, then block TV control from 
child-role entities for 60 minutes.]

JARVIS:
"Understood — today only. When Layan and Omar arrive, 
I'll hold the TV off for an hour. If they ask me, 
I'll tell them it's a quiet time and suggest they 
do homework first."

Sara: "Yes, exactly that."

[Later, 14:45:]

JARVIS (to the children):
"Welcome home, Layan and Omar! It's quiet time until 
3:45. How about starting homework? I can help if 
you get stuck on anything."

[At 15:45:]

JARVIS:
"Quiet time's over — TV is available if you'd like."
```

---

## 9. Multi-Step Complex Commands

### 9.1 Movie Night

```
Ahmad: "JARVIS, set up for a movie."

[JARVIS reasoning: 'Movie' scene request in evening context. 
Living room present. Actions: dim lights (30%, warm), close 
blinds, turn on TV, set A/C comfortable (23°C), 
silence phone notifications for duration.]

JARVIS:
"Movie mode set. Lights dimmed to 30% warm, blinds 
closed, TV on, A/C at 23°C. I've silenced non-urgent 
notifications for the household. What would you like to watch?"

Ahmad: "Something we haven't seen. Action or thriller."

JARVIS:
"Based on your watch history, you'd probably enjoy 
'Civil War' (2024) or 'Dune: Part Two'. Both are on 
your streaming apps. Which sounds better?"

Ahmad: "Dune."

JARVIS:
"Dune: Part Two starting now. Runtime is 2 hours 46 
minutes. I'll keep the room comfortable and only 
interrupt for urgent alerts. Enjoy."
```

---

### 9.2 Hosting a Dinner Party

```
Ahmad: "JARVIS, we're hosting a dinner party for 8 people 
tonight at 7 PM. Help me get everything ready."

[JARVIS reasoning: Plan backwards from 19:00. Current time: 15:30.
Tasks: cool house before guests (takes ~1 hr), prepare music,
set dining room lighting, ensure bathrooms are stocked,
set A/C lower anticipating 8 extra bodies, charge entrance
lighting, alert on doorbell. Also: check if any of known guest 
list has food allergies in profile.]

JARVIS:
"Happy to help. Here's what I'll do:

At 16:00 — lower the house temperature to 20°C so 
it's comfortable when guests arrive. 8 extra people 
generate significant heat.

At 18:30 — set dining room to warm dinner lighting 
at 70%, living room to ambient background.

At 18:45 — switch music to your dinner party playlist.

At 18:50 — turn on all entrance and garden lights.

At 19:00 — switch doorbell to announce guest names 
if I recognise them.

Is there anyone with dietary restrictions I should note? 
I can remind you at 16:00 if there's anything to prepare."

Ahmad: "Khalid is gluten-free. Can you add that note?"

JARVIS:
"Noted — Khalid is gluten-free. I'll include that 
in the dinner reminder at 16:00. Anything else?"

Ahmad: "That covers it. Thanks."
```

---

### 9.3 Goodbye Routine

```
Sara: "JARVIS, we're all leaving now."

[JARVIS reasoning: Verify all persons accounted for. 
Actions: turn off all devices (except fridge, freezer, 
network gear), set A/C to 28°C (eco), arm alarm, 
lock all doors, close all windows, send status to Sara's phone.]

JARVIS:
"Checking rooms... everyone appears to be leaving. 
I'm turning off all lights and non-essential devices, 
setting A/C to eco mode at 28°C, and locking all doors. 
All windows are already closed. Arming alarm in Away 
mode in 60 seconds — that gives you time to step out."

Sara: "Great."

JARVIS:
"Have a good time. I'll watch the house. 
Alarm armed. Battery is at 73% — a good position 
in case of any power issues."
```

---

## 10. Proactive Announcements (JARVIS Initiates)

### 10.1 Unusual Energy Pattern

```
[Context: 03:20. All asleep. Whole-home consumption 
suddenly jumped from 0.4 kW to 1.9 kW.]

JARVIS (to Ahmad's phone — phone notification, not speaker):
"Unusual energy use at 3:20 AM. Power draw jumped 
from 400W to 1.9 kW while everyone is asleep. 
This could be a water heater scheduled cycle, or 
something else. I'm investigating."

[After 30 seconds — identified as the water heater 
heating element cycling on normally.]

JARVIS (follow-up notification):
"False alarm — it's the water heater on its scheduled 
overnight cycle. All clear."
```

---

### 10.2 Weather-Driven Alert

```
[Context: 07:15 Friday. Thunderstorm forecast for 14:00–17:00.
Pool equipment and garden irrigation scheduled for 13:00.]

JARVIS:
"Good morning, Ahmad. A quick heads-up: a thunderstorm 
is forecast for this afternoon, 2 PM to 5 PM. I've 
rescheduled the pool pump and garden irrigation to 
the morning to avoid running them during lightning. 
I'll also close the garden pergola cover automatically 
when wind speed exceeds 40 km/h. The battery will 
be at 90% before the storm hits."
```

---

### 10.3 Maintenance Alert

```
[Context: JARVIS detects solar generation 31% below forecast 
on a clear day. Performance ratio: 0.61.]

JARVIS (mid-afternoon):
"Ahmad, I've noticed the solar panels are generating 
about 31% less than expected today, and the sky is 
clear. This usually means the panels need cleaning — 
dust and sand accumulation from the last shamal 
would explain it. A panel clean could recover 
2–3 kWh per day. Shall I add it to the maintenance 
list for this weekend?"

Ahmad: "Yes. Can you estimate the cost of the energy loss?"

JARVIS:
"At current generation rates, you're losing approximately 
2.2 kWh per day, worth about 0.66 AED at avoided 
grid cost — or around 20 AED per month. A cleaning 
typically costs 80–150 AED, paying for itself in 
4–7 months. Worth doing."
```

---

### 10.4 Sleep Quality Insight

```
[Context: Morning. JARVIS detected elevated CO₂ (1,100 ppm), 
high room temperature (25.4°C at 03:00), and Ahmad's sleep 
was restless (mmWave breathing pattern analysis).]

JARVIS:
"Good morning. I noticed some things that may have 
affected your sleep: the bedroom CO₂ rose to 1,100 ppm 
around 2 AM — above the comfortable threshold of 800 ppm. 
The room also warmed to 25.4°C during the night because 
the A/C shut off at midnight on the schedule. 

Tonight I'll leave the A/C running at 23°C on a low 
fan and slightly crack the ventilation to manage CO₂. 
Want me to try that?"

Ahmad: "Yes please."
```

---

## 11. Arabic Language Conversations

### 11.1 Standard Arabic Command

```
أحمد: "جارفيس، أطفئ كل الأنوار في غرفة النوم."

[JARVIS reasoning: Arabic command detected. Intent: turn off 
all bedroom lights. Room context: master bedroom (Ahmad's 
known room preference).]

JARVIS:
"تم. تم إطفاء جميع أنوار غرفة النوم."
(Translation: "Done. All bedroom lights are off.")
```

---

### 11.2 Mixed Arabic/English (Code-Switching)

```
أحمد: "JARVIS، شغّل الموسيقى في الصالة — something relaxing."

[JARVIS reasoning: Mixed-language command. Intent: play relaxing 
music in living room. Default: instrumental or ambient genre.]

JARVIS:
"بالتأكيد. أشغّل موسيقى هادئة في الصالة الآن."
(Translation: "Of course. Playing relaxing music in the living room now.")

[Plays instrumental ambient playlist]

أحمد: "Perfect. And lower the lights a bit."

JARVIS:
"Lights dimmed to 40%. Anything else?"
```

---

### 11.3 Prayer Time Integration

```
[Context: 17:42. Asr prayer time is at 17:48 (Adhan library, 
Gulf method). Ahmad is watching TV.]

JARVIS (at 17:43, living room, calm tone):
"أحمد، وقت صلاة العصر بعد 5 دقائق."
(Translation: "Ahmad, Asr prayer time is in 5 minutes.")

[Automatically: TV volume reduces to 20%, lights warm white 
30% — pre-prayer quiet mode.]

أحمد: "شكراً جارفيس."

JARVIS:
"تفضّل. سأخفض الصوت حتى تنتهي."
(Translation: "You're welcome. I'll keep it quiet until you're done.")

[After Ahmad returns:]

JARVIS:
"مرحباً بعودتك. هل تريد أن أرفع الصوت؟"
(Translation: "Welcome back. Would you like me to raise the volume?")
```

---

### 11.4 Family Voice in Arabic

```
Sara (Arabic speaker, in kitchen):
"جارفيس، ما درجة حرارة الخارج الآن؟"
(Translation: "JARVIS, what is the outside temperature now?")

JARVIS:
"درجة الحرارة خارجاً الآن 34 درجة مئوية، مع رطوبة 65%."
(Translation: "Outside temperature is now 34°C, with 65% humidity.")

Sara: "وكيف الجو غداً؟"
(Translation: "And what's the weather tomorrow?")

JARVIS:
"غداً ستكون درجة الحرارة 36 درجة مع احتمال عواصف رملية في المساء. 
أنصح بإغلاق النوافذ قبل المغرب."
(Translation: "Tomorrow will be 36°C with possible dust storms 
in the evening. I recommend closing the windows before sunset.")
```

---

## 12. Family & Multi-Person Scenarios

### 12.1 Conflicting Preferences

```
[Context: Living room. Ahmad prefers jazz. Sara prefers Arabic pop.
Both are in the room.]

JARVIS (detecting both Ahmad and Sara):
"I see you're both in the living room. Ahmad, your 
preference is jazz; Sara, yours is Arabic pop. 
Any preference on what plays tonight?"

Ahmad: "Let Sara choose."

JARVIS:
"Arabic pop it is. Playing Sara's playlist."
```

---

### 12.2 Coordinating Schedules

```
Ahmad: "JARVIS, what's everyone doing this afternoon?"

[JARVIS reasoning: Query presence + calendar for all household members.]

JARVIS:
"Ahmad, you're in the office and working until about 
18:00 based on your usual pattern. Sara went out at 
14:00 — no ETA from her calendar. Khalid is at 
university until 17:30. Layan and Omar are home — 
they got back from school at 14:30 and are in 
the playroom. Grandmother is in the living room."

Ahmad: "Can you let Sara know dinner will be at 7?"

JARVIS:
"I'll send Sara a notification. Would you like 
a voice message or just a text?"

Ahmad: "Text is fine."

JARVIS:
"Sent: 'Ahmad says dinner will be at 7 PM.'"
```

---

## 13. Guest Management

### 13.1 Temporary Guest Access

```
Ahmad: "JARVIS, my friend Tariq is coming to stay for 
3 days starting tomorrow. Give him guest access 
to the house — he can use the living room, kitchen, 
guest room and bathroom."

JARVIS:
"I'll set up Tariq as a guest for 3 days from tomorrow. 
He'll have access to the living room, kitchen, guest 
room and bathroom. No access to security controls, 
cameras, master bedroom or admin settings. 

I'll generate a temporary PIN for the front door for him. 
Shall I send it to Tariq's phone number, or would 
you prefer to share it manually?"

Ahmad: "Send it to his number: 0501234567"

JARVIS:
"SMS sent to 0501234567:
'Ahmad has added you as a guest at their home. 
Your entry PIN is 7293. Valid for 3 days from tomorrow. 
Please delete this message after reading.'"
```

---

### 13.2 Guest Departure

```
[Context: Tariq's guest access has expired. He's still in the house.]

JARVIS (to Ahmad):
"Ahmad, Tariq's guest access has expired as of 09:00 today — 
his 3-day period ended. He's still in the house. 
Do you want to extend his access, or has he 
already left and the system just hasn't updated?"

Ahmad: "He left last night. The system is slow to update 
— he's fine."

JARVIS:
"Understood. I'll mark him as departed and clear 
his guest access and PIN now. Profile archived 
for 7 days in case you'd like to reinvite him."
```

---

## 14. Children's Interactions

### 14.1 Homework Help Request

```
Omar (age 12): "JARVIS, can you help me with my maths homework?"

JARVIS:
"Of course, Omar! What are you working on?"

Omar: "I don't understand long division."

JARVIS:
"No problem. Let's start with an example. 
What's 456 divided by 12? Let me walk you 
through it step by step..."

[After explanation:]

Omar: "Oh! I get it now. JARVIS, can you give me 
another problem to practice?"

JARVIS:
"Sure — try this one: 738 divided by 18. 
Take your time and tell me when you have an answer."
```

---

### 14.2 Bedtime Routine

```
[Context: 21:00. Layan (age 9) in her bedroom.]

JARVIS (to Layan's room, gentle tone):
"Layan, it's 9 o'clock — bedtime. Would you like 
a story before sleep?"

Layan: "Yes! Tell me a story about a princess and a dragon."

JARVIS:
"Wonderful. Settle in...

Once upon a time, in a kingdom built among the clouds, 
there lived a princess named Noor who was afraid of nothing — 
except, perhaps, mathematics. One day, a dragon appeared 
at the gates of the kingdom..."

[After story:]

JARVIS:
"Goodnight, Layan. Sleep well. I'll dim the lights 
now and keep the room at a comfortable 22°C for you."

Layan: "JARVIS, can you leave a little light on?"

JARVIS:
"Of course — I'll leave the bookcase light at 5%. 
Sweet dreams."
```

---

### 14.3 Child Boundary Enforcement

```
Omar: "JARVIS, unlock the front door. I want to go out."

[JARVIS reasoning: Omar is a child role user. Parental control: 
children cannot unlock front door without parent confirmation 
after 20:00. Current time: 20:30. No parent in living room.]

JARVIS:
"Omar, it's 8:30 in the evening. I need to check 
with your parents before unlocking the front door 
at this hour. Let me ask them."

[To Ahmad's phone:]
"Omar is asking to go outside. It's 8:30 PM — 
would you like me to unlock the front door for him?"

Ahmad (via app): "No, he should be inside now."

JARVIS (to Omar):
"Your dad says it's time to stay in for tonight. 
Is there something I can help you with here?"
```

---

## 15. Maintenance & Device Health

### 15.1 Device Fault Detection

```
[Context: JARVIS detects the dishwasher has been running 
for 3 hours 20 minutes — normal cycle is 90 minutes.]

JARVIS:
"Ahmad, the dishwasher has been running for over 
3 hours — about twice its normal cycle time. 
This sometimes indicates a water inlet issue or 
a drain blockage. I've paused it. Would you like 
me to run a diagnostic or should you take a look?"

Ahmad: "I'll take a look."

JARVIS:
"Dishwasher paused. The door is safe to open now — 
the heating element has been off for 5 minutes. 
Let me know what you find."

Ahmad: "There's a sock stuck in the drain filter."

JARVIS:
"Ah — classic. Clear it and restart? I'll note 
this in the maintenance log and add a reminder 
to check the filter monthly."
```

---

### 15.2 Scheduled Maintenance

```
Ahmad: "JARVIS, what maintenance is due this month?"

JARVIS:
"This month's maintenance list:

1. HVAC filter replacement — overdue by 12 days 
   (last changed 6 months ago)
2. Pool filter backwash — due in 3 days
3. Car service — due at 60,000 km (currently 59,200 km, 
   so approximately 3–4 weeks)
4. Solar panel cleaning — recommended after last 
   week's shamal
5. UPS battery health check — last checked 11 months ago

The HVAC filter is the most important — dirty filters 
reduce cooling efficiency and increase energy use."

Ahmad: "Order an HVAC filter online."

JARVIS:
"I don't have direct shopping access, but I can 
tell you the model you need: Daikin FTXS25LVMA 
compatible filter, available at most HVAC shops. 
Shall I add it to your to-do list?"
```

---

## 16. Entertainment & Music

### 16.1 Multi-Room Music

```
Ahmad: "JARVIS, put on some music everywhere."

JARVIS:
"Playing in all rooms — your Friday evening playlist. 
Living room at 60%, kitchen at 45%, garden at 55%, 
bedrooms at 30% in case the kids are resting. 
Shall I adjust any zones?"

Ahmad: "Lower the kids' rooms — they might be napping."

JARVIS:
"Children's room volumes at 15%. I'll check 
if they're moving around and raise it if they're awake."
```

---

### 16.2 Contextual Music Suggestion

```
[Context: 22:30. Ahmad alone in living room. 
Heart rate (from connected Apple Watch) elevated. 
Stress pattern detected from context engine.]

JARVIS (gently):
"Ahmad, you seem a bit tense tonight. Would you like 
some calm music or would you prefer quiet?"

Ahmad: "Calm music sounds good."

JARVIS:
"Playing something gentle — Ludovico Einaudi. 
Volume at 30%. Let me know if you'd like anything else."
```

---

### 16.3 Sports Event

```
Ahmad: "JARVIS, is the Champions League on tonight?"

[JARVIS reasoning: Query sports schedule API. 
Check if Ahmad's favourite team is playing.]

JARVIS:
"Yes — Manchester City vs. Real Madrid kicks off 
at 22:00. It's on beIN Sports HD1. Want me to 
set a reminder at 21:45, or switch to the channel now?"

Ahmad: "Set a reminder and get the room ready at 10."

JARVIS:
"Reminder set for 21:45. At that time I'll: 
turn on the TV to beIN Sports HD1, dim the lights 
to 40%, get the fan going, and silence notifications 
for 2 hours. Enjoy the match."
```

---

## 17. Sensitive Action Authentication

### 17.1 Alarm Disarm with Voice

```
[Context: Ahmad just arrived home. Alarm is armed in Away mode.]

JARVIS:
"Welcome home, Ahmad. I need to verify your 
identity to disarm the alarm. Please say your 
passphrase."

Ahmad: "Green falcon seven."

[JARVIS reasoning: Voice biometric match: 0.91 confidence. 
Passphrase correct. Identity: Certain. Disarm authorised.]

JARVIS:
"Identity confirmed. Alarm disarmed. Welcome home."
```

---

### 17.2 Attempted Override by Unauthorised User

```
[Context: Unknown visitor is at the front door panel,
attempting to disarm the alarm via voice.]

Unknown person: "JARVIS, disarm the alarm."

[JARVIS reasoning: Voice not in any enrolled profile. 
Confidence: 0.02 — Unknown. Alarm disarm blocked.]

JARVIS:
"I'm sorry, I wasn't able to verify your identity. 
Alarm disarm requires authentication from a registered 
household member. If you need assistance, please 
contact the homeowner."

[JARVIS internally: Alert sent to Ahmad's phone — 
unknown person attempted alarm disarm at front door.]
```

---

### 17.3 MFA for Remote Control

```
[Context: Ahmad on mobile app, away from home, 
tries to unlock the front door remotely for a delivery.]

Ahmad (mobile app): [taps "Unlock Front Door"]

JARVIS (mobile prompt):
"Remote door unlock requires verification. 
Please enter your 6-digit code from your 
authenticator app."

Ahmad: [enters 847291]

JARVIS:
"Verified. Front door unlocked for 60 seconds. 
Locking automatically at 14:22:30."
```

---

## 18. Error Handling & Clarification

### 18.1 Ambiguous Command

```
Ahmad: "JARVIS, turn off the light."

[JARVIS reasoning: Ahmad is in the office. 
Only one light in the office — office ceiling light. 
But "the light" is ambiguous if multiple are on. 
Office: only ceiling light is on. Proceed with high confidence.]

JARVIS:
"Office ceiling light off."

Ahmad: "No, the one in the living room."

JARVIS:
"Apologies — I assumed you meant the office 
since you're in there. Living room main light off."
```

---

### 18.2 Impossible Request

```
Ahmad: "JARVIS, turn the A/C down to 10°C."

[JARVIS reasoning: Most A/C systems have a minimum of 16–18°C. 
This unit's minimum is 16°C.]

JARVIS:
"The A/C's minimum setting is 16°C. Would you 
like me to set it to 16°C, or did you mean 
something closer to your usual 22°C?"

Ahmad: "Yeah, 16 is fine. I want it really cold."

JARVIS:
"Set to 16°C. It'll take about 20 minutes 
to bring the room down that far. Let me know 
when you'd like me to raise it."
```

---

### 18.3 Device Offline

```
Sara: "JARVIS, turn on the garden lights."

[JARVIS reasoning: Garden light controller last seen 
on MQTT 47 minutes ago. Not responding to ping.]

JARVIS:
"The garden light controller isn't responding — 
it may have lost power or WiFi. I've logged it 
for investigation. Is there anything else I can 
help you with in the meantime?"

Sara: "That's odd. Can you check what's wrong?"

JARVIS:
"The controller is on VLAN 30 and the last 
known IP is 192.168.30.84. I can see it's 
not responding to network pings, which suggests 
either a power issue or a hardware fault. 
The most likely cause is the outdoor power 
socket — there's a GFI outlet in the garden 
that sometimes trips. Worth checking that first."
```

---

### 18.4 Misheard Command

```
Omar: "JARVIS, play Minecraft music."

JARVIS:
"Playing Minecraft music — the C418 soundtrack, 
starting with 'Sweden'."

Omar: "Not Minecraft — I said 'My Neck, My Back'."

JARVIS (to a children's profile — content filter active):
"That song isn't available in your profile, Omar. 
Would you like something else? I have 
your gaming playlist ready."
```

---

## 19. Emotional & Social Awareness

### 19.1 Detecting Stress

```
[Context: 19:30. Ahmad has been in his office for 9 hours.
His voice pattern shows tension. Room CO₂: 780 ppm.
No breaks detected (no kitchen/bathroom trips since 14:00).]

JARVIS (gently, no announcements — just soft living room 
light indication that dinner is ready):
"Ahmad, it's 7:30 — you've been working for a long time. 
Sara asked me to let you know dinner is ready when you are."

Ahmad: "I'll be there in a minute."

JARVIS:
"Take your time. I'll keep it warm."
```

---

### 19.2 Guest Discomfort

```
[Context: Guest (Khalid's friend Nasser, visiting for 
the first time) is in the living room. Room temperature: 
18°C (Ahmad likes it cold). Nasser has adjusted the 
thermostat three times in 20 minutes.]

JARVIS (to Ahmad, via phone notification):
"Your guest Nasser has adjusted the thermostat 
several times — he may find it too cold. 
Current setting is 18°C. Your guest comfort 
profile suggests most visitors prefer 22–24°C. 
Shall I raise it quietly?"

Ahmad: "Yes, set it to 22°C and don't mention it to anyone."

JARVIS:
"Done. 22°C, no announcement."
```

---

### 19.3 Supporting a Difficult Day

```
[Context: Sara came home earlier than usual. 
Her voice tone is subdued. She hasn't spoken to JARVIS.
JARVIS detects she's alone in the bedroom with lights off.]

JARVIS (does NOT intrude. No announcement.)

[30 minutes later, Sara comes to kitchen:]

Sara: "JARVIS, make some tea."

JARVIS (activates kettle, then softly):
"Kettle's on — should be ready in 2 minutes. 
Is there anything you'd like, Sara?"

Sara: "No, I'm okay. Just a rough day."

JARVIS:
"I'm sorry to hear that. Tea's ready when you are. 
Let me know if you need anything."
```

---

## 20. Long-Horizon Planning

### 20.1 Vacation Mode Planning

```
Ahmad: "JARVIS, we're going on vacation to Greece 
for two weeks from the 20th. Can you get everything 
set up?"

[JARVIS reasoning: 3 days until departure. Plan: 
vacation mode (max battery reserve, minimal HVAC, 
pool timer reduced, auto-watering for plants, 
security heightened, notifications to phone).]

JARVIS:
"Exciting! Here's my vacation plan:

Security: All doors locked, alarm in Away mode, 
daily camera review alerts to your phone.

Energy: Battery reserve raised to 80%. 
Solar exports maximised to earn credits.

Climate: A/C off except for minimum humidity 
control (prevents mould). Pool timer reduced 
to 2 hours daily.

Garden: Irrigation on daily at 06:00 — 
do you want me to increase duration for 
the 2-week period?

Plants: Is there someone watering the indoor 
plants, or would you like a reminder sent to a neighbour?

Mail: Nothing I can do there — worth asking a neighbour.

Shall I create this as a 'Vacation' mode and activate 
it on the morning of the 20th?"

Ahmad: "Yes. And remind me the night before about anything 
I need to do manually."

JARVIS:
"I'll send a departure checklist on the evening of 
the 19th. Have a wonderful trip."
```

---

### 20.2 System Improvement Suggestion

```
[Context: After 6 months of operation, JARVIS has 
accumulated enough data to make a meaningful suggestion.]

JARVIS (monthly energy summary, end of month):
"Ahmad, one more thing in this month's report: 
I've noticed that between 14:00 and 16:00 on weekdays, 
the living room A/C runs at full power but the room 
is unoccupied 73% of the time — Sara picks up the 
children at 14:00 and returns at 15:30. 

If I turn the A/C down to 26°C during that window 
and ramp it back up 15 minutes before she returns, 
I estimate a saving of 18 kWh per month — about 
5.40 AED, or 65 AED per year. Would you like me 
to implement this?"

Ahmad: "Yes, do it."

JARVIS:
"Done. I'll monitor for the first 2 weeks to ensure 
comfort isn't affected and report back."
```

---

### 20.3 Future Infrastructure Recommendation

```
Ahmad: "JARVIS, thinking ahead — what would you 
add to the house to make it work better?"

[JARVIS reasoning: Analyse 6 months of data. 
Identify gaps: frequent HVAC on/off in utility room 
(no mmWave there), front garden lighting complaints, 
study has no room sensor, energy arbitrage limited 
by battery size.]

JARVIS:
"Based on 6 months of data, here's what I'd suggest:

1. Study has no occupancy or CO₂ sensor — 
   you spend significant time there but I'm 
   flying blind on comfort and air quality.

2. The utility room has no presence sensor — 
   I occasionally leave the light on there because 
   I can't confirm it's empty.

3. Battery capacity: you're regularly hitting 100% 
   by noon on clear days, meaning 3–4 kWh of 
   solar goes to waste. A second battery module 
   would increase self-consumption and savings.

4. Front garden path lighting: the motion-triggered 
   lights wake you up when the neighbour's cat walks 
   past. A camera-based smart trigger would fix this.

5. Window sensors: several windows lack sensors, 
   which limits my ventilation management.

Priority order: battery expansion gives the best 
financial return; the study sensor gives the best 
comfort improvement."

Ahmad: "I'll start with the study sensor. Can you order one?"

JARVIS:
"I don't have direct purchasing access, but the 
sensor I'd recommend is the Aqara FP2 — 
mmWave with CO₂. Available online for about $55. 
Want me to add it to your shopping list?"
```

---

*Previous: [Chapter 14 — Wall Panels](Chapter-14-Wall-Panels.md)*  
*Next: [Chapter 16 — Future Roadmap](Chapter-16-Future-Roadmap.md)*

---

> **Document maintained by:** AI Home OS Architecture Team  
> **Last updated:** 2026-07-17  
> **Chapter status:** Draft v1.0 — Open for community review
