---
title: "Discord Open Sources Osprey: A Safety Rules Engine Processing Millions of Rules per Second"
seoTitle: "Discord's Osprey: Open Source Safety Rules Engine for Real-time Threat Mitigation"
seoDescription: "Explore Discord's newly open-sourced Osprey, a high-performance safety rules engine built with Rust and Python, capable of processing 2.3 million rules per second for real-time content moderation."
datePublished: 2026-03-31T18:06:32.063Z
cuid: cmnexhvum001n2fk0fwzlc3vh
slug: discord-open-sources-osprey-a-safety-rules-engine-processing-millions-of-rules-per-second
cover: https://i.ibb.co/wh6t1pyK/discord-open-sources-osprey-safety-rules-engine.png
ogImage: https://i.ibb.co/wh6t1pyK/discord-open-sources-osprey-safety-rules-engine.png
tags: python, rust, scalability, discord, open-source, content-moderation

---

Keeping a massive platform like Discord safe from harmful content requires an equally massive and efficient system. Managing 400 million daily actions and mitigating threats in real-time is no small feat. Discord has now open-sourced **Osprey**, their advanced safety rules engine, offering a look into how they achieve this monumental task.

As [InfoQ reports](https://www.infoq.com/news/2026/03/discord-osprey/?utm_campaign=infoq_content&utm_source=infoq&utm_medium=feed&utm_term=Architecture+%26+Design), Osprey is designed to process an astonishing 2.3 million rules per second, enabling Discord's trust and safety teams to deploy real-time threat mitigations with unparalleled speed and scale.

## What is Osprey?

Osprey is a high-performance, real-time safety rules engine. Its primary purpose is to evaluate incoming actions and content against a set of predefined rules to identify and act upon potentially harmful behavior. This allows Discord to proactively protect its users from various threats, ranging from spam to more severe forms of abuse.

## A Polyglot Architecture for Scale

One of Osprey's most fascinating aspects is its polyglot architecture, combining the strengths of Rust and Python:

*   **Rust Coordinator**: At the core, a Rust-based coordinator manages traffic and orchestrates the rule evaluation process. Rust's performance, memory safety, and concurrency features make it an ideal choice for this high-throughput, low-latency component.
    
*   **Python Workers**: Stateless Python workers are responsible for executing the actual logic of the safety rules. These workers utilize a Python-based Domain-Specific Language (DSL) called **SML** (Safety Markup Language) for defining rules.
    

This architectural split offers a significant advantage: Rust handles the heavy lifting of coordination and performance-critical operations, while Python, with its readability and ecosystem, provides flexibility and ease of rule authoring for trust and safety teams. This allows these teams to rapidly iterate and deploy new rules without deep engineering involvement, balancing performance with agility.

## Authoring Rules with SML

Trust and safety teams leverage SML to define the intricate logic that governs Osprey's decision-making. SML provides a human-readable way to express complex conditions and actions. For example, a rule might check for specific keywords, user behavior patterns, or content types, and then trigger an action like flagging content, issuing a warning, or taking down a message.

This abstraction empowers non-engineers to contribute directly to the platform's safety mechanisms, making the system highly adaptable to evolving threats.

## The Impact of Open Sourcing

By open-sourcing Osprey, Discord is contributing a robust and battle-tested solution to the broader community. This move benefits:

*   **Other Platforms**: Companies facing similar content moderation and safety challenges can learn from or even adopt Osprey's architecture and principles.
    
*   **Developers**: Rust and Python developers can explore a real-world, high-scale application of their respective languages, gaining insights into building resilient, performant systems.
    
*   **The Industry**: It fosters collaboration and innovation in the crucial domain of online safety, potentially leading to better tools and practices across the internet.
    

## Conclusion

Osprey stands as a testament to how thoughtful architectural design and the strategic use of polyglot programming can tackle some of the most demanding challenges in modern online platforms. Discord's decision to open-source this powerful engine is a significant contribution, offering valuable lessons and tools for anyone striving to build safer digital spaces at scale. It underscores the importance of real-time processing and flexible rule management in maintaining a secure user experience.