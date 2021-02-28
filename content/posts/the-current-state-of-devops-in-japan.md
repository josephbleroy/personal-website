---
author:
  name: "Joseph"
date: 2016-09-15
linktitle: The Current State of DevOps and the Agile Movement in Japan
title: The Current State of DevOps and the Agile Movement in Japan
type:
- post
- posts
weight: 10
series:
- 
aliases:
- 
---

Last Friday I was fortunate enough to attend a seminar at [Creationline, Inc.](http://www.creationline.com/en/) in the Akihabara district of Tokyo. The topic, as you may have already figured out, was on the current state of DevOps and the Agile Movement in Japan. I've been interested in learning more about how software development and projects are managed in Japan. Some of the speakers included [Ippei Suzuki](https://twitter.com/CloudNewsCenter) from Creationline, Inc., [Hiroaki "bucci" Kawabuchi](http://www.ciandt.com/home) from CI&T and [Tsuyoshi Ushio](https://twitter.com/sandayuu) from Microsoft.

One of the several problems preventing Japan from being a global leader in software development and truly embracing startup culture is due to its traditional business attitude toward workplace innovation and management. Many companies in Japan have failed to adapt to modern software development practices, and as a result, they're being technologically defeated. However, there are many companies who've accepted this process and have been able to keep up with society's ever-changing landscape.

> Samuel J. Palmisano, chairperson of IBM, preaches that "only the person who changes the rule of the game wins." However, many Japanese companies have adopted the same rule of the game . . . [a]s result, the Japanese companies have fallen victim to the Galapagos syndrome, which means an isolated state.

[*Japanese Management in Change: The Impact of Globalization and Market Principles*](http://www.springer.com/us/book/9784431550952)

#### What DevOps Is and What It Isn't
DevOps is often mistaken for a product or something that can be purchased and installed. However, that is not the case and some companies are falling victim to this belief. It is, however, a way for companies to automate processes, connect their IT and Software Development teams, unify protocols, and prevent compliance issues. If one system can be set up to meet certain guidelines and security protocols, a thousand can be rapidly deployed and integrated into a company's network, saving time and other valuable resources.

By using configuration automation software, such as Chef or Puppet, a company's infrastructure can be managed by turning it into a single, self-contained configuration file, where it is then deployed across all client systems through a master configuration system through configuration files called "recipes" and "manifests", respectively. There is a steep interdisciplinary learning curve to make DevOps work properly within an organization, as well as learning the tools which are required to employ this model. However, this goes without saying, the benefits are endless because it allows IT and Software Development teams to work in unison. The benefits DevOps introduces to organizations may include simplifying the maintenance of systems, improving security and freeing up resources to develop new features with fewer bugs in shorter time frames.

#### Agile Development for Project Management
Most traditional companies follow the "waterfall" methodology when they start an IT or Software Development related project. However, there's a pitfall to this approach and it prevents teams from delivering products until they're completely finished. Essentially, there's nothing in between for the client to experience or provide to their customers.

During this part of the seminar, Hiroaki Kawabuchi from CI&T presented a case study from one of their recent projects. Kawabuchi-san, or "bucci" as he likes to be called, explained how Agile concepts are utilized in the various phases of development.

Luckily I was able to jot down some key takeaways detailing how Agile development works, from a beginner's perspective. Think of it as the steps required to get a car running. You need the body, the engine, the wheels, and the operator. Until all requirements are satisfied, you're unable to drive. However, using an agile development process you start off with something that is fully operation during each phase of development. Your end goal might be a luxury car, however, that's an impractical expectation at the start of a project. So, at step one of the Agile development process you might have a skateboard, step two a bicycle and step three a car. During each phase your clients are able to work with something and your team is able to constantly implement features, such as a bike with a light (more efficient backend feature) or a car with tinted windows (new frontend feature).

It's important to note that the Agile movement isn't a methodology, but rather, a set of values and principles used to develop different project management frameworks. What I've described above can be implemented into what's called the Scrum methodology, which essentially is an "iterative and incremental agile software development framework for managing product development". Scrum development has been widely embraced by teams across the entire IT and software development landscape. Project leaders in the [Department of Defense](https://www.mitre.org/sites/default/files/publications/MITRE-Defense-Agile-Acquisition-Guide.pdf) have even decided to modernize their IT infrastructure by using methods using Scrum methodologies.

#### Implementing DevOps Into a Waterfall Society
The last talk was provided Tsuyoshi Ushio. Tsuyoshi is a Senior Technical Evangelist at Microsoft. Before I explain the talk, I want to provide a description of what a Technical Evangelist does, and why they're incredibly important. There's a concept in the field of social dynamics called critical mass which is when something is widely adopted and utilized by everyone, such as the way Google and Uber are used today. What Tsuyoshi does is build support, generate momentum for a certain product or concept, and implement it into a team's development process. It then becomes their solution for solving complex problems and building software, scaling infrastructure, etc.

The deeper meaning of Tsuyoshi's presentation was to describe the problems with Japan and why companies are having a difficult time implementing DevOps and Agile development concepts into their culture. What Japan is having a problem with is accepting more 外国人 (gaikokujin) or people from foreign countries into their companies and giving them a voice to express different ways to approach and solve problems. Utilizing this approach allows companies to appeal to the foreign market, evolve, and continue to grow.

However, many traditional Japanese companies fail to let this happen. There's an inherited risk of identity loss when foreigners are included in a company that employs primarily Japanese individuals. However, that fear also prevents Japan from being innovative and technologically relevant.

I greatly welcome any criticism or feedback in response to this article. If there are any inaccuracies, please let me know by sending me an [email](https://www.josephbleroy.com/contact/) or by leaving a comment. A significant portion of this was written from my personal point of view, however, I've tried to eliminate any personal bias I might have toward Japan and its culture. I've attended over a dozen events and conferences over the past several years, which by no means qualifies me as an expert, but rather someone who wants to see Japan succeed and work with more foreign countries and individuals.

#### References

* [Japanese Management in Change: The Impact of Globalization and Market Principles](http://www.springer.com/us/book/9784431550952)
* [An Introduction to Configuration Management](https://www.digitalocean.com/community/tutorials/an-introduction-to-configuration-management)
* [Learnings from a DevOps Hackfest with Karadamedica](http://microsoftdevops.github.io/devops/2016/05/24/karadamedica.html)
