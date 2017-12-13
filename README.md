# Agera-ComplexRepository

In our [earlier github project](https://github.com/AnkitDroidGit/Agera-Repositories) we have learned about Repository and have implemented `MutableRepository`.
Lets now learn complex repository.


# Complex Repositories

A complex repository in Agera can react to other repositories (or observables, in general) and produce values by transforming data obtained from other data sources, synchronously or asynchronously. The data supplier by this repository is kept up to date in reaction to the events from the sources it's observing.

The description provides a declaration of:

- When it's going to react to data sources
- Where this reaction is going to take place (in which thread)
- What it's going to do with the data

        topAlbumsRepository = repositoryWithInitialValue(emptyList())
          .observe(accountManager,      // When the account changes...
                   networkStatus)       // or when we get online...
          .onUpdatesPer(10000)          // but at most every 10 seconds...
          .goTo(networkExecutor)        // on the network thread...
          .getFrom(albumsFetcher)       // fetch albums from API...
          .thenTransform(                   
            functionFrom(String.class)
            .unpack(jsonUnpacker)       // unpack JSON items...
            .map(jsonToAlbum)           // for each album...
            .filter(fiveStarRating)     // filter the best...
            .thenLimit(5))              // and give us the first five of those!
          .compile();
          
- Each line is called a directive.
- The IDE will help you add directives, as there are some rules to follow:
- Always start with `.repositoryWithInitialValue(value)`
- Continue with the event source `.observe(...)`
- Frequency of reaction `.onUpdatesPer(...)` or `.onUpdatesPerLoop()`
- Data processing flow: `.getFrom(...)`, `.mergeIn(...)` See below (Operators).
- Before you finish with `.compile()`, the previous directive must start with `.then (.thenTransform, .thenMerge, .thenGetFrom...)`.
- There are other miscellaneous configurations that we'll talk about later: `.notifyIf(...)`, `.onDeactivation(...)`, `.onConcurrentUpdate(...)`, etc.



## Operators

Data transformation is done with operators.

#### Operator  -> Description

- getFrom(Supplier) -> Ignores input and returns a value from a supplier
- transform(Function) -> Applies a function to an input and returns the result
- mergeIn(Supplier, Merger) -> A function that takes the input from the previous directive and the input from an external supplier to produce a result
- sendTo(Receiver) -> The input is not modified, but it's sent to an external receiver
- bindWith(Supplier, Binder) -> Similar to sendTo, but the input is sent to a binder that takes a second parameter from a supplier.
- check(Predicate).or() -> Evaluates a condition for "early exit"


## The Result wrapper class

The functional interfaces `Supplier`, `Function` and `Merger` are defined not to throw any exceptions, but realistically, many operations may fail. To help capture the failures, Agera provides a wrapper class `Result`, which encapsulates the (either successful or failed) result of a fallible operation, which we call an attempt. The attempt can be implemented as a `Supplier`, `Function` or `Merger` that returns a `Result`.

The data processing flow provides failure-aware directives that allow terminating the flow in case of failure:

- `.attemptGetFrom(Supplier).or`...`;`
- `.attemptTransform(Function).or`...`;`
- `.attemptMergeIn(Supplier, Merger).or`...`;`

The `Result` wrapper class can also store absent (or present) values.

## let's connect to learn together
  - [Twitter](https://twitter.com/KumarAnkitRKE)
  - [Github](https://github.com/AnkitDroidGit)
  - [LinkedIn](https://www.linkedin.com/in/kumarankitkumar/)
  - [Facebook](https://www.facebook.com/freeankit)
  - [Slack](https://ankitdroid.slack.com)

  
  
  ### License
  
      Copyright 2017 Ankit Kumar
      
      Licensed under the Apache License, Version 2.0 (the "License");
      you may not use this file except in compliance with the License.
      You may obtain a copy of the License at
  
         http://www.apache.org/licenses/LICENSE-2.0
  
      Unless required by applicable law or agreed to in writing, software
      distributed under the License is distributed on an "AS IS" BASIS,
      WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
      See the License for the specific language governing permissions and
      limitations under the License.