---
layout: default
title: Next.js
date:   2021-09-11 20:28:05 -0400
categories: next
nav_order: 101

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

## About the dynamic pages

Notes:
1. You need to place the `key` into `[]`;
2. If you only use `getStaticProps`, then the return result must be like
  ```javascript
  return {
    props:{
      one of the key must be the `key`: content
    }
  }
 ```
3.   if you'd like to have params in `getStaticProps`, then you need to use `getStaticPath`
4.   the `getStaticPath` will get all `key`, then return the result must be like
  ```JS
  return {
  props: {
    props:content # Here if you use {content}, then the default export function will use different props as it's name. If {content}, then you can use `export default function Abc({content}).
}
}
 ```
5.   The function `getStaticProps` will use only one `params` from `getStaticPath`
6.   the export default function will use value of the  key `props` from `getStaticProps` function
