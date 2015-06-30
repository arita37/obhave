This document describes the interactions of certain components in model level. There is another document for the technical architecture, which is based upon modern component-based front-end solutions, middle-ware and RESTful APIs. OBHave has been designed for cloud in a scalable way to provide Reinforcement Learning powered recommendations.

In order to tackle Cold Start-problem OBHave-provides API for setting-up the initial state, or adding new concepts from existing learning sets or pure data.

# Minimum Viable Products

## MVP 0.1

### Implement RPC-getUserSuggestions

Each user is assigned an UUID, but in order to protect the privacy of the user, this UUID is never persisted; this way OBHave! will never be able to steal user behavior data of the customer. The recommendations are provided through "stateless" user groups, that contain content item lists, which generate content stream suggestions for the user. The user data is persisted with the user group UUID.

Each user has a chance to be kicked out of a user group, which increases as the user behaves in a non-conforming way compared to the rest of the group. Each user belongs to at least three user groups and the recommendations are chosen with probabilities from these groups. These probabilities are adjusted with the "user mood" API. The content can also be filtered and sorted, just like any RESTful API with the GET query parameters, that come from the navigational interactions of the user.

The amount and size of user groups can be adjusted by the amount of computing power the web-application provider is willing to spend.

RPC-getUserSuggestions will return a list of UUID's of content items, which are then queried through a RESTful API abstraction of the existing service for example with GET todos?uuid=1;3;5;7;11. This can be easily implemented with RESTful API libraries or frameworks like Apigility. This is the standard behavior of any RESTful API.

### Implement RPC-changeUserGroup

When user is kicked out of user group, the UUID will be sent to changeUserGroup RPC. With the UUID the user sends the past browsing history regarding content items consumed, which is contained by the browsers LocalStorage; if this list is too short or refresh is requested due to predefined paramters, the OBHave! will ask the abstraction of the existing service for predefined RPC-call which provides data about users past behavior. Certain parameters may be included to seek "similar" user groups as the one from which the user got expelled.

Users UUID and behavior data will be sent to the OBHave!-RESTful API, which will first filter the data for a mood profile, which is used to query the existing user groups. This is done due to performance reasons, it would be impossible to match users real behavior data to real user group behavior in a real-time environment. The mood profile is kind of a tag, that is calculated from entropy analysis of the behavior patterns; popular patterns are stored in cache and used for user group change matches. Less popular patterns are persisted for recommendation adjustment and even less popular patterns are garbage collected or merged.

Mood profile should match with several user groups and the user would be assigned to one of the with a weighted probability.

### To be continued...

 - Certain user groups should be very long lived to avoid problems during peak hours; another solution would be to use proxy groups, where the real user group is hidden and similar mood-profile user group will replace it temporarily.
 - NoSQL solution is probably the best implementation for the user groups, since they go well with BASE-architecture. GraphDatabase such as OrientDB, might be a good solution also.
 - The user groups would assign the new data to the OBHave! as sum transactions, e.g. 9 views of X, 11 views of Y.
 - Content Items contain UUID's and GET parameters for RESTful filtering and sorting. User Groups can update their Content Item lists... or users which have consumed X% of the content should have X% more chance of getting kicked out of the user group to a next and more evolved user groups that are similar (user mood profile).
 - New user group creation should be completely separate process from existing user groups.
 - When user groups submit user data, they command the system to "evolve", which at certain percentage might create a new user group. The data contains mood parameters and navigational event information, which is tied to predefined reward/penalty heuristics.
