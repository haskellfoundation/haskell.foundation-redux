*Joachim Breitner (0:00:15)*: My name is Joachim Breitner, and together with Andres Löh, I’m interviewing Avi Press. Avi is the founder of Scarf, but uses Haskell to analyze open source software is used. We’ll hear about the kind of shitstorm telemetry can cause when correctness matters less than fearless refactoring, and how that can lead to statically typed Stockholm syndrome. 

Hi, Avi. Good to have you on the show.

*Avi Press (0:00:39)*: Excited to be here. Thanks for having me.

*JB (0:00:41)*: So, the first thing I’m always curious about is, how did you get into Haskell?

*AP (0:00:46)*: Yeah. So I got into Haskell around the time I had my first software engineering job out of college when I was writing a lot of Java and Python and running into all the frustrations of a dynamically typed language in production and just hitting these constant small bugs that I was feeling like in Java I wouldn’t be dealing with. And then in Java, I was dealing with other problems of Java. I was looking for a language that would satisfy all the things that I wanted. And what it turned out I wanted was a language that was functional, because I really liked Lisp. It was my first language I ever learned. But I really wanted a static type system as well.

*JB (0:01:25)*: How did you get to learn Lisp as your very first language?

*AP (0:01:28)*: It was like the introductory Computer Science course that I took at Berkeley.

*JB (0:01:31)*: Oh, okay. 

*AP (0:01:31)*: Was taught in Scheme.

*Andres Löh (0:01:33)*: Which year roughly are we talking about here?

*AP (0:01:36)*: This was 2010. 

*AL (0:01:38)*: Okay.

*AP (0:01:38)*: 20 – or, oh, sorry. Well, 2010 was when I was starting programming. Got into Haskell in like 2014, beginning of 2014. And so, yeah, like I really liked functional programming, and I knew that I wanted to really dive into that more deeply. But I also really like static typing. And so, I was looking for a language that would satisfy both of those things and came across Haskell, tried to learn it, and got super stuck, and then put it down. I think I did learn you a Haskell for great good, get to the monad part, and it rocked my world. And I got a little bit stuck and set it down, and then came back to it a few months later.

I did this a couple of times before it really stuck with me once I could actually sit down and build a side project with it and just work past the conceptual difficulties that I was having, kind of grokking the language really. But once I really got going, it was just a really magical programming experience. Like just wrestling with a compiler and then just having things fall into place and start working, I was hooked from there. And then it just quickly became what I wanted to write everything in. That’s where I am today, where pretty much any coding I do, I do it in Haskell. 

*AL (0:02:55)*: So, when you got across this point, which you had trouble with, you think it’s just that you were more dedicated during that final attempt where you managed, or did you do something different or found a different resource to learn from or –

*AP (0:03:15)*: I think it was more that at the beginning I was like reading a lot about Haskell and not writing it very much, and then when it was time to really wrestle with the more practical things that I needed to do. And I remember at the time I needed some state in a web server. I needed to do some, actually have something mutable that I was reading and writing from. And once I figured out how to do that, it really clicked, and it was like, “Oh, okay, actually, you can use an IORef or whatever you want to do.” And it just became a lot easier to have a mental model of, and then things like monad transformers became a lot more, I don’t know, easy to understand in reference to an IORef, which I found to have a fairly understandable API when I didn’t get it yet.

*JB (0:04:05)*: Well, what happened then? You didn’t just play around with Haskell for some fun. 

*AP (0:04:11)*: Yeah. Okay. Fair, fair, fair. Yeah. At that point, I’m really using Haskell for my side projects, and I was working at Pandora, which is a big music streaming service in the United States. And I pushed Scala there because it was the closest thing that I could use at work in a way because we were like really a JVM shop over there. And so, I was doing a lot of Scala at the time as well, continued to write Haskell in my spare time. But after Pandora, I bounced around to a few different startups and was thinking about starting my own company essentially. And once I had the freedom to just pick my own tech stack and I had been writing Haskell for several years for just various side projects and libraries that I had been working with, I felt it was finally time to really use Haskell in production for real. So, I’m the founder of a company called Scarf, which does usage analytics for open source software, and the whole backend is Haskell. So, it’s a lot of data analytics, package registry, gateways, and things that can sit in front of packages and containers. And we use Haskell to do a lot of that. And that’s where I really started getting much deeper into Haskell and all the good and bad parts of it when you’re using it every day and using it in production at scale.

*JB (0:05:28)*: Can you tell us a bit more what Scarf is doing? 

*AP (0:05:30)*: Yeah. So we work with lots of open source projects, companies that commercialize open source software, open source foundations, and really anyone that wants to know about like, how is the software getting used in the real world? And so, for businesses, our customers, that’s often what companies rely on the software, who can be potential customers of whatever our commercial offerings are, and using the open source as a means of actually understanding where your commercial opportunities are. We also contribute our services to haskell.org. So, we help with usage metrics for web analytics for the haskell.org website and then surfacing all the companies that are reading about Haskell. We’re actually currently, right now, by the time this comes out, it might be done already, but working with the GHCup team as well for better metrics around how GHCup is being used. And that has a lot of interesting effects, I think, where we’re helping haskell.org better understand really who the users are and where the growth opportunities are. And from talking to some of the folks at haskell.org, I was really surprised to learn, it was surprising to them how many universities were reading haskell.org. And I think the light bulb moment happened of, “Oh, maybe we should be working with universities more directly given that this is where a lot of Haskell traffic and adoption is.” And when I heard that, I was shocked that that was a revelation. 

*AL (0:06:52)*: I’m also a bit surprised, I must say. I mean –

*AP (0:06:55)*: I was too, and I think this is the kind of thing where, in the open source world, we just have no data about a lot of this stuff. And then when you finally have even a little bit of it, a lot of things that may seem very obvious in hindsight, they come up, and you can actually do something with that.

*AL (0:07:13)*: But how did we get from Haskell is a purely academic language that is only used at universities to being surprised that universities are reading our website?

*AP (0:07:25)*: I don’t know. I’m not sure. 

*JB (0:07:28)*: And I find it more surprising to draw the conclusion that, oh, the universities are using Haskell. We should focus on the universities rather than we should focus on all those who are not using Haskell already. 

*AP (0:07:38)*: Right. I mean, it’s a little bit of both, right? I think we very much need to be putting a lot of effort into where we’re doing well already as a language, I think, and making sure that we continue to do what’s working and do it better. But then also, there’s a lot of opportunities where Haskell is not hitting a home run all the time and you want to do both. And I think that this is the kind of thing that I want to see more of in the Haskell ecosystem personally.

*JB (0:08:08)*: So are you only tapping into, I guess, passive analytics like web services and logs and things, or do you also do telemetry?

*AP (0:08:18)*: We do telemetry as well. Telemetry in code is not for all open source projects. I think it’s appropriate in some cases and less so in others. So, what we do is we offer a more broad variety of ways to collect this kind of data in the open source world. And so, it’s things like tracking pixels that don’t use cookies, so you can put them in open source documentation. And then anywhere the docs get loaded, maintainers can see how those things get read. Sitting in front of artifacts is a really clean way of doing this as well, because then you don’t have to phone home or send telemetry anywhere. You can just passively collect this information and then, of course, telemetry being another way as well. And so, when we blend all these things together, we can start to give package maintainers a bit more understanding of how their software actually gets used. 

I think I skipped a few steps in terms of my journey with Haskell from the very beginning until starting Scarf. But that was a situation that I was in as a maintainer, is that I knew that big companies were using some of my tools that were written in Haskell. And all I had was like download counts from Hackage and download counts from Docker Hub, and I’m trying to make this call of, “Do I quit my job to build a business around some of these tools?” And trying to make that decision without any of the real information to make that call was very tricky. And so, that’s what got me started down this path and continue to want to use Haskell to do it as much as we can.

*JB (0:09:49)*: Yeah. I can understand the wanting to know this, and I’ve been brought up socially in the Debian world of open source software, where of course every kind of tracking is very contentious. And so, my projects, I don’t even have like a simple page lock running. I have no idea how much people are using it. The best indication I have is that for one of a project, there’s a video that has 400,000 views. Now I can guess like, is 1% of the views actually looking at the software? Is it two? That’s the best I have. So, having some good idea of what’s happening there is I can see how that it’s really useful.

*AP (0:10:27)*: Yeah. And even the Debian project has the popularity contest is I think what they call it. And so, I think from a lot of different open source communities, there’s the acknowledgment that this can be useful, but there’s less consensus on the right way to do it. So, what we are basically doing is trying to navigate that as best we can of like, what is the right way to do this? And what that’s turned into looking like is needing to have a variety of ways to collect the data, a variety of ways to opt out of analytics if you’re a user. We have to delete all the sensitive data as soon as we can. So, we’ll look up an IP address metadata and delete the IP address and these kinds of things. And so, there’s a lot that you have to do to do this correctly, I think. And Haskell helps us be very confident that that we’re handling data how we expect to, and that we don’t break things. So, it’s all good.

*JB (0:11:18)*: And I know everybody wants to start to hear more about Haskell, but I’m still curious about the business in the sense. Did you ever run into a case where you, in hindsight, said, “Okay, this stepped on too many toes,” or you got a shitstorm because some project included telemetry?

*AP (0:11:36)*: Oh, yeah. Yes. I don’t want to derail us too much from Haskell, I know, but maybe I think this is a good story. The first tool that we launched that really got traction was a JavaScript package called Scarf-js. All it would do, you add it as a dependency to your NPM package, and it would fire a post-install hook to us basically just sending the package version that got installed. That’s it. A subset of what NPM collects already. It got into some very popular React libraries, like React Table or React Query Final Form, like some of these very widely downloaded packages. The maintainers really liked it. 

About six months or so after they started using it, some of their communities were really demanding. They removed the telemetry. It erupted into this big discussion in the Reactiflux Discord, if you’re familiar with that community. And basically, what we learned from the people who really didn’t like this was we were collecting analytics at a time that was unexpected by end users. It wasn’t the data that we were collecting. It wasn’t the fact that maintainers would have this information finally, even though NPM already had. It was that I didn’t expect to send data to Scarf when I ran NPM install. And this surprised me. 

And so, with that realization, we came back and we said, “Okay, well, what about tracking documentation of the open source with like a pixel? That seems more subversive, if anything, but it falls more into your expectations.” And people said, “Yeah, I don’t really care if you do that. That’s fine. I’m used to that.” And so, all of these JavaScript maintainers that were using our JavaScript package all started using the tracking pixels instead. And so, they still got something, even though some of them stopped using Scarf-js. 

But then three years later, that sort of telemetry is actually becoming a lot more popular, interestingly. So, there’s like shifting attitudes around this, I think. As people start to come to expect – as people’s expectations start to shift, what we can do in a way that doesn’t ruffle feathers, I think, also changes over time as well. But it really can help the maintainers just be a lot more data-driven and less burnt out because they’re spending time on the right things and being able to do a better job of knowing what not to work on and what to prioritize. And I think that’s a big problem that we face in the Haskell ecosystem, is that it’s very community-driven and resources are scarce. I think that, yeah, how we prioritize our efforts in the Haskell community, I think, is definitely not a trivial problem. And there’s a lot of disagreement about it. And so, it’s an interesting topic.

*AL (0:14:11)*: So did I get it right that when you started with Scarf, it wasn’t really a question for you which language to use? You wanted to use Haskell at that point, and that was a given? Or did you still make a decision between multiple possibilities or options? 

*AP (0:14:30)*: I definitely had a little bit of doubt about it. I could have written this in Go or something like that, or some much more –

*AL (0:14:38)*: Yeah, sure. That you could have this.

*AP (0:14:40)*: Yeah. And I mean, I had that thought. I definitely had this internal calculus of like, how do I choose this? And at the time, I had all these justifications about like, well, I want to be able to refactor confidently, I want to have correctness, all these kinds of things. But upon a lot of reflection about it, yeah, I picked Haskell because I like it. That’s it. I could pretend there were deeper things within that. But really, I wanted to write Haskell. It was finally my own choice to do that. And so, that was really what swayed me to do it. And I’m really glad that it did.

*JB (0:15:16)*: Is it just you that has to suffer from the consequence of this decision or –

*AP (0:15:20)*: No, not at all. 

*JB (0:15:22)*: People are working with code base now.

*AP (0:15:23)*: Yeah. We’ve got about 16 people on the team. The last time I looked, it’s about a 350,000-line Haskell code base. So, it’s a pretty sizable code base at this point. It’s not like humongous, but it’s pretty sizable. And so, yeah, I have not written much Haskell at Scarf in quite a bit at this point, which both is a good thing and a sad thing. 

*AL (0:15:46)*: How old is the company now? 

*AP (0:15:47)*: Four years old? 

*AL (0:15:49)*: Four years old.

*AP (0:15:49)*: Or I guess, yeah, three and a half, four. Yeah. I’ve been working on it for five. Had the first anniversary. That first commit was five years ago now. 

*AL (0:15:56)*: Yeah. Okay. So then perhaps tell us a little bit about what you want to start with. The things that went well, or the things that did not go so well?

*AP (0:16:05)*: Well, let’s start with what went well. 

*AL (0:16:07)*: Okay. 

*AP (0:16:08)*: I mean, one thing that was just great about it was just that it was really pleasant. It’s fun. Haskell is a fun language to work with. The community is great. It’s full of really smart people. When we first started hiring engineers, the people that want to write Haskell are great and very smart. And the ecosystem is lovely. 

*AL (0:16:27)*: So you did not have problems with hiring?

*AP (0:16:29)*: No, at least not at first. 

*AL (0:16:31)*: Okay. 

*AP (0:16:32)*: We literally would just post a job listing on Hacker News, and the next day, we would have more solid applicants than we needed. It was super easy. It was great. And Haskell is a really great first pass filter because, I mean, the learning curve works to your advantage here, where the people who took the time to learn Haskell care about the craft of programming in general. Certainly exceptions to that, but I think that –

*AL (0:16:58)*: Yeah. I think, in general, I also very much agree. Had the same experience.

*AP (0:17:04)*: Yeah. And I think at the very beginning of working on Scarf as well, I think one of the huge benefits is that at the beginning of starting a business, there’s so much that you don’t know. You don’t know really exactly what your customers want. You don’t even really necessarily know exactly who your customer is yet. You’re trying to figure all these things out. And so, your requirements are a very quickly moving target. And so, the correctness aspect just doesn’t really matter at all, because who cares if you match the spec when your spec is wrong? And the spec is going to be wrong for a long time as you figure out what the specs should be. And so, we were changing huge parts of our system very quickly and very confidently. And I think that agility for an early-stage company is so important. And how quickly you’re making those moves and how the friction to doing so all is just so crucial. I talk about the clock speed. If the company is a processor, what is the clock speed of that process? And the faster that cycle is, the more chances you have to get it right. I think if I’m going to say one thing about why Haskell was great for us, it’s that. It let us adapt quickly. 

*AL (0:18:17)*: So you would say Haskell is generally living up to this promise of fearless refactoring? 

*AP (0:18:22)*: Absolutely. Yeah. Anytime that I would do it, it was literally like I looked at my types. How have I modeled this domain? And here’s my new domain model, and now let me remap my types and then just follow the compiler messages to success, and I was done. And that is just such a – I don’t know. I’ve done big refactors in Python before.

*AL (0:18:47)*: Somehow Python is always the language that is being used to contrast with.

*AP (0:18:52)*: Right. Yeah. I mean, I think it’s the epitome of the problem, I think, Like a big Django app and it’s like, “Oh, we need to do surgery on this big Django app and it’s live, and these kinds of things.” And ah, gosh, it’s just like you change one thing, you’re like, “Well, we’re on the test, the test pass. I think it should be fine.” But yeah, lacking that confidence makes it really hard. And with Haskell, we just didn’t worry about it as much. 

And the other thing here too is at the very beginning, you can do a lot more with fewer tests at first. And I think that quickly, that only takes you so far. But with Haskell, it takes you further. And that’s good, too. It’s a very practical thing for a startup when you can just reason about your type safety and understand the code base, and just move forward quickly.

*JB (0:19:44)*: What kind of Haskell tech stack do you actually have? Just a Snap Server doing something? What are the components on interesting technologies?

*AP (0:19:53)*: Yeah. Let’s see. So, this has evolved a lot over time as well. Today, I think we have a good chunk of our API as a Servant Server, and our gateway, which needs to be very, very fast and very, very specific, is using WAI directly. What else can I say about the Haskell stack? We do MTL for the kind of monad stack. So, we do monad transformers. We just have that one big application monad that a lot of things end up sitting in. And I think we can come back to that later because there’s some good and bad parts of that. I guess, what else is helpful here? Like, libraries that we’re using or tool?

*JB (0:20:34)*: Yeah. 

*AP (0:20:35)*: Okay. We started with Stack and have moved back to Cabal. We build the developer environment with Nix, and then the rest gets built with Cabal. And then we build Docker images that we will then deploy when we push that to production. That’s the general gist of the Haskell stack there. It’s largely just like a set, two big web applications basically that we ship a lot of data processing. 

*JB (0:21:02)*: What’s your persistence layer?

*AP (0:21:03)*: We’re using Beam for the ORM on top of Postgres. Postgres and Kafka are the two data – well, Postgres, Kafka, S3 are the main data stores that we use. Beam sits on top of the Postgres thing. I actually don’t know what we do for Kafka these days. I can find out. We can put it in the show notes.

*JB (0:21:25)*: I guess I find it most interesting to hear that people are using Nix and they don’t even think that it’s worth mentioning anymore.

*AP (0:21:32)*: Yeah. I mean, I think that it’s definitely becoming a little bit more commonplace for Haskell projects these days. We actually were using Nix quite a bit more and backed off of it a little bit. We were having it build the whole, like everything was getting built by Nix, which I think was a little bit much, it turned out. Anytime a library got bumped or something, some dependency needed to be updated. There were just a lot of constant breakages and headaches that came with that. But once we backed off to just having it build the developer environment, that seems to have been working really great for us.

*AL (0:22:08)*: Yeah. So, there are lots of follow-up questions that I have about your choices, but perhaps, let’s start actually with the observation that if you’re saying both Servant and Beam, then I guess you’re not afraid of using the type system to its fullest. I mean, sometimes there are these discussions about like, should you be using simple Haskell or should you not be? I mean, clearly, with these two choices, you have made a statement, whether you’re happy with it is a different question. 

*AP (0:22:40)*: Right.

*AL (0:22:41)*: But perhaps you can say a little bit more about how you made these choices in the first place because, I mean, often people are saying this is one of the first big problems, right? How do you pick a database library? How do you pick a web framework? And then there are many different options there. So, how did you make these choices?

*AP (0:23:01)*: Yeah. These are good questions. And I think that the criteria for this stuff is also a moving target a little bit, as well as things shift around in the ecosystem. So, for Beam, which is, for those who don’t know, just an ORM that I think mostly targets Postgres. The reason that I liked Beam is that it didn’t use Template Haskell. I wanted to just write plain records and use plain records. And then lens, like the basic lens operations, worked very nicely, and you could – it was a fairly ergonomic way to get started with it. 

It was a somewhat similar story with Servant, where the goal originally was like, let’s just get something up and running pretty quick and not worry about the specifics too much. And I was familiar enough with Servant where I could just get going with it quickly. But I think back to part of your question there, I do think that getting what you can out of the type system is a good thing. But actually, I am one that advocates more simple types when you don’t need anything fancier. At the time—this was five years ago now where I was making some of these decisions—Template Haskell was a lot slower back then than it is today. There have been notable performance improvements with compilation of Template Haskell that have made that a little bit more of a reasonable choice than it is today if you are picking something with Template Haskell.

*AL (0:24:31)*: I guess most of the drawback of Template Haskell is that you have compiled time degradation, right? That you have like worse recompilation checking and so on and so forth. And the runtime performance of Template Haskell code is generally very good. 

*AP (0:24:46)*: Right. I think that another piece of my aversion to Template Haskell also was just that the error messages you get from the compiler are sometimes a lot harder to understand. And so, I had experiences like when I was first getting started with Haskell and I was trying out Yesod, and it was just trying to make some super simple models and like, “Why is this not compiling? I have no idea what this error message means.” And it was just really challenging to understand what was going on. And so, that gave me a pretty long-term aversion to Template Haskell. 

And so, pretty much the only place – I’m trying to think. Yeah, I mostly wanted to avoid Template Haskell altogether, except I was into deriving generic for types and stuff. And that was someplace where I was using a little bit of Template Haskell so I could derive the lenses and these kinds of things. And I think that’s one of those places where I feel like I wish that Haskell made some of these basic things a little bit more ergonomic, like dealing with records and stuff, so you don’t need to reach for templates as often. But I know that records have changed quite a bit, especially since I’ve been writing as much Haskell. Yeah.

*AL (0:25:56)*: Yeah. I mean, I’ve actually been always a little bit skeptical, or I always said I don’t actually find the record system of Haskell that bad as everybody else finds it. And I was not a strong proponent of the whole OverloadedRecordDot. But now that it’s there, I must say I’m a convert. It is actually quite nice, and I am using it.

*AP (0:26:21)*: There’s a lot of things I feel like we’ve tricked ourselves into thinking it’s okay in the Haskell world. I think like, I mean, no shade against tools. When GHC IDE was really popular, I think a lot of people were saying, “Look, Haskell has the best IDE story. I have this really quick compilation in my editor.” And I want to say just like, “Are you crazy? Have you used other tool, like other languages in the toolchains? This is primitive in comparison to what we’re doing.” 

*AL (0:26:51)*: But I think, to be fair, the history there is that there were things like ghc-mod.

*AP (0:26:55)*: Right. 

*AL (026:56)*: And ghc-mod was already much more ambitious, but it never worked. There was a new compiler release and it did not get updated for half a year or something like that, or you put it on a slightly bigger project and then it doesn’t work. And ghc-mod was an amazing effort. And I mean, nothing against the author at all. I think it was fantastic. But the unique selling point of GHC IDE was that if GHCi works, GHC IDE works. It basically always works. And not that it is better than IDE tools of other languages, but simply that you have something that actually works. 

*AP (0:27:35)*: Right. Right. And I think it’s because our bar was so low for where the tools should be that we were happy with something that was getting the job done somewhat okay. 

*AL (0:27:45)*: Right. Yeah. Exactly. 

*AP (0:27:46)*: And so, I think – yeah, I don’t know where I’m going with that.

*AL (0:27:50)*: I think I generally agree with your points. I mean, but yeah, let’s get back to Servant and Beam once more. 

*AP (0:27:57)*: Yeah. So, Servant is really great when you don’t need to do anything complicated because the types that are given and the APIs are, I thought, very intuitive to put them together and get something working. Once you need to start doing some more complicated stuff with your authentication, oof. You’re going to have some really complicated types. The complexity on step two really ramps up super quickly, in our experience. I think that maybe others have had a different time with some of those pieces. But I think, if I remember correctly, we wanted to have an API that would do like bearer off token authentication, but there was something else that needed simple off. And when you want to mix and match different authentication types with APIs, it quickly gets really complex. And that was tricky for us. 

*AL (0:28:49)*: Yeah. I think it is probably actually all doable. 

*AP (0:28:54)*: Oh, totally doable. 

*AL (0:28:55)*: But I can certainly see why, like, if you’re using something like Servant, which it is basically to be completely fair, I guess to some extent a research project, right? How far can we push the type system and get what we want? That if you have new requirements, you’re probably the first or one of the first persons in the world who is figuring out how to actually do it within that set of constraints. And that is totally non-trivial. 

*AP (0:29:23)*: Yeah. And I think that’s kind of the thing here, is we have a lot of tools that have really good theoretical foundations. They were really well set up to build world-class toolchains in the Haskell ecosystem. And I think we’re at this point where we can continue to invest in that kind of developer experience and polish of those day-to-day workflows. And I really want to see more of that in the Haskell world. We could have the best web framework in the world. We could have the best ORMs in the world, I think. There’s a lot that Haskell gives us that can be a real driver of an incredible developer experience. And so, yeah, you can get off working in Servant to do whatever you want, but you might be waiting around a long time. 

*AL (0:30:11)*: It would be nicer if it was just working. This is where I’m always not sure myself what the right answer is, and perhaps it can give you a viewpoint on this as well, because sure. I mean, wouldn’t it be nice if we could agree on, say, a web framework like Servant or potentially another one and then say, “Let’s put all our effort in this to make it really nice for all the advanced use cases as well”? But on the other hand, if we had said that in the beginning, we would have never got to Servant in the first place.

*AP (0:30:43)*: Right.

*AL (0:30:45)*: So, what is the right point in this design space? Because on the one hand, Haskell is really great because we are always striving for more and we are very perfectionistic, and we are saying, “Oh yeah, this is really great, except it is not great in this area, so let’s do yet another library that does this better as well.” But at the same time, we’re never saying, “Okay, let’s consolidate and actually put something into a state where it’s actually usable for many, many use cases and is professional and complete and maintained well and so on and so forth.” But how can you have both? I mean, this is – 

*AP (0:31:24)*: I think the answer is we need a bigger community. We have to make a trade-off of where we’re putting all of our resource. Like collectively the Haskell community, where we’re putting all of our resources. Are we putting it into more academic features in GHC? Are we putting it more into ensuring backwards compatibility of the compiler? Are we putting it into developer tools? Where is that effort going? And I don’t think – I very much agree. We don’t want to lose the things that make Haskell amazing. All the innovations that we have, all the really great work that’s being done on the compiler, I don’t think we want to lose that. But I think that Haskell’s getting to a point where it’s being used in production by more companies. And it’s a critical moment for Haskell, I think, where we need to decide, are we going to invest? How are we going to invest to continue that growth? 

And so, I think that ultimately, we need to just do more to onboard more people to Haskell and make that journey a little bit smoother so we can get more people to keep making it smoother. That flywheel needs to get going. We’re not quite there yet, I think, but that’s a lot easier said than done. And it’s not going to happen overnight. And it’s not going to happen unless we decide that that’s what we want. The community, if we make this conscious decision that we want the Haskell ecosystem to grow, that’s a different set of trade-offs than where we currently are. That’s not the goal right now, but I think that should be a goal right now. Or maybe you disagree with that. I mean, I know it’s some people’s goal.

*AL (0:32:54)*: I don’t know. I mean, I think you’re probably right as far as that, like hardly at any point the Haskell community as a whole agrees on something. 

*AP (0:33:06)*: Right.

*AL (0:33:06)*: So, I guess probably it is not that everybody agrees that it is a goal to grow the community. But I would imagine that it is actually something that many people would agree to, and that it is not something that people are actively fighting against.

*AP (0:33:22)*: Right. I don’t know to what extent this is the case in the Haskell world, but some communities in other parts of the open source world, not chiming in on something is consensus for something. So, if people are not present on something and the people who are showing up to do something, they’re not going to be blocked by the people who are not chipping in on it. And I don’t know if that’s the case here. Or maybe it’s partly true in some cases and not in others, but I think it shouldn’t be the case that pushing forward on the academic research sides of Haskell should get in the way of people wanting to push the industrial use cases more forward. But where those two things really collide is when we break backwards compatibility of things. That causes a lot of pain, I think, for a lot of the people that rely on Haskell. And when it’s really late at night and you’re upgrading *aeson* in your code base and you just want to go to sleep, that’s the time where this impacts our lives as Haskell developers. 

*JB (0:34:25)*: Yeah. So are you reporting actual experience right now, or is it something that doesn’t actually bother you personally too much, but you keep hearing from others?

*AP (0:34:34)*: We dealt with the *aeson* thing very much so at Scarf. That one was not one that I was doing myself. By then, I was already not coding as much at Scarf, but I definitely had times where, yeah, GHC upgrades really took a lot of time, or needing to patch the Haskell Stripe library took a lot of time, and it was using type families. This was a while ago as well. The library took 20 minutes to compile every time I wanted to make a small change. These kinds of things, they’re paper cuts, but they’re really deep paper cuts. And in an environment where you are trying to focus on your business, I don’t want to think about Haskell very much. I’m trying to just get my work done and focus on other things. Yeah, it’s hard. It’s sometimes hard to write Haskell and have it like not have to be really top of mind about Haskell stuff, if that makes sense.

*JB (0:35:30)*: So, from your experience, it is an actual concrete issue that Haskell compiler and libraries tend to break compatibility team more often than you would wish?

*AP (0:35:38)*: A hundred percent. Yes. Very much so. Over time, this is something – I think it was worse a little while ago, I think. We were complaining about it a lot more like a year ago than we complain about it right now. But it still is very much a thing that we deal with.

*JB (0:35:53)*: On the list of things that you have to deal with that you wish you wouldn’t have to deal with, where is it and what else is on the list?

*AP (0:36:00)*: Yeah. I think the more common one that most people will say is the library ecosystem, I think, is full of some really high-quality libraries, but still in the scheme of things, is very small. And so, one thing that we’ve seen a number of times is Haskell dictates our choices in other technologies more than I would like. So, we need to pick a new database for some – we need to pick an analytics database at Scarf. And we were making this call about like, “Well, what do we go with?” And who has the good Haskell bindings became one of the big topics. And that’s not a good reason to choose a database, but you really don’t want to have to write it yourself either. 

And so, for us, we use ClickHouse for our backend analytics. And the ClickHouse libraries that were out there did not fit our needs for a few different reasons. And so we wrote our own. Not ideal. There was a time where we were briefly using RocksDB and the existing fork was super out of date. And so we had to fork it and do a lot of work on it. Even having to make this decision every time is tiresome and time-consuming. And it’s tough because you just need a lot more people, you need a lot more hands to just build out the library ecosystem more. And so, the amount of times you have to do things yourself, I think, was something that was very much like – I knew we were going to run into that, and we went ahead anyway. But that’s one of the consequences of that choice.

*JB (0:37:30)*: So, what I’m hearing is if you’re building a service to startups and you want to reach all of them, better provide Haskell bindings because otherwise, you won’t use simple Haskell for using once.

*AP (0:37:39)*: Yeah. And you know what? As the Haskell ecosystem grows, we’ll have more of a reason to do that. And I talk to vendors all the time about their code scanning tool, their security tool, whatever it is. I’m like, “You can’t sell to us. We use Haskell.” The reps are always like, “You use what?” And I’m like, “I don’t even want to bother with this conversation right now,” that kind of thing. I’ve had that conversation so many times with people, which is, it’s fun and it’s funny that I –

*AL (0:38:04)*: Can I use it as an argument to get out of unwanted discussion? 

*AP (0:38:07)*: Very true. That’s a great point. Yeah. So, I think the library ecosystem is a big one and another reason why I want the community to just focus on growth a bit more and bringing on more people into the ecosystem. For anyone listening, I think you could start a consulting group that just built Haskell libraries for companies and startups at this point would probably be a reasonable business. But yeah, I think there’s just a lot of libraries that need to exist that don’t exist yet.

*JB (0:38:34)*: I can think of a very, very good Haskell consultancy company right off my head, but right now they’d probably be happy to – 

*AP (0:38:38)*: Yeah, I’m sure there are a couple. Yeah. I’m trying to think of other things that – I mean, there’s things like it’s sometimes hard to know what the right way to write Haskell is. In certain cases, there’s like coding standards I think would help out more. I think just like the general complexity of certain aspects of the language have definitely been a thing that we run into where you have really experienced Haskell developers that are not really sure when you need to foldl versus foldr versus foldr’ or whatever it might be. And there’s just a lot of complexity to the language.

*AL (0:39:16)*: That should ideally not happen.

*AP (0:39:19)*: Yeah.

*AL (0:39:20)*: I’m trying to teach this in my introductory courses. 

*AP (0:39:23)*: Nice, nice.

*AL (0:39:24)*: So hopefully, everybody who has done Haskell for a couple of days knows, but I’m with you. I’m surprised by this as well because sometimes we’re interviewing people at our company and they don’t know, and they say, “I’ve been writing Haskell for 10 years, but I don’t know whether to use a foldr or foldl’ here, or even the difference between foldl and foldl’ and something like that.” And yeah, these are important aspects of understanding Haskell. And I think to some extent, it’s an artifact of the history of teaching Haskell that teaching Haskell at universities, also if I think back at my own time when I learned Haskell, often went along with, “Oh, we are going to teach you this exotic programming paradigm where you don’t have to care about how things get evaluated and how things perform at all. You just write declaratively what you want, and it magically happens.” And if you teach people that way, they might get enthusiastic, but then they also take that to heart and don’t care about performance. And at some point later, there is this bad awakening that, well, you have to think about performance after all. And then you have to go back and understand where all the holes and gaps in your knowledge are and what you have to learn. So, I’m a strong believer in teaching Haskell in such a way that you care about these things to a reasonable extent from the very beginning, right? 

*JB (0:40:57)*: Speaking about the performance issues commonly attributed to Haskell, and since you listed all the other things people typically complain about Haskell already, how was it with space leaks? Did you have any, or is at least that not a problem?

*AP (0:41:09)*: We have had them before. 

*JB (0:41:11)*: Okay.

*AP (0:41:12)*: It hasn’t been – space leaks has not been a huge problem for us, but when it happens, it’s like, “Oh my God. How do we even go about troubleshooting this?” And the tools for it are definitely not anywhere near where other languages are for the runtime troubleshooting, and Haskell is definitely trickier and less built out than if you’re doing Scala, for instance. But yeah, space leaks has not happened too much. Otherwise, the performance that we get at Haskell is great. Our gateway that sits in front of Docker containers and binaries and packages has to be really, really fast. And it is really, really fast. And we didn’t really have to do all that much to achieve that. We actually started that project as an Nginx, like full Nginx application. And so, we’re writing Lua to script some of the things that we needed to do and we were hitting all sorts of issues, and we internally really debated like, should we rewrite this in Haskell? It might work great. We might hit all the reasons why people don’t rewrite Nginx from scratch. But it’s been working out really well for us so far. The performance, just out of the box, gives us what we need. The confidence and the correctness has also been really great, and it has had excellent uptime and reliability as a result. 

*AL (0:42:27)*: That’s nice to hear.

*AP (0:42:29)*: Yeah, I mean, having compiling down to native code is just such a huge advantage, I think, over some of the other functional languages.

*AL (0:42:36)*: So you said at some point when I asked about hiring initially, you said you didn’t have problems with hiring at first. 

*AP (0:42:42)*: Yes. 

*AL (0:42:45)*: Do you want to say more about that or –

*AP (0:42:47)*: Yeah, there’s a few things about this. I mean, one is that I think, if you have a team of Haskell engineers, you are almost forced to be a remote company. You’ll have a very hard time hiring them where you live. And so, at the beginning when I was doing this, it was like the very beginning of COVID, so that was something that I was totally fine with. But later on, there was definitely a desire to hire Haskell engineers in a certain place. Maybe we want to spread our time zones so we get different on-call schedules covered better, or whatever it needed to be. And if you want to hire a Haskell developer in a specific location, the talent pool is just not big enough to reliably do that, I think, if you need to do it quickly, in our experience so far anyway. It’s not impossible, but just harder.

*AL (0:43:29)*: I would have thought that if your criterion is time zone, for example, then that is still wide enough.

*AP (0:43:37)*: Yeah. I mean, but sometimes if I wanted to hire people local to me, that would be really tricky, I think.

*AL (0:43:42)*: Yeah. If it’s a particular city, then sure. Absolutely

*AP (0:43:45)*: Yeah. Time zone has made that easier, for sure. And like now, we have engineers in Germany and Finland. So, it’s all over, but it forced our hand into that in a way that I would’ve liked the choice a little bit more. And then I think the other difficulty that we don’t have now but I foresee it is, if I had to hire 30 Haskell engineers right now, it’d be hard. It’d be hard to do. If I had to hire 30 JavaScript developers, that would not be very tricky. And so, I think it’s one of those things where, if you’re at a startup that might have to grow really quickly, Haskell might be a headwind to do that. But you don’t always need that. That’s not like a criteria that everybody has. It’s just a thing that Scarf is on a trajectory where we might need to do that at some point of like, “Oh, we need to hire a bunch more people right now.” And we could be in a tricky spot.

*AL (0:44:38)*: Have you ever considered so far, probably not like gathering from this, but just hiring good developers and then teaching them Haskell yourself? 

*AP (0:44:49)*: Yeah. I think the tough thing is that on a really small team, you just don’t really have the resources to meaningfully coach someone from zero to Haskell proficiency while also moving quickly in other parts. And so, I think this is more of a startup thing, but it’s just really tough to do that.

*AL (0:45:09)*: It would just be so nice because you could actively grow the Haskell community while –

*AP (0:45:16)*: Yeah. And I think if we do the follow-up discussion in five years, I might have a very different opinion on that. If we were Jane Street size or Mercury size, I’d say, yeah, very much so. We should just hire functional programmers and train them on Haskell. And I think that that would be a totally workable solution. It just doesn’t work for us right now to do that. 

*AL (0:45:35)*: Yeah, no, that makes sense.

*AP (0:45:36)*: Yeah, I’m trying to think. Those were my main desires for the Haskell world from a startup perspective, I think.

*AL (0:45:42)*: These are all good points. But I also gather that, in summary, there is a lot of good and bad, but you are still quite happy with the choice.

*AP (0:45:49)*: Yeah. I think I would make the choice again. If I went back, I would do it again. However, I think that over time, as Scarf grows, we’ll probably use other languages for certain things. I think we’ve hit walls in certain domains of the software. So, the analytics pipeline of Scarf has to work with a lot of data, and we wanted to use Parquet, but there weren’t great Parquet bindings for Haskell. And we might want to use this other database, but not great Haskell bindings. And you want to do TensorFlow. And there’s all these certain things that you might want to do in the data space, and it’s just like, just Python might be a better tool, or Scala might be just a better thing for that because of the library ecosystem. And I don’t think there’s any reason that Haskell can’t do all those things just as well, but it doesn’t right now.

*AL (0:46:38)*: Yeah. And obviously, you cannot do everything yourself as a startup.

*AP (0:46:43)*: Right, exactly. So, yeah, ultimately, I am definitely happy with the choice and I think that Haskell can have a very bright future with startups and with companies that are growing and have the freedom to choose tools like that. But that was a decision that we made and worked through because we’d love it. If I didn’t love Haskell, I would’ve dropped it really quickly, I think. The love for the language is what kept us pushing through it, not all of these benefits, quite frankly. The benefits are good, but the drawbacks are also significant. 

*AL (0:47:18)*: But the love is also there for a reason, right?

*AP (0:47:20)*: A hundred percent. Yeah. But it keeps the workday very fun if you’re coding at Scarf. And that means something. That’s not nothing, right? If something keeps your day-to-day development more enjoyable, it’s hard to put a price tag on that.

*AL (0:47:38)*: And I mean, in a way, like this case that you made about the speed of adaptation, that in particular for startups is an excellent argument in a way because many of the pain points you’re describing are actually growth problems, like if you want to scale, if you want to expand here, if you want to expand there. But initially, if you want to come up with something quickly and you don’t know the design or you’re just evolving the design, it seems like the advantages are more clearly playing to the strengths of Haskell in a way. So, I guess probably the lesson is, actually, more startups should be using Haskell.

*AP (0:48:18)*: Well, here’s a corollary to that, is that if you’re switching what you’re doing a lot, correctness doesn’t matter quite as much because your spec is probably wrong. You’re moving things really quickly, and there’s less of a reason to think through all the corner cases in the way that Haskell forces you to think through all those things upfront. Sometimes it doesn’t actually matter all that much. And you’d be better off shipping something a little bit buggier and learning all the reasons why the design wasn’t quite right and iterating, then make this design perfectly correct as described and then shifting. And so, it’s like, yeah, in some ways, it’s a huge benefit. And in other ways, it lessens a different advantage that Haskell offers or makes it just less appropriate for the domain in this particular case of like fast-moving business.

*AL (0:49:13)*: I guess it depends a bit on the domain you’re in. I mean, I’m a bit surprised to hear you saying this. My impression is always that too often I’ve seen projects that fail because they get to 80 or 90% with a flawed fundamental design because they have not been forced to think about the corner cases. 

*AP (0:49:35)*: Yeah. And that’s totally true, too. Totally.

*AL (0:49:37)*: And at some point, they notice that, oh yeah, it’s really almost there, but it doesn’t work because it’s simply there is a mistake. And this mistake is essentially unfixable. And I could have figured that out much earlier if I had been thinking about this.

*AP (0:49:51)*: I think it was forcing you to think through the domain versus forcing you to think through really technical details that are not really relevant to your – like the accidental complexity. Like yeah, I want to think through my domain to completion and be formally sure that my domain is what I think it is and I’ve thought through all the things. But I don’t want to have to think about like, “Oh, how do I juggle these two monads from these two different libraries?” And like, “Oh, they don’t stack in the right way.” That’s complexity that is actively getting in the way.

*AL (0:50:25)*: Yeah. Okay. No, no, I think that is a very good point because I think in many places really also actually the Haskell approach helps you with even being imprecise in select areas. 

*AP (0:50:34)*: Totally.

*AL (0:50:35)*: You can define your types in such a way that you just randomly weaken things in certain areas and say, “I have a catchall case,” or “I will fill in this later.” But what you are describing is an artifact of this. Everybody has a slightly different approach to everything. And then you have this one library that you would want to use, but it is not using MTL, but some effects library, and you have to figure out how to make that work with the rest of the ecosystem that you’re currently using.

*AP (0:51:07)*: Yeah. It’s just a lot of accidental complexity. And I think that good tools do as much as they can to eliminate that kind of complexity. There’s like the kind that you need to grapple with and the kind that you shouldn’t have to. An initiative that I would love to kick off in the Haskell world would be like a developer experience paper cuts, like group to just find these small annoyances that we run into. There’s a lot of low-hanging fruit for that. I think of small fixes that would make a big deal to the developer experience that are probably not very technically challenging. We just don’t do it. We actually did this at Scarf pretty recently for our UI of like UI paper cuts and how can we small tweaks that make the UI much better. And I think I would love to have a similar kind of initiative in the Haskell world. Maybe I’ll start one.

*AL (0:51:52)*: I think it’s a very good idea. I would like that as well. I think that, yeah, you’re right. There are many small improvements possible that are often going unchanged or perhaps even unnoticed because, as we discussed earlier, sometimes there is this, you’re subconsciously suffering, but you’re not even really understanding that you’re suffering until you see something better.

*AP (0:52:17)*: Yeah. We’ve got the Stockholm syndrome, very statically typed Stockholm syndrome.

*AL (0:52:22)*: Okay. So, perhaps to end on a positive note, and this was already perhaps a start, what kind of positive, ideally positive changes or improvements would you like to see in the Haskell world in, say, the next five years?

*AP (0:52:42)*: Yeah. I would definitely like to see the on-ramp for beginners to continue to become more and more polished, and something where I hear less people that tried to learn Haskell that weren’t successful at it for one reason or another. Yeah, I’d like to see just a bigger Haskell community. I’d like to see an ecosystem and compiler that was breaking or breaking things less often. I mean, those are the general things. Yeah.

*AL (0:53:11)*: I mean, these are all at least things where I would say these efforts have been identified and started. They’re difficult problems to really solve completely and quickly, but I think it’s at least moving into the right direction at the moment. 

*AP (0:53:25)*: Yeah. And I think just having more of an industry presence in the Haskell world as well, I think, can also go a really long way because, from funding to the HF to more libraries getting contributed, I just want to see more companies writing or a lot of companies do use Haskell. They’re just not really participating all that much sometimes. But the more that we have, the more of it we’ll see. I want to see that flywheel going of bringing in more Haskell users.

*JB (0:53:54)*: Thanks. I think that’s a great wish for the future. Thanks for being on the show.

*AP (0:54:01)*: Yeah, thanks for having me.

*Narrator (0:54:03)*: The Haskell Interlude Podcast is a project of the Haskell Foundation, and it is made possible by the generous support of our sponsors, especially the Monad-level sponsors: GitHub, Input Output, Juspay, and Meta.
