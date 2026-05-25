# How to approach a system design problem?
# Steps:
## Step 1. Understand the problem and define all requirements
### 1. Functional Requirements
FRs are the requirements that the end user specifically demands as the basic functionalities that system should provide. These are must haves.
Example:
- *What are the core features of the app?*
- *What are the edge cases we need to consider?*

### 2. Non-Functional Requirements
NFRs are the system characteristics, standard, constraints of the system. These are the quality gates which the system must satisfy so that it can work effectively.
Example:
- *System should be fault tolerant*
- *System should be able to handle millions of requests*
### 3. Extended requirements
These are nice to have requirements and can be out of scope of the system.
Example:
- *System should have metrics and analytics?*
- *System should have health and performance monitoring?*
## Step 2. Understand the scale of the system
*Here we need to do the back-of-the-envelope estimation(quick rough calculation).* 
We need to get an estimate of things like:
- User traffic: Daily Active Users (DAU), Requests per second (RPS)
- Read/Write ratio of the system
- How much storage will be needed and type SQL/NoSQL.
## Step 3: High level design
In this step we need to draw a very high level diagram and trace the user request from client -> server -> database. Identify the system components such as load balancers, API Gateways, Web/Application servers and storage layer.
## Step 4: Database Design
In this step define the following:
1. Different Entities and relationships between them
2. How many table/collections will be needed
3. SQL/NoSQL which is better?
4. Databases scaling, partitioning and sharding
### Step 5: API Design
Define the APIs that will be required for the system.
1. We don't have to write the complete code, just need to define the interfaces and methods.
2. For example: createAccount(String name, String email);
### Step 6: Detailed Design