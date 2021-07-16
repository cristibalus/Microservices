## Are microservices needed for the business' goals?

....

## Can we successfully implement and maintain a microservices architecture?

After deciding that microservices might be a viable solution for a project, one needs to assert that the endeavor is not set for failure before it even gets started. At NetRom, we are working in a specific environment which, most of the times, implies very different scenarios from regular companies which are using microservices. For example, one difference would be that, apart from our internal apps, we don't have our own products, we aren't the only ones responsible for the teams formation and we probably can't just enforce specific processes to our clients. Fortunately, such peculiarities of NetRom's activity and processes can be used in relationship with disadvantages of microservices to form some kind of a "checklist", in order to take informed decisions, which are specific to our company. This way you can maybe rule out projects which just aren't appropriate for microservices or at least identify critical attention points, so that you know what you are going to deal with beforehand. Here is a proposal of what a non-exhaustive microservices checklist might look like:

### 1. Does our team structure allow microservices development?

When implementing microservices, two of the most important matters are **low coupling** and **high cohesion**. This might sound simple. We just have to be careful to have functionalities implemented into separate services, define domains, avoid chatty communication and we'll be fine. Well, it doesn't really sound simple per se, but at least it's doable, right?. In NetRom's case however, it is more complicated than this. Reaching low coupling and high cohesion between components takes more than just technical solutions and, unfortunately, there is a law which may stand tall between NetRom and those two, indispensable targets. That's just because of how we're working with our clients:

> Any organization that designs a system will inevitably produce a design whose structure is a copy of the organization's communication structure.
>
> ​																																			**Conway's law**

Conway's law basically says that if your company structure relies on highly coupled communication between different departments, your actual system's components, which are implementing those department's processes, will be coupled together. As stated before, we don't have ownership over team formation. Sometimes we just have only one team taking care of the whole project, which tightly communicates with a product owner and maybe another dev team from our clients. In case of microservices, one can say from the beginning that this scenario is set for failure. You just can't have 10, maybe 20 microservices implemented by the same two teams. They will end up performing chatty communication and just being tightly coupled together overall. Of course, microservices architectures worked just fine for companies like Netflix, Amazon and so on, but even those giants had to make important structural changes in their organization in order to accommodate microservices.

So, this means that we can't implement microservices at NetRom, right? I wouldn't go that far to say that this statement is 100% true in all scenarios. 

Firstly, we do have a few projects which consist of multiple (3, 4 or even 5), self sufficient teams on our side. Add one or two teams on the client's side and there you have it! With a little bit of effort, achieving low coupling won't be impossible anymore. You still have to pay attention to other matters, but it's not impossible. 

Secondly, the decision might not be a straight "NO" for smaller setups. Maybe the MVP for the client's needs is not that complex and can be handled by only one or two teams initially. Once the project becomes bigger, more teams can join in to implement other needed components, which is perfectly fine. Moreover, there is one variation of Conway's law, which probably maps better to our situation. It states that:

>  A system will perform better if it closely matches its organization's communication structure.

In this variation, "better" not only refers to the efficiency of the system itself, but also to a better productivity of all the people involved in implementing the services. This would mean that if you had a way to keep the productivity up and pay closer attention to microservices pitfalls, you could actually make it work. It wouldn't be as efficient as doing it "by the book", but in case microservices are **really** needed, you have an acceptable way to do it.

This variation also goes the other way around and was actually the case at one of our projects. Even at NetRom we used to have a project with 4 teams on our side which were working on a big, monolithic solution. This was leading to a loss of efficiency in communication and lots of frustrations because lots of teams were working on the same, huge codebase. The decision in the end was the adoption of a microservices architecture, by splitting up the monolith into microservices step by step.

### 2.  Do we have the needed knowledge?

We've seen which are the team's requirements at a macro level. Now, what about micro level? Let's say we're in the easiest scenario, having 4 or 5 teams working on separate services. As we said, the teams should be self sufficient i.e. every team should be competent enough to achieve their goals without help from anyone outside the team. This will improve high cohesion and low coupling eventually. However, there are quite a few aspects related to microservices that team members must be proficient in. This translates in it being very hard to find the right people in order to fulfill the needs of a self-sufficient team. Those are some of the aspects you need to pay attention to when assessing team's capabilities:

- **Are our teams capable of identifying domains and splitting responsibilities between them**? Your organization should be able to separate functionalities in services and decied which functionality goes in which service. When poorly designed, this can easily mess up the entire system, so you need a lot of knowledge and context in order to be able to do it correctly. Maybe there is one person on our team, or our client's team who has a broad overview of all the functional scenarios - an architect, for example. They would be a good candidate for solving this issue, as they can understand and take decisions about which functionalities go into which service. However, this is a double-edged sword, as this person might become a big dependency between all the teams. It's important to learn, in time, how to make use of that person's knowledge without being dependent on them.
- **Do we have the knowledge to work with containers?** While other matters may be 1% questionable, there is no space for questions about this one. For microservices you HAVE TO work with containers. Skipping this would defeat the whole purpose of having self-deployable, scalable services. You have to assess whether there are people in the team who are familiar with containers, Docker, Kubernetes etc. and accommodate trainings where needed. 
- **Messaging.** Services usually also expose REST APIs, but it is preferred - because of reasons which will not be addressed in the scope of this article - that the communication is performed through some message bus. This means all your developers must at least have some basic knowledge about what queues are or about sending messages.
- **Testing.**  One microservice should handle the failure of other services, so that they can work and be deployed independently. This behavior can be tested by breaking the circuit between some of the services. Have any people in my team ever done that? Have they ever written unit tests (you'd be surprised how often those are just skipped)? Does anybody have any knowledge about end to end automated tests?
- **Continuous integration**. Ok, now we have tests, but are we capable to run them continuously? Are our teams capable of creating pipelines? Does everybody understand the need of a pull request and is everyone keen on using pipelines for integrating and deploying the work?

Every developer should have a somewhat basic knowledge in every matter described above. However, it's always good to have a couple of people which are experts in every area. Ideally, you would have one expert in each area in every team, so that every team is self-sufficient, but this is not always possible. It's not necessarily the end of  the world if you have no knowledge about one of those areas, but the expertise should at least be spread across different units, so that even if they aren't 100% efficient, you are less prone to having chatty communication, therefore high coupling between teams.

### 3. Infrastructure

Let's say you figured out the first two points. You have capable enough teams to be self-sufficient, you are able to implement tests, messaging, to have pipelines up and running but... where are they going to run? While microservices offer good scalability and high availability, this comes with the cost of complexity across the board. Your infrastructure has to facilitate that scalability. 

Even though this topic might also go down to continuous integration/ops/containers points, at NetRom it has to be a separate discussion. This is because we're not alone, we're working with clients. Of course, there is the classic question: do we have the knowledge to implement this? But there comes an additional question: Who is responsible for creating and maintaining the infrastructure? Most of the times, this is handled by our clients, since they all probably want to be able to continue their activity in the unfortunate event of our collaboration ending. Nevertheless, this comes with an overhead, as our teams might become dependent on the client's availability, in case they want to perform some actions they are not authorized to.

## Considerations for starting fresh

When considering moving from monolithic architecture to microservices architecture we must first ask ourselves a few questions:

1. Do our current project timelines allow it?
2. Is our current team able to handle the workload?
3. Does our current monolith suffer form overly-tight coupling?
4. Has our monolith become harder to understand and maintain?
If the answer to all of the above questions is **YES** then we can certainly consider the transition. 

By switching to microservices we will be able to have better organization due to each microservice having a very specific job which would not affect other components. Furthermore this will also have a decoupling effect on the services by increasing their reusability, allowing partial deployments and thus increasing the overall performance.

Before starting, it is critical that everyone has a common understanding of a microservices ecosystem: 
*	Microservices ecosystem is a platform of services each encapsulating a business capability. 
*	A business capability represents what a business does in a particular domain to fulfill its objectives and responsibilities. 
*	Each microservice exposes an API that developers can discover and use in a self-serve manner. 
*	Microservices have independent lifecycle. 
*	Developers can build, test and release each microservice independently. 
*	The microservices ecosystem enforces an organizational structure of autonomous long standing teams, each responsible for one or multiple services. 
*	Contrary to general perception and ‘micro’ in microservices, the size of each service matters least and may vary depending on the operational maturity of the organization.

In order to be successful with this approach, don't promise too much all at once. Engage with business stakeholders because microservices might require new hardware, software, IT tools and people. Effective microservices development and deployment demand cross-functional teams that collaborate freely and implement a strong CI/CD pipeline.

A CI/CD pipeline is a series of steps that must be performed in order to deliver a new version of software. Continuous integration/continuous delivery (CI/CD) pipelines are a practice focused on improving software delivery using either a DevOps or site reliability engineering (SRE) approach.

A CI/CD pipeline introduces monitoring and automation to improve the process of application development, particularly at the integration and testing phases, as well as during delivery and deployment. Although it is possible to manually execute each of the steps of a CI/CD pipeline, the true value of CI/CD pipelines is realized through automation.

The steps that form a CI/CD pipeline are distinct subsets of tasks grouped into what is known as a pipeline stage. Typical pipeline stages include:

*	**Build** - The stage where the application is compiled.
*	**Test** - The stage where code is tested. Automation here can save both time and effort.
*	**Release** - The stage where the application is delivered to the repository.
*	**Deploy** - In this stage code is deployed to production.
*	**Validation and compliance** - The steps to validate a build are determined by the needs of your organization.


## Breaking up the monolith

...

## Microservices alternatives

...

## Architecture examples

...
