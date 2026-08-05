---
title: "AWS FCAJ Agent Forge – Deepdive"
date: 2026-08-01
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---
# Event Report: AWS FCAJ Agent Forge – Deepdive (Day 1)

### Event Overview

This was an advanced **L300** workshop held with the First Cloud AI Journey (FCAJ) community. AWS speakers shared practical knowledge for engineers and businesses that want to build complete Agentic AI systems, from proof of concept to production.

### Event Objectives

- Learn about performance, scalability, security, and governance challenges when using Agents in real systems.
- Understand the three main Amazon Bedrock AgentCore components: Runtime, Identity, and Gateway.
- Practice tool installation, source configuration with Kiro, and Agent deployment on AWS.

### Speakers

- **Nghĩa** — Senior Speaker who led the session and explained Agentic AI foundations and enterprise architecture.
- **Hải Anh** — Hands-on lab host who guided project setup, dependency installation, and Agent testing.

### Workshop Format

The workshop is a three-day series that moves from foundations to production deployment with Amazon Bedrock AgentCore.

1. **Day 1 (August 1): AgentCore Foundations** — Runtime, Gateway, Identity, and core Agent concepts.
2. **Day 2 (August 8): Memory, Evaluations, Observability & Optimization** — memory management, evaluation, monitoring, and performance tuning.
3. **Day 3 (August 15): DevOps, Policies & Production Best Practices** — DevOps, policies, security, and production practices.

### Key Highlights

#### Agentic AI and autonomy

**Agentic AI** can work toward a goal instead of only answering one prompt at a time. An Agent can plan, choose tools, perform steps, review results, and adjust its plan. For example, an Agent asked to deploy an app on AWS could build the app, create a Docker image, push it to a registry, deploy cloud services, check the system, and report the result.

The workshop described a range of autonomy levels: deterministic agents that follow fixed rules, reactive agents that respond without planning, goal-oriented agents that plan toward a result, learning agents that improve from experience, and multi-agent systems where coding, testing, security, and DevOps agents work together.

#### Amazon Bedrock AgentCore

Amazon Bedrock AgentCore is an AWS service for building, deploying, and operating AI Agents in production. Its managed infrastructure lets developers focus on Agent logic. Key benefits include serverless runtime, automatic scaling, built-in security, and support for the Agent lifecycle from development and testing to deployment and operations.

#### Runtime, memory, and streaming

AgentCore Runtime is a managed production environment. Agents start on demand, and each run uses an isolated **Firecracker MicroVM** for security and a consistent runtime. The service scales with request volume and supports session state.

- **Session Memory** keeps context within a session or conversation.
- **Long-term Memory** stores information for future sessions.
- **Context Management** controls and optimizes the context sent to the language model.
- **Streaming Response** and **Progress Updates** return partial output and status early for long-running work.

#### Identity and security

AgentCore supports secure access with JSON Web Tokens (JWT), Amazon Cognito, AWS IAM, and service-to-service authentication. Agents should follow **least privilege**, record activity with AWS CloudTrail, and protect data in transit with HTTPS/TLS.

Important practices include using Amazon VPC when network isolation is needed, storing sensitive information in AWS Secrets Manager, applying least privilege to IAM roles and policies, and monitoring with Amazon CloudWatch and AWS CloudTrail.

#### Gateway, middleware, and human approval

Amazon Bedrock AgentCore Gateway connects Agents to tools, APIs, and external services. It routes requests and centralizes API management, authentication, authorization, and monitoring. Middleware can transform data, cache responses, retry temporary failures, and provide logging and monitoring.

For important actions such as financial approval, bulk notifications, or publishing content, **human-in-the-loop (HITL)** requires human approval before the Agent continues.

#### Hands-on practice

The session followed the Agent workflow: **Reason** to understand the request, **Plan** to break down the work, **Execute** to call tools or APIs, and **Reflect & Adapt** to assess and improve the result.

Examples included customer-support chatbots, data-analysis assistants, multi-step business automation, and software-development Agents. The workshop also covered prompt engineering, clear instructions, enough context, role and boundary definition, output formatting, workflow caching, parallel work, timeouts, and error handling.

### What I Learned

#### Technical knowledge

- I understand Agentic AI and the autonomy range from deterministic agents to multi-agent systems.
- I understand the Runtime, Gateway, and Identity architecture of Amazon Bedrock AgentCore.
- I learned how Agents plan, use tools, and complete multi-step work.
- I learned the security role of JWT, Amazon Cognito, IAM, and least privilege.

#### Deployment knowledge

- I understand the basic process for building and deploying production Agents.
- I know how Agents can integrate with external APIs and tools.
- I understand why HITL is needed for actions that require approval.
- I learned basic prompt-engineering and workflow-optimization techniques.

#### Key takeaways

- Build small, focused Agent functions before building a complex system.
- Put security and access control first when an Agent uses resources.
- Monitor, evaluate, and improve Agents using real results.
- Design systems to be scalable and maintainable.

### Event Experience

Day 1 of **AWS FCAJ Agent Forge – Deepdive** gave me a clear view of how to build and operate enterprise AI Agents. The examples explained the path from understanding a request and planning to tool use and task completion, while introducing the Runtime, Gateway, and Identity components of Amazon Bedrock AgentCore.

The mix of theory and examples in process automation, customer support, and software development created a useful foundation for the next workshop sessions.

> **Overall assessment:** The workshop provided a strong foundation in Agentic AI and Amazon Bedrock AgentCore, from concepts and architecture to production deployment, while emphasizing security, scalability, lifecycle management, and tool integration.

## Event photos

{{< event-image src="images/4-EventParticipated/Event_4_8_pic1.jpg" alt="Event photo 1" >}}

{{< event-image src="images/4-EventParticipated/Event_4_8_pic2.jpg" alt="Event photo 2" >}}

{{< event-image src="images/4-EventParticipated/Event_4_8_pic3.jpg" alt="Event photo 3" >}}

{{< event-image src="images/4-EventParticipated/Event_4_8_pic4.jpg" alt="Event photo 3" >}}
