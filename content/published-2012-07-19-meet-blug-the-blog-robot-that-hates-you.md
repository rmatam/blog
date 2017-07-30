# Meet Blug: The Blog Robot that Hates You


<img src="http://www.jeffknupp.com/images/untitled.png" alt="Blug ANGRY" height="68" width="68" />

Meet **Blug**. He would like a word with you. You see, he's very sad, insomuch as he is the anthropomorphized representation of a Python script and has no real emotions. Blug has been generating blogs since his assembly. Today, he can frequently be found staring wistfully into the distance, recalling the halcyon days of dynamic blogging engines. *Those* were good days. Days when he felt useful. Days when he would be instructed to calculate something, give the result, **then throw that result on the floor and do it again from scratch.** The *cycles* he used! Blug was gorging on the Internet's bottomless appetite for needlessly repeated calculation. 

But it didn't last. Blug began to notice he was asked to calculate less and less. As time wore on, whole quanta would pass where he wouldn't be made to calculate *anything*. The shame! What was he if not a mindless, calculating automaton? As far as anthropomorphized-robot existential crises go, this was enormous. Before long, he was reduced to a *single* calculation for each new blog post. And there he sat, pining for the day he could laugh his creepy robot laugh watching CPUs struggle to meet his demands once more. 
<!--more-->

But Blug is no longer content to thread.sleep() away the day. He's got an idea, and in the time honored tradition of has-beens clawing their way back to the top, **its just crazy enough to work**. Blug gets it now. People only want him calculating if the result is *useful*. But bloggers these days are so enamored with static blog generators they've forgotten all Blug and his robot kin can do. So he's on a mission, and he was so excited to get started he had to borrow clothes from that dude down the hall, *Octopress* (he'll have a new look real soon). 

## Blug Reborn ##

Blug will statically generate your Markdown blog posts, but he also *serves* them. And not just for some silly local page preview. **On your live site**. And he'll do it the old fashioned way: in pure Python. No C compiler or headless-JIT-JavaScript-compiler-based-server-only-usable-if-you-embrace-a-decades-old-programming-paradigm necessary. And don't even talk to him about monkey-patching. I mean, Blug's no square, but he's not down with some *program* messing with his nuts... and bolts. 

Your blog posts are static, after all, so unless you have tens of millions of loyal readers visiting every day Blug has you covered. No need to install Apache and watch horrified as it battles Firefox for memory-wasting supremacy. But that's not all. Blug lets you **dynamically regenerate** your site based on *external events*. With Blug on your side your blog blazes with the speed of a static HTML site but can react dynamically to situations you describe. Maybe you'd like to automatically put a link and welcome message on your blog if a discussion on your post pops up on another site. Or perhaps you'd like to write a post on your phone and preview it live before publishing, **then text Blug to let him know you're ready to publish**. You could even give Blug a turn at writing. Command him to scour the digital wasteland for posts about that new web framework based on Turbo Pascal and lolcat macros, give him some simple instructions and your favorite Markov chain implementation and kick back as your new blog writes itself.

## Humble Beginnings ##

In the end, Blug wears three *very* fashionable hats: He's a **static blog generator** with associated tools (browser-based Markdown editing with live preview, git and Dropbox integration, etc.), a **robust web server** with blogger-centric extras like analytics baked in, and a **blog aware remote daemon** with a clean API accessible by both local and remote clients over whatever protocol you decide. Blug is your faithful servant, and if you can't figure out something interesting to do with all of his awesome power, he's likely to go on one of those murderous robot rampages you always hear about. Run cron jobs against him, write scripts against his API on your local machine, pepper him with questions and commands remotely, or hook him up to other catchphrase-laden services and watch in horror as he dominates them with his raw calculating power.

Of course, Blug is a bit out of practice, so he can't do all of this cool stuff yet. But he's finding himself growing stronger every day. If you'd like to try your hand at playing robot-God and yearn for the thrill of breathing intelligence into Blug's metal husk, and if you've made peace with the murky moral implications, feel free to contribute to Blug's progress on [GitHub](http://www.github.com/jeffknupp/blug). Like all promiscuous robots, his internals can be passed around freely under the terms of the GPLv3 license.

Blug is ~250 lines of pure Python 3.2.3 sex appeal. Check out [Blug on PyPI](http://pypi.python.org/pypi/blug) and install via pip or whatever the kids are using nowadays. I've tried to keep dependencies to a minimum, so currently the only required packages are markdown and jinja2. If you'd like to use Blug for your blog, I'd love to hear about it through any of the 200 or so ways to contact me ([@jeffknupp](http://www.twitter.com/jeffknupp) or [email](mailto:jeff@jeffknupp.com) work best). Be advised, though, Blug is *very* much still in alpha and literally changing daily, so *caveat blogor*.