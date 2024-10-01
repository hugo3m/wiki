---
layout: page
permalink: /design_pattern/architecture_pattern/layer
title: Layer
---

![Type object](/wiki/assets/design_pattern/architecture_pattern/layer/base.png)

Each layer should be independent. However, be careful for **anti-pattern**, such as passing request from presentation layer to persistence layer through business layer without the business layer have not done anything.