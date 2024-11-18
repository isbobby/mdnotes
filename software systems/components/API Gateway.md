# Overview
When we have multiple micro-services with numerous APIs, the following system functions are essential
1. load balancing
2. service blowing
3. Gray release
4. caller able to call an independent service
5. caching
6. monitoring
7. Authentication
8. Reverse proxying
9. Request packaging for intended micro-service, such as padding default parameters
API gateway manages the key question - how can we route the caller's request to the intended micro-services. 

API gateway is a special server, it is the only entrance to the entire system.

API gateway - API oriented, serially centralised management and control service.

Encapsulates system internals from clients.

Service degradation - 



### References
Zhao, J. T., Jing, S. Y., & Jiang, L. Z. (2018). _Management of API Gateway Based on Micro-service Architecture_. IOP Conference Series: Journal of Physics: Conference Series, 1087(3), 032032. https://doi.org/10.1088/1742-6596/1087/3/032032