---
layout: post
title:  "About Software Maintainability"
author: "Pablo Acuña" 
date:   2014-10-29 17:00:00
categories: quality
tags: architecture good-practices quality 
---

Maintainability is an important factor in software development. As developers, 
we need to ensure the best quality in our products in the event that other 
developers must continue developing the product at a later stage.
It’s hard to define maintainability, but some questions that can indicate how 
maintainable our software is, are:

- Is our software easy to change?
- Is our software easy to analyze?
- Can our software be easily extended once it is finished?
- Is the value of test coverage enough?
- How modular is our software?
- How much code reuse is present in our software?
- How much code can we reuse in order to extend the product?


The easiest way to evaluate the quality of a product is to use Quality Models.
But we can’t just use any model. We need to choose wisely and then adapt it to
the context of the product.

A typical and widely used standard is the ISO 9126, recently updated and 
renamed to ISO/IEC 25010. This upgraded version contains new characteristics 
that are more relevant to current software systems.


The ISO/IEC 25000 standard consists of 5 divisions:

- ISO/IEC 2500n - Quality Management Division
- ISO/IEC 2501n - Quality Model Division
- ISO/IEC 2502n - Quality Measurement Division
- ISO/IEC 2503n - Quality Requirements División
- ISO/IEC 2504n - Quality Evaluation Division

In order to realize an evaluation, we can take the quality model in the quality
model division, choose the characteristics and sub characteristics that are 
relevant to our product, and then find metrics that can give us objective 
results for the software. We also can realize a formal evaluation using the 
guides present in the Quality Evaluation Division.

The ISO/IEC 25010 model consists of the following characteristics:
 
- Functional Suitability
- Performance Efficiency
- Compatibility
- Usability
- Reliability
- Security
- Maintainability
- Portability

Our main focus is on maintainability, which consists of:

- Analyzability
- Changeability
- Modularity
- Testability
- Reusability

With these sub characteristics we can start to choose metrics. Some examples are:

- Modularity: relational cohesion, lack of cohesion in methods, afferent coupling, efferent coupling
- Reusability: code duplication
- Modifiability: instability, cyclomatic complexity, maintainability index, depth, 
parameters per function
- Testability: code coverage

There are many  alternatives that can help us to obtain these metrics. The 
most-used ones are static program analyzers. This type of software can bring a 
huge benefit to the quality of the product if is used and interpreted properly.

It’s important to notice that these measures should not be performed by someone
who doesn’t understand the software architecture and metrics correctly, 
especially since some should not be applied without previous analysis. For 
example, is not the same to analyse server-side code heavily object-oriented 
and client-side not object-oriented (like Javascript). There are different 
metrics for  different purposes and that is when experience and  expertise play
a huge role.

Developers should be aware of this software quality characteristics in order 
to build better software and reach a high level of maintainability on their 
products. When the software is maintainable, not only is good for the authors, 
but also for the future developers that will work on the code source. 

