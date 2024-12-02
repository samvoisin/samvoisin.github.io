---
layout: post
title: Lessons of Vertical Slice Architecture
date: 2024-12-01 12:39:00-0400
description: Lessons learned while converting a monolithic application to a feature-focused vertical slice architecture.
tags: software architecture application-design
categories: engineering
related_posts: false
thumbnail: assets/img/vsa_post_thumb.png
---

## Background

Over the last several months I have been leading the software development effort for an application I am developing with my creative partner. The details of the app aren't germane to this blog post. Instead, I want to share some important takeaways from the design process. Specifically, I want to share some key insights from converting the app from a monolith of features and dependencies to a vertical slice architecture.

## What is a vertical slice architecture?

Vertical slice architecture (VSA) is a software design approach that structures your application around features rather than technical layers. It emphasizes separating application functionality into distinct, end-to-end slices that handle a specific feature or use case independently. Each slice contains the requirements to handle the implementation of the related feature. This includes the presentation (e.g. API endpoints), the feature's infrastructure needs, the application code, and the business/domain logic.

VSA stands in contrast to the more traditional layered architecture in which all API endpoints are defined together in the presentation layer, all infrastructure needs are defined together in the infrastructure layer and so on.

Milan Jovanović provides an excellent and detailed description of a VSA [here](https://www.milanjovanovic.tech/blog/vertical-slice-architecture) if you want to read more.

## Motivation for the switch

In the conception stage we experimented with a number of different feature implementations for our app. We were building quickly, adding features as they became important, and removing features that seemed irrelevant. The project's structure was very simple which allowed for quickly prototyping new ideas.

Eventually, we settled on the features that we intend to offer at launch and a loose roadmap for adding new features in the future. Structure became a serious consideration at this point. We chose to adopt VSA so we can tailor feature implementations to specific requirements. After all, our goal is to quickly stand up new functionality with minimal impact on existing features. VSA ensures we are able to do this effectively by grouping dependencies based on the feature they support.

## Lessons learned

### What to implement in each layer

Each vertical in the VSA has layers similar to the traditional approach: domain, application, infrastructure, and presentation.

Infrastructure and presentation are fairly straightforward in their contents. Infrastructure handles the "plumbing" of the application. Examples are things like third party service communication (i.e. interacting with external APIs) and database access. The presentation layer represents the aspects of the application that are exposed to users. API endpoints are defined here and well as the corresponding data validation models (e.g. Pydantic).

Differences between the application and domain layers are a bit more nuanced. The purpose of the domain layer is to encapsulate the **core business logic and rules**. It represents the core of the application, independent of technical concerns like APIs or databases. The domain layer knows what the business logic is but doesn't concern itself with how it is used or executed. This is why then domain layer does not depend on the other layers as we will soon see.

The application layer orchestrates application workflows and use cases. It acts as a mediator between the domain and infrastructure layers. The difference between application and domain layers is that the application layer knows how to use the business logic to achieve a specific goal, but it doesn't define the logic itself.

### Dependency between layers

In the traditional layered architecture, dependencies flow from the presentation and infrastructure layers inward to the application layer and ultimately to the most inner layer - the domain layer.

The VSA dependency graph is somewhat more complicated.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/vsa-dep-graph.png" class="img-fluid rounded z-depth-1 w-50" %}
    </div>
</div>

Note that the domain layer has no outgoing dependencies as stated above. The presentation layer is solely dependent on the logic defined by the application layer. Finally, the infrastructure layer is closely coupled with the application and domain layers.

### Dealing with cross-feature code

One drawback to VSA is the potential for code duplication. Some shared logic may need to be duplicated across slices. Managing shared code requires careful planning. I have seen a number of recommendations on handling this problem. One recommendation is to create a cross-cutting `shared` or `common` module. I don't really like this suggestion for two reasons: (1) it violates the main idea of the VSA and (2) it creates a submodule that ends up being an incoherent hodgepodge of code which is challenging to navigate and maintain.

An alternative approach which I prefer is to use application services to coordinate needs between features. Let's use an example to illustrate this: Assume there is a feature for getting data about items in our inventory. There is an `items` table with information about the items and a `get_item_info` endpoint which allows us to read data on the items from the `items` table.

Now we want to implement a feature that allows us to create sales listings of the items we have. We want the sales feature to be able to access data from the `items` table. We define a `create_sales_listing` endpoint which creates an element in the `sales` table which incorporates data about `items`. Instead of re-implementing the logic that pulls data from the `items` table in the sales feature, we have the logic that underlies the `create_sales_listing` endpoint call to the `get_item_info` endpoint. Thus we have avoided duplicating code or moving code out of its relevant vertical.

It is important that you do not let the sales feature directly depend on the item feature's internal logic or database tables. This would create tight coupling and violate the independence of slices.

## Conclusion

 By Adopting a vertical slice architecture and structuring our codebase around features, we've achieved greater modularity and flexibility. This approach has allowed us to rapidly implement new functionality with minimal impact on existing features. While adopting VSA has drawbacks, the benefits are substantial for the overall design. The shift to VSA has aligned well with our goals for quick development and maintainable architecture.