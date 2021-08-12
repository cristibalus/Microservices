## Are microservices needed for the business' goals?

....

## Can we successfully implement and maintain a microservices architecture?

Upon considering microservices architecture for a project, one needs to assert that the endeavor of implementing such an architecture is not set for failure before it even gets started. At NetRom, we are working in a specific environment which, most of the times, implies very different scenarios from regular companies which are using microservices. One difference would be that, apart from our internal apps, we don't have our own products, we aren't the only ones responsible for the teams formation and we probably can't just enforce futuristic processes and architectures to our clients.

This section analyses such peculiarities of NetRom's activity in relationship with disadvantages of microservices to form some kind of a "checklist", to help with deciding whether it's possible to implement microservices in a specific situation. This way one can maybe rule out projects which just aren't appropriate for microservices or at least identify critical attention points. Here is a proposal of what a non-exhaustive microservices checklist might look like:

### 1. Does our team structure allow microservices development?

When implementing microservices, two of the most important matters are **low coupling** and **high cohesion**. This might sound simple. We just have to be careful to have functionalities implemented into separate services, define domains, avoid chatty communication and we'll be fine. Well, it doesn't really sound simple per se, but at least it's doable, right?. In NetRom's case however, things are a little bit more complicated. Achieving low coupling and high cohesion between components takes more than just technical solutions and, unfortunately, there is a law which may stand between NetRom and those two, indispensable targets. That's just because of how we're working with our clients:

> Any organization that designs a system will inevitably produce a design whose structure is a copy of the organization's communication structure.
>
> ​                                                              																		**Conway's Law**

Conway's law basically says that if your company structure relies on highly coupled communication between different departments, your actual system's components, which are implementing those department's processes, will be coupled together. As stated before, we don't have ownership over team formation. Sometimes we just have only one team taking care of the whole project. That team usually communicates with a product owner and maybe another dev team from our clients. In case of microservices, one can say from the beginning that this scenario is set for failure. You just can't have 10, maybe 20 microservices implemented by a single team. They will end up performing chatty communication and just being tightly coupled overall. Of course, microservices architectures worked just fine for companies like Netflix, Amazon and so on, but even those giants had to make important structural changes in their organization in order to accommodate microservices.

So, this means that we can't implement microservices at NetRom, right? I wouldn't go that far to say that this statement is 100% true in all scenarios. 

Firstly, we do have a few projects which consist of multiple (3, 4 or even 5), self sufficient teams on our side. Add one or two teams on the client's side and there you have it! With a little bit of effort, achieving low coupling won't be impossible anymore. You still have to pay attention to other matters, but it's not impossible. 

Secondly, the decision might not be a straight "NO" for smaller setups. Maybe the MVP for the client's needs is not that complex and can be handled by only one or two teams initially. Once the project becomes bigger, more teams can join in to implement other needed components, which is perfectly fine. Moreover, there is one variation of Conway's law, which probably maps better to our situation. It states that:

> A system will perform better if it closely matches its organization's communication structure.

In this variation, "better" not only refers to the efficiency of the system itself, but also to a better productivity of all the people involved in implementing the services. This would mean that if you had a way to keep the productivity up and pay closer attention to microservices pitfalls, you could actually make it work. It wouldn't be as efficient as doing it "by the book", but in case microservices are **really** needed, you have an acceptable way to do it. One other way to accommodate microservices would be to start with a monolith and transition to microservices later on, when the need for them rises. This is actually the preferred approach at the moment, but this topic will be addressed in the next chapter.

### 2.  Do we have the needed knowledge?

We've seen which are the team's requirements at a macro level. Now, what micro? Let's say we're in the easiest scenario, having 4 or 5 teams working on separate services. As we said, the teams should be self sufficient i.e. every team should be competent enough to achieve their goals without help from anyone outside the team. This will improve high cohesion and low coupling eventually. However, there are quite a few aspects related to microservices that team members must be proficient in. This means it's going to be very hard to find the right people in order to fulfill the needs of a self-sufficient team. Those are some of the aspects you need to pay attention to when assessing team's capabilities:

- **Are our teams capable of identifying domains and splitting responsibilities**? Your organization should be able to decide which functionality goes in which service. When poorly designed, this can easily mess up the entire system, so you need a lot of knowledge and context in order to be able to do it correctly. Maybe there is one person on our team, or the client's team who has a broad overview of all the functional scenarios - an architect, for example. They would be a good candidate for helping with solving this issue. However, this is a double-edged sword, as this person might become a big dependency between all the teams. It's important to learn, how to make use of that person's knowledge while gradually lowering team's dependency on them.
- **Do we have the knowledge to work with containers?** While other matters may be 1% questionable, there is no space for questions about this one. For microservices you **HAVE TO** work with containers. Skipping this would defeat the whole purpose of having self-deployable, scalable services. You have to assess whether there are people in the team who are familiar with containers, Docker, Kubernetes etc. and accommodate trainings where needed. 
- **Messaging.** Services usually also expose REST APIs, but it is preferred - because of reasons which will not be addressed in the scope of this article - that the communication is performed through some message bus. This means all your developers must at least have some basic knowledge about what queues are or about sending messages.
- **Testing.**  One microservice should handle the failure of other services, so that they can work and be deployed independently. This behavior can be tested by breaking the circuit between some of the services. Have any people in my team ever done that? Have they ever written unit tests (you'd be surprised how often those are just skipped)? Does anybody have any knowledge about end to end automated tests?
- **Continuous integration**. Ok, now we have tests, but are we capable to run them continuously? Are our teams capable of creating pipelines? Does everybody understand the need of a pull request and is everyone keen on using pipelines for integrating and deploying the work?

Every developer should have a somewhat basic knowledge in every matter described above. However, it's always good to have a couple of people which are experts in every area. Ideally, you would have one expert in each area in every team, so that every team is self-sufficient, but this is not always possible. It's not necessarily the end of  the world if you have no knowledge about one of those areas, but the expertise should at least be spread across different units, so that even if they aren't 100% efficient, you are less prone to having chatty communication, therefore high coupling between teams.

### 3. Infrastructure

Let's say you figured out the first two points. You have capable enough teams to be self-sufficient, you are able to implement tests, messaging, to have pipelines up and running but... where are they going to run? While microservices offer good scalability and high availability, this comes with the cost of complexity across the board. Your infrastructure has to facilitate that scalability. 

Even though this topic might also go down to continuous integration/ops/containers points, at NetRom it has to be a separate discussion. This is because we're not alone, we're working with clients. Of course, there is the classic question: do we have the knowledge to implement this? But there comes an additional question: Who is responsible for creating and maintaining the infrastructure? Most of the times, this is handled by our clients, since they all probably want to be able to continue their activity in the unfortunate event of our collaboration ending. Nevertheless, this comes with an overhead, as our teams might become dependent on the client's availability, in case they want to perform some actions they are not authorized to. This should be discussed both internally and with the clients before starting implementing microservices.

## Starting with microservices vs. monolith-first approach

So, following the previous chapters you may have decided that a.: dealing with microservices overhead is worth, because the advantages they bring are suited for your project and b: you may actually be capable of implementing this kind of architecture. Now it's time to decide how to go about it. One may ask: what's there to decide? Why are we still talking about monoliths if the decision was to go with microservices? This question can be answered by looking at stories of other companies. In fact, many of those who successfully implemented microservices started with a monolith that got too big and had to be broken up. At the same time, the rate of success for the companies which started implementing microservices from scratch is very low, but we don't hear about them because... well... they were unsuccessful. 

However, microservices architecture is still young and it would be reckless to take decisions only based on the very few experiences some guys wrote about in their articles. If you go with microservices, you're going to have to think for yourself, as there is no definite rule of thumb for anything and community support is very low or questionable. BUT, we already have some considerations about starting straight with microservices vs. monolith-first approach though. Based on those past experiences we can decide for ourselves.

### The monolith-first approach 

The general consensus in the community is that, even if a company knows its project is going to get big enough to require microservices at some point, they should start with a monolith until they are really needed. This makes perfect sense, it's basic YAGNI principle. You don't build separate pipelines, processes, codebases and so on for multiple services until the complexity of the system actually requires it.

We also discussed in previous chapters about how domain definition is one of the toughest challenges while implementing microservices. This is one of the most painful issues the monolith-first approach is trying to solve. Maybe you don't always have overview of all system's domains in the beginning, or the team doesn't have enough experience to identify them. Then, a monolith-first approach would give you time to learn about the system and prepare for microservices and identifying domains. This would also be a good way to start if you have a very small team, since it would be a pain to maintain separate deployment for multiple services with only 3 or 4 people.

Please bear in mind that, with this approach, the end goal is still implementing microservices, so you're going to have to make all the implementations accordingly. This is one of the downsides of implementing monolith-first, because it forces you to spend extra time to build a monolith which you should make sure is "microservice-able" at any point, or take the risk of building it faster and not being able to break it into microservices when you need to, because it's too tightly coupled.

### Straight microservices

Implementing microservices from the beginning is essentially the opposite as far as advantages and disadvantages go. In the beginning, you'll add a lot of overhead, as you will have to add complexity in infrastructure, team organization, processes etc. However, you will be spared from living with a constant spike in your back, knowing that you don't have to make a transition from monolith to microservices at anytime. You can't know and can't communicate to your client what such a transition would mean and when it's going to be done, because this is going to be figured out along the way in a monolith-first approach. So skipping the monolith part would make things easier with our clients.

Therefore, in a way, even if it seems this approach comes with an overhead, this overhead is most of the times only related to technical aspects, which can easily be overcome. Implementing microservices from the beginning would be preferable in a scenario where the client desires to start with multiple units immediately. Another way in which this would work would be to start implementing just a few services with a small team and, as the project grows, the team should expand as well. But this would also require a promise from the client's side to expand the team, as extending the project with more microservices and a small team would turn into a disaster.

### Breaking up the monolith

Even if you started with microservices in mind from the beginning or just inherited a big codebase, you might find yourself in the position of having to split up a monolith into separate services. The way to go in this case is to identify small seams which can be broken apart from the monolith with the least possible effort, and deploy them as microservices one by one.

When considering moving from a monolithic architecture to microservices one must first find answers [together with the client] for a few questions:

1. Do our current project timelines allow it?
2. Is our current team able to handle the workload?
3. Does our current monolith suffer from overly-tight coupling?
4. Has our monolith become harder to understand and maintain?
5. Have we arrived in a point in which we have too many people working on a single, monolithic application?

If the answer to all of the above questions is **YES** the time has come to consider the transition. However, especially if the monolith wasn't developed with microservices in mind, while answering those questions you might find out that your system is so tightly coupled, that you can't even break it into small services. This is why it's important to answer those questions in the first place. There's no harm done. If you and the client still really want to go for microservices, you can even wipe the entire solution and start from scratch, but there are few stories about people who did that successfully and probably even fewer clients which would agree to this approach.

In order to be successful with any approach, don't promise too much all at once and be careful that your client understands every step of the process. Microservices architecture is still new and some clients might just want to join the hype, without realizing what it implies. You might manage to convince them to go with it without them understanding what microservices mean. This is bad, because when every architecture-specific downside happens (and they're going to happen), you'll be to blame and they will start seeing microservices as some kind of a technical caprice.

Therefore, at any point you are in your endeavor to implement microservices, try to engage with business stakeholders to make them understand some concepts - at a high level at least - and participate in taking decisions about:

- Hardware, software, trainings, IT tools and people requirements for microservices
- The need for cross-functional teams that collaborate freely
- A microservices ecosystem is a platform of services each encapsulating a business capability. 
- A business capability represents what a business does in a particular domain to fulfill its objectives and responsibilities. 
- Microservices have independent lifecycles. 
- Developers must be able to build, test and release each microservice independently. 
- The microservices ecosystem enforces an organizational structure of autonomous long standing teams, each responsible for one or multiple services. 
- Contrary to general perception and ‘micro’ in microservices, the size of each service matters least and may vary depending on the operational maturity of the organization.

## Microservices alternatives

A lot of times, microservices are seen as a very extreme architectural approach. That's probably because the people who implement such architectures tend to be fierce fans of microservices and sometimes they might take this to the extreme. However, it doesn't have to be this way. If you don't think microservices are the way to go, but still you want some kind of a modular architecture, those might be good alternatives:

### Plugin Architecture

Plug in architectures work exactly as you would imagine. It's similar to browsers, which have some core functionalities and can be extended with specific plugins. The goal with this kind of implementation is to be able to plug features into an existing component, without having to modify that component at all. Since there are two components here: the core and a set of plugins, this architecture would be suitable if:

- You have a core implementation which is going to be used in all cases
- You have to extend that implementation, but in a different way for different scenarios or customers

Let's see one example of a project at NetRom, which used to use plugins. It was a self-scanning application for multiple popular supermarkets. As you would imagine, all of them wanted their app to be able to scan items, remove items form the basket and other basic operations. However, there were some specific functionalities, related to how supermarkets operate their promotions or advertisement for example. Some of them wanted those functionalities, some didn't. With a plugin architecture, you can deliver the core functionality to all supermarkets, and let them configure what kind of plugins they want to support in their app. 

Also, it would be nuts to have a completely separate app for every customer. Every time a change in common functionality would be needed, you would have to "copy-paste" it to all other apps. With a plugin architecture, you would just have to redeploy the core functionality containing new features or fixes for the clients which need it. However, you have to keep a very stable contract between core functionality and extensions, as breaking that contract very often would require lots of changes, which is one of the drawbacks of this architecture.

### Service Oriented Architecture (SOA)

SOA is an approach which became popular in the late 90's. It is also based on separately-maintained and deployed software components. Basically, microservices can be seen as a subset of SOA, a new iteration of the same concept, with some differences:

- Services in SOA might share a data source, microservices always have their own data source
- SOA is usually focused on functionality reuse, while microservices are all about defining cohesive domains
- Microservices were born in the cloud era, so usage of containers is a lot more popular

The two approaches are similar in some ways, but they differ in details and practices. This is good, because you have the perfect alternative to microservices. However, there is an aspect which can clearly draw the line between microservices and SOA, from a conceptual point of view: microservices implementations will always be in favor of sharing as little as possible, while SOA is exactly the opposite, where the emphasis is on reusing business functionality. This differentiating factor might be key in your decision between SOA and microservices.

## Architecture examples

...
