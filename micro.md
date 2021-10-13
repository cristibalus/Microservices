# **Microservices - Part I**

Microservices are one of the newest hype in IT. At a high level, they revolve around the idea of breaking a large system into small, independent services which are built around business capabilities. Their target is to be independently deployable and maintainable. This architectural approach tends to be chosen because it facilitates rapid and customizable scaling and maximizes availability.

Even if microservices are more widely adopted with every year that passes, community's experience is still very low, and most of the documentation you can find on the subject is either questionable or not applicable in NetRom's case. This article is based on NetRom developers' experiences and desires to deliver personalized ideas of how architectural decisions can be argued upon. This should help developers which consider implementing microservices for their project go through the process in a structured way.

This article is NOT a thorough guide, nor a pleading for adopting microservices and it's scope is NOT to encapsulate all the knowledge about microservices in a short summary. Therefore, even if some general concepts are touched, this article is not right for you if you are looking for a thorough description of what microservices are.

# **Do we need microservices?**

Microservices are focused on providing one business capability. They should be very well separated from the others and able to run on their own, handling all faults of other components. You will often find those concepts translated to *high cohesion and low coupling*. To achieve these goals (but at the same time coming as their consequence), systems which implement microservices outline common characteristics, every one of them coming with pros and cons.  

In order to decide if microservices are what you need for your project, you should carefully weigh those pros and cons, eventually ending up with having to answer the same question for every microservice "characteristic": **Do we need ...?**

### **Customized Scalability**

When it comes to scaling an application, there are two options: scaling out (horizontally), or scaling up (vertically). Scaling a system up means increasing performance of your single server (more RAM, faster CPU etc.). It's like extending a bus with an additional floor when a route gets very busy.

![scalingup](C:\Users\c.s.balu\Documents\scalingup.png)

Horizontal scalability, on the other hand, is performed by adding more instances of the existing machines. In terms of bus routes, it's like sending multiple buses on the busy route instead of replacing buses with bigger ones.

![scalingout](C:\Users\c.s.balu\Documents\scalingout.png)

**Pros:** Since microservices are individually deployable, you can have granular control over the performance of the application, meaning you can measure and keep track of every component independently and only scale parts which are overloaded. This allows for an easier implementation of horizontal scalability. In a monolith's case, your only option is to scale the whole application up if one of the components performs poorly.

Continuing the bus routes analogy, vertical scalability would translate into having to pay for a double-decker (more power) even when you don't actually need it. If traffic on the route frequently varies (i.e. traffic in a software system), you wouldn't be able to just chop off the top floor of your bus and then stick it back the next day. Instead, it would be a lot easier to buy more buses and send just as many as you need on the route, sparing lots of resources and reducing costs.

**Cons:** As managing a bigger fleet of buses would add process complexity, so does maintaining multiple servers and the load between them. You will need to have software which is solely responsible for scaling and testing, adding complexity. Also, even though horizontal scalability is more cost-effective in the long run, scaling out (buying more buses) might initially be more expensive than scaling up.

### **High resilience and availability**

Failures cannot be avoided. Even Amazon offers "only" around 99% uptime for their computing services. At scale, that 1% is very significant. Knowing when and why that 1% happens can mean a lot in reducing our developers' frustration while looking for a bug and, at the same time, make the communication with our clients more transparent.

**Pros:** By design, a modular architecture will always be more resilient than a monolith. This is because when something fails, it's easier to identify where it happened and handle the failure. This way, problems can be isolated and the rest of the system keeps working. Within a monolith, even if a failure doesn't break the entire system (although this is more likely), it will be very hard to know what failed and where and how you need to handle the fault.

**Cons:** As every other aspect related to microservices, resilience comes at the cost of complexity. You have to know how to handle failures. However, this cost is significantly lower than having to look into a totally unknown issue while your system is down or not behaving as expected.

### **Small, granular codebases**

All codebases get bigger as new features are introduced. With this, the chances of code being spread or duplicated towards being used by similar consumers increase exponentially. Either this, or it is thrown in a "Core" project, which often becomes so large that it is very hard to tell where a change needs to be made in order to fix a bug or implement a simple functionality. This can be avoided by implementing only code which has to be changed for the same reason in the same place.

**Pros:** Even though this principle might be available in a monolithic architecture as well, with microservices you always focus on developing cohesive services. By doing this, it is ususally obvious where each piece of code should go and you are less likely to grow one component into an unmanageable giant.

**Cons:** The question with microservices becomes *how small is small enough*? The catch is that, as a service is smaller, all microservices advantages and disadvantages will have bigger impact. Yes, the code will be more manageable, but at the same time dependencies between components and teams which develop them will grow. Since at NetRom we tend to have independent teams, finding out how small is small enough can be reduced to answering the following, more manageable question: "Does the service feel too big?". If the answer is no, then that service is probably small enough.

### **Technology agnosticism**

One of the ways high cohesion and low coupling is achieved through microservices is their interaction, which is performed over an inter-process communication protocol such as HTTP or AMQP. This allows every service to be implemented in whichever language is the most suited for its needs.

**Pros:** Even though at NetRom we focus on a few technologies, such as .NET, Java or PHP, some of the tasks our clients need us to perform might be impossible or cumbersome to be implemented in those technologies. Microservices allows us to pick a certain technology only for specific implementations. For example, if you need to use Python for a component which uses ML, you wouldn't have to write all your application in Python. This is important, because you wouldn't have to organize Python trainings for all developers, while that specific task can be carried on with only one or two resources.

**Cons:** There is really no downside to being able to implement services in different languages. However, you should be careful when doing so and limit the number of technologies. It can become hard to keep an overview if a project has 5 services implemented in 5 different languages. At NetRom, we should probably implement a service in a different language only if this would bring us an obvious benefit, or if it's not possible to do it otherwise.

### **Replaceability**

As microservices are cohesive and individially deployable, it's very easy to replace one of them if a better alternative shows up. You would only need to make changes towards that specific component you want to replace, since the other ones already handle the interaction with it.

**Pros:** Take the example of a monolithic .NET application with lots of functionalities which do work properly. Let's imagine that in about 10 years .NET becomes obsolete: .NET developers will be hard to find, NetRom won't have a dedicated department anymore. How do you keep maintaining your application? What if there is a need to change only a small part of the system to use a third party library instead of the current implementation? This is a little bit to SF, you might say. This is what Fortran developers thought 30 years ago, though. If constant change is what you need to accommodate, then implementing microservices could be a good approach.

**Cons:** Again, there are really no cons about being able to easily replace a component. Nevertheless, this should count towards your "Do I need it?" checklist. If you don't need most of the pros microservices offer, then the crushing complexity they automatically come with is just not worth it.

# **Three ways to fail at microservices**
At NetRom, we are working in a specific environment which, most of the times, implies very different scenarios from regular companies which are using microservices. One difference would be that, apart from our internal apps, we don't have our own products, we aren't the only ones responsible for the teams formation and we probably can't just enforce futuristic processes and architectures to our clients. Messing up microservices is easy enough for regular companies, but at NetRom we have to take it even a step further.

## **1. Starting with an inappropriate organizational structure**

When implementing microservices, two of the most important matters are *low coupling* and *high cohesion*. This might sound simple. We just have to be careful to have functionalities implemented into separate services, define domains, avoid chatty communication and we'll be fine. In NetRom's case however, things are a little bit more complicated. Achieving low coupling and high cohesion between components takes more than just technical solutions and, unfortunately, there is a law which may stand between NetRom and those two, indispensable targets. 

>
> *Any organization that designs a system will inevitably produce a design whose structure is a copy of the organization's communication structure.*
> 
> *Conway's Law*

Conway's law basically says that in order to achieve low coupling and high cohesion between software components, we have to do it organizationally as well. As we don't usually have ownership over team formation, in some cases it's really hard to have our teams specialized in only one component. At the same time, having lots of microservices implemented by a single team will create a mess. They will end up performing chatty communication and just being tightly coupled overall.

This doesn't mean that we cannot implement microservices at NetRom. There are a few approaches we can take, such as targeting only a few functionalities in the beginning or starting with a monolith which can be broken down later on, when the need arises. Implications of those approaches will be discussed more thoroughly in the second part of this article. However, it's critical that this aspect is taken into account before you even write the first line of code so that architectural disasters can be avoided.


## **2. Not assessing and continuously improving team's capabilities**

Every team should be competent enough to achieve their goals without help from anyone outside the team. This will improve high cohesion and low coupling eventually. However, it is very hard to find the right people in order to fulfill the needs of a self-sufficient team and there are some aspects which you need to pay attention to when assessing team's capabilities.

**Are our teams capable of identifying domains and splitting responsibilities?**

Your organization should be able to decide which functionality goes in which service. When poorly designed, this can easily mess up the entire system, so you need knowledge and context to be able to do it correctly. Maybe there is one person in the client's team who has a broad overview of all the functional scenarios - an architect, for example. They would be a good candidate for helping with solving this issue. However, this is a double-edged sword, as this person might become a big dependency between all the teams. It's important to try, in time, to make use of that person's knowledge while lowering team's dependency on them.

**Do we have the knowledge to work with containers?**

While other matters may be 1% questionable, there is no space for questions about this one. For microservices you **HAVE TO** work with containers. Skipping this would defeat the whole purpose of having self-deployable, scalable services. You have to assess whether there are people in the team who are familiar with containers, Docker, Kubernetes etc. and accommodate trainings where needed. 

**Messaging**

Services usually also expose REST APIs, but it is preferred - because of reasons which will not be addressed in the scope of this article - that the communication is performed through a message bus. This means all your developers must at least have some basic knowledge about how queues work.

**Testing**

One microservice should handle the failure of other services, so that they can work and be deployed independently. This behavior can be tested by breaking the circuit between some of the services. Have any people in my team ever done that? Have they even ever written unit tests (you'd be surprised how often those are just skipped)? Does anybody have any knowledge about end to end automated tests?

**Continuous integration**

Ok, now we have tests, but are we capable to run them continuously? Are our teams capable of creating pipelines? Does everybody understand the need of a pull request and is everyone keen on using pipelines for integrating and deploying the work?

Every developer should have a basic knowledge in every matter described above. However, it's always good to have a couple of people which are experts in every area. Ideally, you would have one expert in each area in every team, so that every team is self-sufficient, but this is not always possible. It's not necessarily the end of  the world if you have no knowledge about one of those areas, but the expertise should at least be spread across different units, so that even if they aren't 100% efficient, you are less prone to having chatty communication, therefore high coupling between teams.

## **3. Poor delivery processes and infrastructure**

Let's say you dodged the first two points. You have capable enough teams to be self-sufficient, you are able to implement tests, messaging, to have pipelines up and running but... where are they going to run? While microservices offer good scalability and high availability, this comes with the cost of complexity across the board. For example, with a microservices architecture, developers shouldn't even have to leave their IDE in order to deploy a new version of a service. Doing this would result in a huge bottleneck when you have to deliver large numbers of microservices. Your infrastructure needs to support integrating and delivering distributed components rapidly.

Because at NetRom we're working with clients, there comes an additional question: Who is responsible for defining and facilitating processes and infrastructure? Most of the times, this is handled by our clients, since they all probably want to be able to continue their activity in the unfortunate event of our collaboration ending. Nevertheless, this approach is an overhead, as our teams will depend on the client's availability to carry on some actions. This should be discussed both internally and with the clients before starting implementing microservices to make sure processes for those kind of actions can be acommodated.

# **Microservices - Part II**

So, following Part I of this article, you may have decided that:

A: Dealing with microservices overhead is worth the effort, because the advantages they bring are suited for your project and you might be able to actually pull it off.

**OR**

B: Microservices is not an appropriate architecture for your project, but you are still interested in some other distributed architecture alternatives. In this case, you can skip to the last chapter of this article.

In case you find yourself in scenario A, there are some choices you need to make, one of them being: do you start with microservices immediately, or do you implement a monolith first and then break it down when needed. In the first part, we discussed about how Conway's law can keep us from having successfull microservices implementations. We do have our ways to obey that law, though, and those could help us take a decision.

One may ask: what's there to decide? Why are we still talking about monoliths if the decision was to go with microservices? This question can be answered by looking at stories of other companies. In fact, many of those who successfully implemented microservices started with a monolith that got too big and had to be broken up. At the same time, the rate of success for the companies which started implementing microservices from scratch is very low, but we don't hear about them because... well... they were unsuccessful. Let's see what are our options when we decide microservices would be a good architecture for a specific project.

## **Our ways of not breaking the law - Option one: Straight microservices**

Firstly, we have a few projects which consist of multiple (3, 4 or even 5), self sufficient teams on our side. Add one or two teams on the client's side and there you have it! With a little bit of effort, achieving low coupling won't be impossible anymore. You still have to pay attention to other matters, but it's not impossible. This is the easy scenario, in which starting with microservices would make the most sense.

Secondly, the decision might not be a straight "NO" for smaller setups either. Maybe the MVP for the client's needs is not that complex and can be handled by one or two teams initially. Once the project becomes bigger, more teams can join in to implement other needed components. This would actually be an ideal setup for NetRom and our clients, since it would give all parties time to build trust while implementing the first features. 

Nevertheless, by implementing microservices from the beginning, you'll add complexity in infrastructure, team organization, processes etc. Moreover, being able to define application domains (i.e. which functionality goes in which service) becomes critical. If this kind of knowledge is missing, this approach is probably not the best option for your situation.

However, by starting straight with microservices, you will be spared from living with a constant spike in your back, that you have to make a transition from monolith to microservices at anytime (if you know for sure that this will be required sometime). You can't know and can't communicate to your client what such a transition would mean and when it's going to be done, because this will be figured out along the way in a monolith-first approach.

Therefore, skipping the monolith part would increase transparency in communication with our clients. If you can fit your team's structure as described above and also agree this with your client, then it's probably wisest to take this approach. Bear in mind this requires a client which is able to start with multiple units immediately, or be able to promise to expand the team in time. If this doesn't happen and the project complexity grows, you will have to manage lots of microservices with a small, non cohesive team, which we know is going to turn into a disaster.

## **Our ways of not breaking the law - Option two: The monolith-first approach**

The general consensus in the community is that, even if a company knows its project is going to get big enough to require microservices at some point, they should start with a monolith until they needed microservices. You don't build separate pipelines, processes, codebases and so on for multiple services until the complexity of the system actually requires it. This makes perfect sense, it's basic YAGNI principle.

We also discussed in previous chapters about how domain definition is one of the toughest challenges while implementing microservices. This is one of the most painful issues the monolith-first approach is trying to solve. Maybe you don't always have overview of all system's domains in the beginning, or the team doesn't have enough experience to identify them. A monolith-first approach would give you time to learn about the system and prepare for microservices by identifying domains.

Please bear in mind that, with this approach, the end goal is still implementing microservices, so you're going to have to make all the implementations accordingly. This is one of the downsides of implementing monolith-first, because it forces you to spend extra time to build a monolith which you should make sure is "microservice-able" at any point, or take the risk of building it faster and not being able to break it into microservices when you need to, because it's too tightly coupled.

## **Breaking up the monolith**

Even if you started with microservices in mind from the beginning or just inherited a big codebase, you might find yourself in the position of having to split up a monolith into separate services. The way to go in this case is to identify small seams which can be broken apart from the monolith with the least possible effort, and deploy them as microservices one by one.

When considering moving from a monolithic architecture to microservices one must first find answers [together with the client] for a few questions:

1. Do our current project timelines allow it?
2. Is our current team able to handle the workload?
3. Does our current monolith suffer from overly-tight coupling?
4. Has our monolith become harder to understand and maintain?
5. Have we arrived in a point in which we have too many people working on a single, monolithic application?

**Trying to break the unbrakeable?**

If the answer to all of the above questions is **YES**, the time has come to consider the transition. However, especially if the monolith wasn't developed with microservices in mind, these criteria might be misleading. You might find out that your system is so tightly coupled, that you can't even break it into small services. In such situations, implementing microservices won't be a good solution for getting rid of tight coupling and unmaintainable code. You will just end up with separated, but tightly coupled pieces of code, not microservices. This is why it's important to answer those questions in the first place. There's no harm done.

If you and the client still really want to go for microservices, you can even wipe the entire solution and start from scratch. There are few stories about people who did that successfully and probably even fewer clients which would agree to doing this, but the approach has to be agreed by both sides.

**When can you actually break it?**
Breaking a monolith is a matter being able to support the resulting microservices and having the knowledge to identify domains. For the first part, at any point in your endeavor you should engage with business stakeholders to make them understand some concept - at least at a high level - and take decisions about pros, cons, and processes described in the first section.

Having the knowledge is trickyer. It's hard to 

In order to be successful with any approach, don't promise too much all at once and be careful that your client understands every step of the process. Microservices architecture is still new and some clients might just want to join the hype, without realizing what it implies. You might manage to convince them to go with it without them understanding what microservices mean. This is bad, because when every architecture-specific downside happens (and they're going to happen), you'll be to blame and they will start seeing microservices as some kind of a technical caprice.

# **Microservices alternatives**

A lot of times, microservices are seen as a very extreme architectural approach. That's probably because the people who implement such architectures tend to be fierce fans of microservices and sometimes they might take this to the extreme. However, it doesn't have to be this way. If you don't think microservices are the way to go, but still you want some kind of a modular architecture, those might be good alternatives:

## **Plugin Architecture**

Plug in architectures work exactly as you would imagine. It's similar to browsers, which have some core functionalities and can be extended with specific plugins. The goal with this kind of implementation is to be able to plug features into an existing component, without having to modify that component at all. Since there are two components here: the core and a set of plugins, this architecture would be suitable if:

- You have a core implementation which is going to be used in all cases
- You have to extend that implementation, but in a different way for different scenarios or customers

Let's see one example of a project at NetRom, which used to use plugins. It was a self-scanning application for multiple popular supermarkets. As you would imagine, all of them wanted their app to be able to scan items, remove items form the basket and other basic operations. However, there were some specific functionalities, related to how supermarkets operate their promotions or advertisement for example. Some of them wanted those functionalities, some didn't. With a plugin architecture, you can deliver the core functionality to all supermarkets, and let them configure what kind of plugins they want to support in their app. 

Also, it would be nuts to have a completely separate app for every customer. Every time a change in common functionality would be needed, you would have to "copy-paste" it to all other apps. With a plugin architecture, you would just have to redeploy the core functionality containing new features or fixes for the clients which need it. However, you have to keep a very stable contract between core functionality and extensions, as breaking that contract very often would require lots of changes, which is one of the drawbacks of this architecture.

## **Service Oriented Architecture (SOA)**

SOA is an approach which became popular in the late 90's. It is also based on separately-maintained and deployed software components. Basically, microservices can be seen as a subset of SOA, a new iteration of the same concept, with some differences:

- Services in SOA might share a data source, microservices always have their own data source
- SOA is usually focused on functionality reuse, while microservices are all about defining cohesive domains
- Microservices were born in the cloud era, so usage of containers is a lot more popular

The two approaches are similar in some ways, but they differ in details and practices. This is good, because you have the perfect alternative to microservices. However, there is an aspect which can clearly draw the line between microservices and SOA, from a conceptual point of view: microservices implementations will always be in favor of sharing as little as possible, while SOA is exactly the opposite, where the emphasis is on reusing business functionality. This differentiating factor might be key in your decision between SOA and microservices.

# **Architecture examples**

...