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

## [Radio, checkbox should in form tab](http://react.tips/radio-buttons-in-reactjs/)

When you handle Radio, checkbox such options, you should put these stuff in a form as follows. Otherwise the radio will not act as you wish.

```javascript
<form >
                <div className="flex items-center mr-4">
                    <input checked={record.Class === 'OPD'} onChange={handleChange}  id={record['ID']} type="radio" value="OPD" name="colored-radio" className="w-4 h-4 text-red-600 bg-gray-100 border-gray-300 focus:ring-red-500 dark:focus:ring-red-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600" />
                    <label for="red-radio" className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300">OPD</label>
                </div>
                <div className="flex items-center mr-4">
                    <input checked={record.Class === 'OPR'} onChange={handleChange} id={record['ID']} type="radio" value="OPR" name="colored-radio" className="w-4 h-4 text-green-600 bg-gray-100 border-gray-300 focus:ring-green-500 dark:focus:ring-green-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600" />
                    <label for="green-radio" className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300">OPR</label>
                </div>
                <div className="flex items-center mr-4">
                    <input checked={record.Class === 'OMD'} onChange={handleChange} id={record['ID']} type="radio" value="OMD" name="colored-radio" className="w-4 h-4 text-purple-600 bg-gray-100 border-gray-300 focus:ring-purple-500 dark:focus:ring-purple-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600" />
                    <label for="purple-radio" className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300">OMD</label>
                </div>
                <div className="flex items-center mr-4">
                    <input checked={record.Class === 'OMR'} onChange={handleChange} id={record['ID']} type="radio" value="OMR" name="colored-radio" className="w-4 h-4 text-orange-500 bg-gray-100 border-gray-300 focus:ring-teal-500 dark:focus:ring-teal-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600" />
                    <label for="teal-radio" className="ml-2 text-sm font-medium text-gray-900 dark:text-gray-300">OMR</label>
                </div>
            </form>

```

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
