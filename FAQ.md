# FAQ


## Q: Does it speed up an application?
Ans: The aim of nimn data format is to reduce the size of data. Processing the data or whether it can improve the performance deoend on implementation. However as you have the data structure in advance, it's processing can be faster.

## Q: What if I compress JSON instead parsing it to Nimn data format?
Ans: The size of Nimn data is smaller than compressed JSON.

## Q: I can't ensure the order of fields in data. Can I still use Nimn format?
Ans: You need to maintain the order of keys in schema which basically define the data structure. Fields in original data can be jumbled.

## Q: My data is dynamic in nature. How can I use Nimn format?
Ans. You'll have find the correct way to make it structured to use Nimn format.
