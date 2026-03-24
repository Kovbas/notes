Do you know about [Therac-25](https://en.wikipedia.org/wiki/Therac-25) and [Ariane flight V88](https://en.wikipedia.org/wiki/Ariane_flight_V88)?


I like these cases because they are so typical in software development, but nobody want to believe it happens with them. Usually, it happens when a company wants to save some money on development, takes an old product and tries to run it in a new HW environment.


Our customer got SW from us and started to test it intensively. In particular, on hang ups and reboots. The purpose was to find all possible issues in power management. The system had 2 parts:

- a small processor with power supply and input signals propagating responsibility;

- a main part with much more stuff with applications running responsibility.

Parts were connected to each other via a serial port.


The customer did know about issues in the power part and they wanted to test everything to be sure it works fine. That's why they made a series of tests, when the power part sends signals about power shut down to the application part to notify about a necessity to close apps. After some time the power part sends another signal about canceling the shut down. And everything was fine except one case... When ~300ms was between two signals the whole device shuts down. When they didn't find anything on their power part they asked us about the application part. That was interesting because we were quite sure that we didn't send any signals to shut down the system except particular cases. But a claim is a claim. We had to investigate.


After several days of investigations we found a suspicious piece of old code. It started a thread on receiving a message about power shut down from the power part. This thread did several notifications and had to stop in ~300ms. And looked like it didn't start anything else and any other activities or especially requests to the power part. When the code was commented out the mentioned shutdowns stopped.


We had decided to investigate more to understand what exactly activity initiated shut down. And we found, of course. But it was not the most interesting thing in the case.


The most interesting thing happened when we started to investigate why this code was added. And we found (surprisingly). At the previous version of the product the power part had a bug that it didn't shut down the power by the request. Meanwhile, when the request on shut down was sent to the power part, that part notified about shut down back. And if shot down didn't happen then the application part did another request, which definitely led to the system shut down.


That's why I like those two cases from the post's top. You always have to be critical to SW. Especially, when it is ported from another HW.


Another lesson from the story is in fact that all such critical requests should be implemented in a dedicated block. It could help to find a caller faster.


And there was another lesson. I will write about it next time.




