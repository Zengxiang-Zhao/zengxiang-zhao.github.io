---
layout: default
title: React
date:   2024-01-11 20:28:05 -0400
categories: React
nav_order: 12

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---
## Subcomponent cannot show up content correctly

That's because there's no key in the subcomponent, you should provide the key in the subcomponent to make sure the content is correct. React use key 
to check whether this component should be rendered. If you use MongoDB, then the _id is a good choice to be the key.
