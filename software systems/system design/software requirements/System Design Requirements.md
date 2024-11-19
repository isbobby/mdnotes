We write softwares to perform some tasks. There's something the system should do - this set of expectation of **what** the system should does is **functional requirement**.

The system can produce an outcome in many ways, we also expect the system to do it in certain ways. This expectation of **how** the system should behave is **non-functional requirement**.

## Functional Requirements
Describes the what - they describe features, capabilities and behaviours of the system.

The key characteristics are
1. directly related to user tasks
2. business-driven
3. have observable outputs

## Non-Functional Requirements
They specify the **quality** of the system, and the constraints the system must adhere to.

The key characteristics are
1. quality attributes - such as performance, scalability, reliability
2. constraint driven - the constraints limit how functional requirements can be implemented
3. impacts overall system design

The non-functional requirements drive how a system is designed, and should be considered regardless of the software.
1. [Software Performance](Software%20Performance.md)
2. [Software Scalability](Software%20Scalability)
3. [Software Availability](Software%20Availability)
4. [Software Extensibility](Software%20Extensibility)
5. [Software Reliability](Software%20Reliability)
6. [Software Security](Software%20Security.md)
7. [Software Maintainability](Software%20Maintainability)
8. Software Portability
9. Software Localisation

## Differentiation

|             | Functional                           | Non-Functional                        |
| ----------- | ------------------------------------ | ------------------------------------- |
| Focus       | Features and behaviour of the system | Quality and constraints of the system |
| Purpose     | Defines "what" the system does       | Define "how" the system does things   |
| Validation  | By tests and demonstrations          | By performance testing                |
| Examples    | Login, payment processing            | Performance, scalability, compliance  |
| Impact      | Related to user interaction          | Related to system architecture        |
| Documenting | User stories, functional specs       | Architectural design, SRS             |
