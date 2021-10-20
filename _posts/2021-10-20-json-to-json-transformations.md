---
layout: post
title:  "JSON-to-JSON transformation: A pragmatic approach"
category: OSS
permalink: "/:categories"
---
Sometimes there’s a need to perform a same-format transformation from one structure into another. There are also times, when you need to provide a generic way to perform the transformation, without the need to change your source code.

We came into such a situation when we implemented a [GitHub action](https://github.com/camelotls/actions-jira-integration), which shall process JSON data from several sources, all differently structured and generate Jira tickets from the data received.

We didn’t want to create strong ties between the individual tools which served with the input to our open source action, perhaps by implementing functionality to handle the different types of input. Also, our requirement was that code changes shouldn’t become necessary even if our GitHub action changes in the way it expects the input data to be. Agents which use our action should be able to change their output in the structure which our action expects at any given time, without the need to change the source code.

So, the idea came up to have an npm lib at hand, which receives a mapping that tells how to process an input JSON file in order to transform it to the desired target structure.
The main focus was to easily address particular fields and to destructure deeply nested object hierarchies in JSON documents.

The idea is rather simple: the mapping shall define the field where the data shall be take from the input and where it shall arrive in the output.
Here we have an example of what it looks like, in its simplest form:

```
const target = {
    flat: [
        {
            fieldOne: 1,
            fieldTwo: 2,
            fieldThree: 3,
            fieldFour: 4,
            that: {
                here: 'here I am'
            }
        }
    ]
}l
```

