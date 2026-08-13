# Scenario: Platform Engineering Build-Out

**Label:** `Interview Preparation`

## Prompt
> Design a self-service cloud platform that allows developers to provision compliant infrastructure without requiring direct access to cloud subscriptions/projects.

## Context
See the fully worked answer in [`platform-engineering/self-service.md`](../platform-engineering/self-service.md) — this scenario is the canonical Platform Architect interview prompt called out explicitly in this repository's structure, so it's worth having a complete, fluent answer ready.

## Problem
Enable application teams to move fast and independently while maintaining security/compliance guardrails, without a central team becoming either a bottleneck (manual gatekeeping) or losing control (broad standing access granted to everyone).

## Analysis
The tension is resolvable, not fundamental — self-service *within automated guardrails* lets teams move fast on the common case while policy-as-code enforces compliance structurally, removing the need for either manual gatekeeping or broad access grants (see [`docs/principal-architect-mindset.md`](../docs/principal-architect-mindset.md) Section 4 on governance vs. enablement).

## Decision (Summary — Full Detail in platform-engineering/self-service.md)
1. Service catalog interface exposing curated, pre-approved infrastructure patterns as parameterized requests.
2. Requests execute via CI/CD pipeline with a scoped, federated identity — requesters never get direct cloud credentials.
3. Policy-as-code validation gates every provisioning request automatically.
4. Full audit trail of every request for governance and analytics.
5. Fast, tracked exception path for genuine edge cases outside the catalog.

## Trade-offs
- A curated self-service catalog is fast and safe for the common case but requires ongoing investment to expand coverage, or teams will hit "not supported yet" too often and route around the platform.

## Follow-Up Depth for Interviews
Be ready to go deeper on: how the execution identity is scoped per request type (least privilege), how the catalog is versioned/evolved as a product (see [`platform-engineering/platform-architecture.md`](../platform-engineering/platform-architecture.md)), and how success is measured (time-to-first-deploy, developer satisfaction, per [`platform-engineering/developer-experience.md`](../platform-engineering/developer-experience.md)).
