# Design a system like Uber

### What does uber do?
*First think about the what the app does*
- Rider can request rides
- Nearby riders will be found
- One driver accepts
- Rider can track driver at real time
- Trip starts
- Rider should be able to make payment
- Trip ends and history is stored
### Functional Requirements
*What must the system do? These are the core features of the app.*
For uber:
1. Riders should be able to input start location and destination and get a fare estimate
2. Rider can request rides based on fare
3. System should be able to match a rider with a driver
4. Driver can accept/reject
5. Real time tracking
6. Fare estimation
7. Payment
8. Trip history
### Non-functional Requirements
*These are overall characteristics, standards and constraints of the app. They do not change what the system does, but dictate how effectively it does it.*

| Requirement       | Meaning                      |
| ----------------- | ---------------------------- |
| Scalability       | Handle millions of users     |
| Low latency       | Matching must be fast        |
| High availability | App should almost never fail |
| Fault tolerance   | Survive server failures      |
| Real time         | GPS updates instantly        |
| Consistency       | Correct trip/payment data    |
### Estimate scale
*Estimate things like daily active users (DAU), number of active rides etc.*
Suppose:
- 10 million DAU
- 1 million drivers
- 