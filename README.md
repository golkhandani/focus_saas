# Focus SaaS
I have started A prefix in my github account named `Focus` to collect what I could find on web about rare topics or One-page learning for beginner level topics;

I began this series with [focus_postgresql](https://github.com/golkhandani/focus_postgresql) and I hope I can release more and more on this series;

`focus_saas` repo will look at some challenges and some technical strategies related to `SaaS` applications;

## What the heck is a SaaS application?
Based on [WIKIPEDIA](https://en.wikipedia.org/wiki/Software_as_a_service): Software as a service (SaaS /sæs/[1]) (also known as subscribeware or rentware) is a `software licensing` and `delivery model` in which software is licensed on a subscription basis and is centrally hosted.
SaaS has become a common delivery model for many business applications, including office software, messaging software, payroll processing software, DBMS software, management software, CAD software, development software, gamification, virtualization,[5] accounting, collaboration, customer relationship management (CRM), management information systems (MIS), enterprise resource planning (ERP), invoicing, human resource management (HRM), talent acquisition, learning management systems, content management (CM), geographic information systems (GIS), and service desk management.[6] SaaS has been incorporated into the strategy of nearly all leading enterprise software companies.[7][8]


So basically it is a release strategy. However, there are tons of technical challenge related to this release method;


### Challenges of SaaS Application

Business Perspective
- Managing SaaS subscription lifecycle
- A customizable system (Or top 5 business model with strict rules)
- GDPR and HIPAA compliant 

Technical Perspective
- Managing multi-tenancy in different level
- Finding best `Architecture` solution based on business perspective
- Predict future changes that could affect on your `Architecture`
- Designing `database` to fullfil security and isolation requirement
- Implementing best deployment solution for zero downtime for all tenant




## Architecture importance in SaaS applications

There are three different layer that result four different Architecture;

- Infrastructure
- Database 
- Application

### 1. Complete isolation 
In this solution you deliver whole product from Infrastructure to application to your user;
Generally speaking, It's not the best solution for most businesses due to its costs;

![Complete isolation](/images/1-complete-isolation.jpeg)

| Pros | Cons |
| - | - |
| Complete isolation from physical level | Costs, costs and costs for each new tenant (and it means more expensive subscriptions) |
| Full control of client load for better optimizations | Updating of application need much more work on operation and deployment level |
| Each client load only affect on its own business not others | Extra work in operation team to handle new clients |
| Predicable IO (network,db,logs,etc) | - |
| Easier customization of Application for specific tenant | - |
| Easier in early stages of development | - |

> This solution can be used with some other as a solution for Massive clients
like what Github, Microsoft or Amazon do for their enterprise level customers.


### 2. Infrastructure-tenancy

In this solution we use a shared infrastructure for a-s
| - | - |
| Less cost in term of hardware | Shared Infrastructure means shared IO that could result lower Load capability |
| Easier to manage clients | Lower level of security that could be a drawback for some of your client |
| Doesn't need extra operation level works for setting up new system from scratch | Still need extra things for running application for new user |
| Easy customization of Application for specific tenant | - |


### 3. Application-tenancy

In this solution only separated part of your product is the database layer;
This method has the best balance between cost/development and separation of sensitive data;

![ Application-tenancy](/images/3-app-tenancy.jpeg)

| Pros | Cons |
| - | - |
| Easiest and fastest method from development perspective | Load of some bigger client could heavily affect on your other client |
| A good enough solution for startups | Controlling of database connection for each tenant handle in app |




### 4. Shared-tenancy
In this method all layer of application are shared between all tenants;


![Shared-tenancy](/images/4-shared-tenancy.jpeg)


| Pros | Cons |
| - | - |
| Really low cost in terms of hardware | Could cause serious performance issue even in medium applications |
| - | could be difficult from technical perspective due to many level of access control and separation that should be handled in application layer |
| - | In some cases it could be in conflict with privacy rules in specially in european union |
| - | Could result security issues or cross tenant access |


Most Important factors in choosing an Architecture are:

- Scalability
    1. Number of tenants.
    2. Storage per-tenant.
    3. Storage in aggregate.
    4. Workload.

- Tenant isolation
    1. Data isolation 
    2. performance (whether one tenant's workload impacts others).
    3. Per-tenant cost.

- Development complexity
    1. Changes to schema.
    2. Changes to queries (required by the pattern).

- Operational complexity
    1. Monitoring and managing performance.
    2. Schema management.
    3. Restoring a tenant.
    4. Disaster recovery.

- Customizability:
    Ease of supporting schema customizations that are either tenant-specific or tenant class-specific.


As [Microsoft](https://docs.microsoft.com/en-us/azure/azure-sql/database/saas-tenancy-app-design-patterns) says: The tenancy discussion is focused on the data layer. But consider for a moment the application layer. The application layer is treated as a monolithic entity. If you divide the application into many small components, your choice of tenancy model might change. You could treat some components differently than others regarding both tenancy and the storage technology or platform used.


Also its good to look at [this](https://docs.microsoft.com/en-us/azure/azure-sql/database/saas-tenancy-app-design-patterns#i-tenancy-models-compared) comparison;

There are really good article about this section in 
[ibm](https://developer.ibm.com/articles/cl-multitenantcloud/) and
[selleo.com](https://selleo.com/blog) blog


## Database importance in SaaS applications

There are four major database design in SaaS applications;

- Server Instance per tenant
- Database per tenant (single instance)
- Table per tenant
- Shared Table



### 1. Server Instance per tenant

Every tenant gets its own hardware for database instance and code will use different connection strings, no impact to SQL


| Pros | Cons |
| - | - |
| Complete physical isolation of tenant data | Cost |
| Control of the DB instance size based on the load exerted by the tenant | DB schema updates is much more harder than other approaches |
| Can have customized DB schema | - |
| Easier to roll out tenant specific code release | - |


### 2. Database per tenant
Every tenant gets his schema, but runs under the same physical database server


| Pros | Cons |
| - | - |
| DB level isolation of tenant data | Cost |
| Control of the DB instance size based on the load exerted by the tenant | DB schema updates is much more harder than other approaches |
| Can have customized DB schema | making coordination of code changes harder if dedicated app servers talk to the db. |
| Easier to roll out tenant specific code release | Will hit the system file size limits |

Depend on number of tenant it this method could be the best one or the worst one;


### 3. Table per tenant

User something like tenant_id as prefix for each table


To be honest this method not even worthy for review due to its critical performance issues;

 ** In my experience in linux, storing 100 directory with 100-200 file per directory could result better performance than 100 * 100-200 file all in one directory
and because how database systems store data (for example InnoDB engine store 1~3 file per DB) you should consider your number of predicted tenant in database design



### 4. Shared Table
Use tenant_id in each record;
Based on your project it could be the best solution if you be careful about all the indexing and security problems;

| Pros | Cons |
| Easy to run cross queries | No db level isolation of tenant data |
| Easy to manage and apply schema updates | No option to support schema changes specific to a tenant |
| - | mbalance of tenants can adversely affect the DB performance, creating hotspots |
| - | Trust the developer to provide the additional where condition |





It's good to see the discussion around this topic in [stackoverflow](https://stackoverflow.com/questions/2213006/how-to-create-a-multi-tenant-database-with-shared-table-structures) also look at [this](https://lobster1234.github.io/2016/11/03/multitenant-mysql/)