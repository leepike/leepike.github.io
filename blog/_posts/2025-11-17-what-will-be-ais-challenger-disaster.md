---
layout: post
title: "Max: Phase 1 Report"
date: 2025-11-17
---

# Will AI Have its Challenger Disaster?

In 1986, the Challenger Space Shuttle exploded 73 seconds after liftoff.[^1] Onboard was Christa McAuliffe, the first civilian and educator selected for spaceflight.[^2] Millions of schoolchildren watched live as the shuttle disintegrated. The tragedy was a national shock.

Within days, a presidential commission was formed to investigate the disaster. The commission uncovered the technical failure—a disintegrated O-ring[^3]—but also revealed deeper systemic issues, calling it “an accident rooted in history.”[^4] NASA had ignored engineers' warnings, pressured teams to proceed despite known risks, and relied on overly optimistic safety assessments.[^5] The shuttle program was grounded for over two years as NASA overhauled both its technology and safety culture.[^6]

It took a disaster to reset NASA’s approach to risk. What will it take to reset artificial intelligence’s safety culture?

AI has contributed to several safety incidents over the years. In 2018, Elaine Herzberg became the first pedestrian killed by a self-driving car when an Uber AI system misidentified her as a bicycle.[^7] AI has made questionable cancer treatment recommendations,[^8] made biased hiring recommendations,[^9] and facilitated financial fraud.[^10] A chatbot was implicated in a child’s suicide.[^11]

But AI has not yet had its Challenger-scale disaster—an event so publicly catastrophic that it compels a complete rethinking of safety standards. We speculate on what such a disaster might look like by drawing from historical tragedies in medical technology, automotive safety, and public infrastructure to predict AI’s reckoning.

One possibility is AI causing direct harm to human life through its integration into medical devices. A precedent is the Therac-25 medical radiation therapy machine that over-radiated six patients in the 1980s, killing or seriously injuring them. The Therac-25 was an early software-controlled medical device in which hardware fail-safes were implemented in code instead, and coding errors caused it to malfunction. The Therac-25 has become a classic case-study in software engineering, and its failures led to developing international medical software standards.[^12] A similar AI-driven failure in diagnostics or robotic surgery could spark a regulatory overhaul.

Another scenario is a series of AI-driven failures culminating in a public outcry. Ralph Nader’s 1965 book *Unsafe at Any Speed* exposed the auto industry’s disregard for safety. The book outlined several smoking guns such as the rollover risk in the Chevrolet Corvair, dangerous styling and ornamentation, and car companies blaming drivers for accidents. Congress formed a new agency that later became the National Highway Traffic Safety Administration (NHTSA) in response to the book.[^13] If AI is shown to be systematically unsafe—whether through biasing decision-making, widespread misinformation, or reckless deployment in self-driving cars—public backlash could force change and institute government oversight. 

A final possibility is a complex, cascading AI failure akin to the Three Mile Island nuclear accident. In 1979, a reactor overheated and partially melted down.[^14] The public backlash was immense; after the accident, no nuclear power plant was authorized to begin construction until 2012.[^15] Charles Perrow argued that such failures are inevitable in complex, tightly coupled systems.[^16] AI, which is being integrated into power grids, air traffic control, and industrial automation, could trigger a similar crisis. Imagine an AI-driven stock trading algorithm triggering a financial collapse, or a failure in AI-managed air traffic control causing multiple plane crashes.

Much of the discussion about AI risk centers around Artificial General Intelligence (AGI)—AI that surpasses human intelligence and escapes human control.[^17] This post isn't weighing in on the "AI doomer" debate about superintelligence. Whether you believe in the possibility of AGI or not, the kind of risks described above are more prescient. Real risks are already here, embedded in everyday AI applications. Worrying about AGI before addressing AI’s current safety failures is like debating self-driving cars before inventing seatbelts.

We do not need to wait for an AI catastrophe to take action. There are clear, pragmatic steps to ensure AI safety without stifling innovation. First, enforce existing safety standards in industries where AI is deployed. Medical devices, aviation software, and industrial automation already have rigorous certification processes—AI should not get a pass. Software in critical infrastructure must meet strict reliability benchmarks; generative AI should not be exempt from similar scrutiny. Second, mandate AI “seatbelts and airbags.” Just as the auto industry initially resisted safety features that saved lives, AI companies may resist regulations that impose guardrails. But explainability, reproducibility, and sector-specific safety requirements are essential. Third, regulate before the backlash. If AI failures mount without oversight, we risk an overcorrection—an AI Winter akin to the post-Three Mile Island freeze on nuclear power, where public fear stalls beneficial technological progress. History shows that industries resist safety reforms until disaster strikes. The AI industry still has time to prove it can be different.

Generative AI and LLMs are amazing\! They show obvious value. Just as the takeaway from the Therac-25 incident should not be that software is inherently bad, the takeaway from this post should not be that AI is inherently bad. But both need to be used safely when potential harm is at stake.

Oh, and Three Mile Island? It’s reopening in 2028—with a 20-year commitment from Microsoft to power its AI data centers.[^18]

[^1]:  [https://www.nasa.gov/challenger-sts-51l-accident/](https://www.nasa.gov/challenger-sts-51l-accident/)

[^2]:  [https://www.nasa.gov/challenger-sts-51l-accident/](https://www.nasa.gov/challenger-sts-51l-accident/)

[^3]:  [https://www.nasa.gov/history/rogersrep/v1ch4.htm](https://www.nasa.gov/history/rogersrep/v1ch4.htm) Report of the PRESIDENTIAL COMMISSION on the Space Shuttle Challenger Accident. Chapter 4: The Cause of the Accident.

[^4]:  [https://www.nasa.gov/history/rogersrep/genindex.htm](https://www.nasa.gov/history/rogersrep/genindex.htm). Report of the PRESIDENTIAL COMMISSION on the Space Shuttle Challenger Accident, Chapter 6 title.

[^5]:  [https://www.nasa.gov/history/rogersrep/v1ch6.htm](https://www.nasa.gov/history/rogersrep/v1ch6.htm). Report of the PRESIDENTIAL COMMISSION on the Space Shuttle Challenger Accident. Chapter 6: An Accident Rooted in History.

[^6]:  [https://www.nasa.gov/history/SP-4219/Chapter15.html](https://www.nasa.gov/history/SP-4219/Chapter15.html)

[^7]:  [https://www.wired.com/story/ubers-fatal-self-driving-car-crash-saga-over-operator-avoids-prison/](https://www.wired.com/story/ubers-fatal-self-driving-car-crash-saga-over-operator-avoids-prison/)

[^8]:  [https://www.statnews.com/2017/09/05/watson-ibm-cancer/](https://www.statnews.com/2017/09/05/watson-ibm-cancer/)

[^9]:  [https://www.reuters.com/article/business/amazon-scraps-secret-ai-recruiting-tool-that-showed-bias-against-women-idUSL2N1VB1FQ/](https://www.reuters.com/article/business/amazon-scraps-secret-ai-recruiting-tool-that-showed-bias-against-women-idUSL2N1VB1FQ/)

[^10]:  [https://www.ic3.gov/PSA/2024/PSA241203](https://www.ic3.gov/PSA/2024/PSA241203)

[^11]:  [https://www.nytimes.com/2024/10/23/technology/characterai-lawsuit-teen-suicide.html](https://www.nytimes.com/2024/10/23/technology/characterai-lawsuit-teen-suicide.html), [https://www.nytimes.com/2025/02/24/health/ai-therapists-chatbots.html](https://www.nytimes.com/2025/02/24/health/ai-therapists-chatbots.html)

[^12]:  [http://sunnyday.mit.edu/papers/therac.pdf](http://sunnyday.mit.edu/papers/therac.pdf). Nancy Leveson. *Safeware: System Safety and Computers*. Addison Wesley, 1995\.

[^13]:  [https://www.nytimes.com/2015/11/27/automobiles/50-years-ago-unsafe-at-any-speed-shook-the-auto-world.html](https://www.nytimes.com/2015/11/27/automobiles/50-years-ago-unsafe-at-any-speed-shook-the-auto-world.html)

[^14]:  [https://www.nrc.gov/reading-rm/doc-collections/fact-sheets/3mile-isle.html](https://www.nrc.gov/reading-rm/doc-collections/fact-sheets/3mile-isle.html) 

[^15]:  [https://nuclearstreet.com/nuclear\_power\_industry\_news/b/nuclear\_power\_news/archive/2012/02/09/nrc-approves-vogtle-reactor-construction-\_2d00\_-first-new-nuclear-plant-approval-in-34-years-\_2800\_with-new-plant-photos\_2900\_-020902\#google\_vignette](https://nuclearstreet.com/nuclear_power_industry_news/b/nuclear_power_news/archive/2012/02/09/nrc-approves-vogtle-reactor-construction-_2d00_-first-new-nuclear-plant-approval-in-34-years-_2800_with-new-plant-photos_2900_-020902#google_vignette)

[^16]:  Charles Perrow. *Normal Accidents: Living with High-Risk Technologies*. Basic Books, 1984\.

[^17]:  [https://www.nytimes.com/2024/09/16/business/china-ai-safety.html](https://www.nytimes.com/2024/09/16/business/china-ai-safety.html)

[^18]:  [https://www.npr.org/2024/09/20/nx-s1-5120581/three-mile-island-nuclear-power-plant-microsoft-ai](https://www.npr.org/2024/09/20/nx-s1-5120581/three-mile-island-nuclear-power-plant-microsoft-ai)
