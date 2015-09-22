## Goals
Ellis Island intends to resolve the following issues with data engineering:

1.  Storage of large volumes
2.  Low latency read/write with a lambda architecture for real-time data pipelines
3.  Production-ready API access without additional implementation

With Hadoop, we solve **1** by nature of its design.  Horizontally scalable, its distributed nature allows for little worries to grow at a linearly cost-efficient rate as our own data grows, with constant and consistent performance that similarly scales.  In addressing creating such data pipelines that are performant and production ready, we are leveraging a [lambda architecture](http://lambda-architecture.net/) design to create a batch and speed layer depending on specific needs.  This is further elaborated in [design](##Design).  Finally, to make Ellis Island production ready, we provided REST APIs at both ends for write and consumption.  Users do not need to understand the underlying implementation behind Ellis Island to incorporate their needs into their own projects.

##Design
<p align="center"><img src=http://i.imgur.com/tzAFJ6i.png heigt="500"></p>
In considering the need to store historical data for long duration of time versus