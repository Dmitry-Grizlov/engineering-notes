# engineering-notes
## My thoughts on the way to senior engineer

This will include the topics that I study, training project, thoughts on the **Designing Data-Intensive Applications** book by Martin Kleppmann.  
The program split into 12 weeks with the following activities:
### Monday - System Design
#### Take SD-x task, project it from ground up, wirte design doc
  Template:
  - Requirements
  - Scale assumptions
  - API
  - Data model
  - Architecture
  - Bottlenecks
  - Failure cases
  - Trade-offs

### Wednesday - Deep Backend Skill
#### Take DBS-x task and deep dive into it

### Friday - Production Engineering
#### Take PE-x task and add this feature to the project

### Saturday - Senior Thinking
#### Take SE-x task and do one from the list:
  - Write mini design doc
  - Describe Architectural Solution
  - Analyse company outage
  - Document lessons learned


### Sunday - Follow-up work on the project

All the tasks are created and are sitting in the [repo project](https://github.com/users/Dmitry-Grizlov/projects/1). 

Along the way I will use the read **Designing Data-Intensive Applications** to get theory on topics I'm working on as well as material to reflect my thougts after reading.  
The book will be sitting in the repo as well.  

For the test project I will work on ***Multi-Tenant Document Platform***, the source code will be added to the repo.

Project functionality:
- User can create documents
- Support document versioning
- PDF document generation
- Sharing links
- API
- Background processing

I will not dive deep into the business part of the project as here I'm training to create systems and this project will be a good example bacuase it will involve
- multi-tenant architecture
- permissions
- queues
- async jobs
- caching
- rate limiting
- observability
- scaling
- storage design
