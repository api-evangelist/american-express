---
title: "Building Trust in AI-Powered Transactions with Amex Agentic Commerce Experiences (ACE) Developer Kit"
url: "https://americanexpress.io/building-trust-in-ai-powered-transactions-with-amex-agentic-commerce-experiences/"
date: "2026-04-22T00:00:00-04:00"
author: ""
feed_url: "https://americanexpress.io/feed.xml"
---
<h2 id="introduction-from-user-driven-to-agent-driven-commerce">Introduction: From User-Driven to Agent-Driven Commerce</h2>
      <p>What if purchases could be made on a Card Member’s behalf by an agent that understands what they need, when they need
        it, and how to execute the transaction?</p>
      <p>In this scenario, the Card Member doesn’t explicitly add items to a cart or tap “Buy Now.” Instead, an agent
        could recommend options and complete a purchase using an American Express account, based on permissions defined
        by the Card Member.</p>
      <p>At American Express, we’re building for this shift with the Agentic Commerce Experiences (ACE) Developer Kit.</p>
      <p>This shift challenges a core assumption of how payments work today.</p>
      <p>Traditional systems are built around user actions where a person browses, decides, and executes each step of a
        transaction. As agents begin to act on behalf of users, that interaction model may no longer hold. In a more
        autonomous agentic AI system, instead of repeatedly translating intent into action, the user defines intent
        once, and the agent continuously evaluates context, makes decisions, and executes when conditions are met. This
        is the new model the Amex Agentic Commerce Experiences (ACE) Developer Kit is designed to enable—bringing
        intent-driven, agent-powered transactions onto the American Express network with trust and control.</p>
      <h2 id="the-core-challenge-enabling-delegation">The Core Challenge: Enabling Delegation</h2>
      <p>This evolution introduced a complex problem: how do we help ensure that any agent taking delegated actions
        is explicitly authorized, controlled, and accountable?</p>
      <p>How can an agent acting on a customer’s behalf prove it has the required authority to make purchases? How can
        guardrails be set for the agent’s actions? Who is accountable if the agent makes a mistake? These questions require
        the payment ecosystem to adapt for agent-powered transactions.</p>
      <h2 id="what-it-takes-core-capabilities">What it Takes: Core Capabilities</h2>
      <p>As we explored these challenges in building the ACE Developer Kit, it became clear that enabling AI-powered payments
        required a new set of capabilities.</p>
      <ol>
        <li>
          <p><em>Establishing Trust Through Identity and Enrollment</em></p>
          <p>Trust begins with a clear, explicit setup: the Card Member provides their payment instrument for agents registered
            with Amex, completes issuer authentication, and defines controls on how that instrument can be used. This
            interaction model is supported by capabilities such as agent registration and account enablement, which help
            create a verifiable relationship between the Card Member, the agent, and the payment instrument.</p>
        </li>
        <li>
          <p><em>Representing Intent as Enforceable Boundaries</em></p>
          <p>At the time of purchase, the agent may play a role in both discovery and execution—recommending what to buy,
            selecting from relevant merchants, and completing the transaction. These decisions are guided by natural language
            instructions that express the Card Member’s intent, which ultimately helps determine what is purchased, how much
            can be spent, and with which merchants.</p>
          <p>With that in mind, we built an interaction model where intent captures qualifiers such as:</p>
          <ul>
            <li>What should be purchased</li>
            <li>Where it could be purchased from</li>
            <li>How much can be spent</li>
            <li>When additional approvals are required</li>
          </ul>
          <p>Intent helps define the boundaries within which the agent can operate, and the ACE Developer Kit helps make
            those boundaries enforceable in practice.</p>
        </li>
        <li>
          <p><em>Enforcing Boundaries at Execution</em></p>
          <p>Delegation often involves enforced boundaries, including spending limits, merchant restrictions, frequency
            controls, and conditional execution. These are applied at execution, helping to ensure agents operate within
            defined boundaries.</p>
        </li>
        <li>
          <p><em>Securing Execution Across the Transaction Flow</em></p>
          <p>At the payment authorization stage, payment credentials are designed to be used in a limited and controlled
            manner; actions are tied to verified intent, while execution is auditable and traceable.</p>
          <p>The ACE Kit is designed to support this through:</p>
          <ul>
            <li>Scoped payment credentials</li>
            <li>Short-lived authorization artifacts</li>
            <li>Strong verification mechanisms</li>
          </ul>
          <p>In this way, the agent can complete a payment within a defined framework.</p>
        </li>
      </ol>
      <h2 id="what-this-means-for-developers">What This Means for Developers</h2>
      <p>Instead of building complex payment and risk infrastructure from scratch, developers can integrate a framework that helps manage identity, intent, and execution in a consistent way.
        As the platform evolves, additional tools will further simplify integration and help accelerate development.</p>
      <h2 id="how-this-works-in-practice">How This Works in Practice</h2>
      <p>When these capabilities come together, an adapted model of payments emerges. In the ACE Developer Kit, this
        interaction model is implemented through a sequence of coordinated steps.</p>
      <p>Intent is first captured and stored as a structured contract. At the time of payment credentials
        issuance, Amex generates a scoped credential tied to that intent.</p>
      <p>Before a payment credential is generated, the ACE Kit is designed to validate:</p>
      <ul>
        <li>That the intent is still valid</li>
        <li>That constraints are satisfied</li>
      </ul>
      <h2 id="partner-integration-supporting-existing-commerce-flows">Partner Integration: Supporting Existing Commerce Flows</h2>
      <p>The ACE Developer Kit is designed to integrate with partner environments—including AI agent providers, platforms, and merchants—without requiring fundamental changes
        to their existing payment infrastructure. Partners interact with the Kit through a combination of synchronous
        APIs and asynchronous notifications. API-driven interactions allow partners to initiate enrollment, create
        intent, and request payment credentials, while event-driven notifications provide updates on state transitions
        such as authentication completion or transaction outcomes. This dual model allows partners to operate in both
        request-driven and event-driven environments, depending on their architecture.</p>
      <p>In a typical flow, the partner initiates a request to create or update an intent. Amex evaluates the request,
        applies risk controls, and determines whether criteria are met to permit execution. Scoped payment credentials
        are then issued and used by the merchant to process the transaction through existing payment rails.</p>
      <p><em>Security Protocols and Design Patterns</em></p>
      <p>Security is a foundational element of this architecture and is implemented across all stages of the lifecycle.</p>
      <p>All API interactions are authenticated using OAuth and mutual TLS, ensuring that each request is associated with
        a verified partner or agent identity. This allows access to be scoped, monitored, and revoked as needed.</p>
      <p>Sensitive payloads are protected using industry-standard encryption mechanisms, helping ensure account details
        and credentials remain protected in transit.</p>
      <p>The platform follows a zero-trust model with respect to client input. No request is accepted based solely on
        client-provided data. Instead, each step is validated using signed artifacts that bind to a specific
        intent. These artifacts are short-lived and include protections against replay, ensuring that requests cannot be
        reused or forged. This combination of authentication, encryption, tokenization, and verification allows ACE to
        enforce strong security guarantees without introducing unnecessary friction for developers.</p>
      <p><em>Scaling Across Ecosystems</em></p>
      <p>As adoption grows, the ACE Developer Kit is designed to scale and be interoperable with different standards
        and models.</p>
      <p>By decoupling intent and credential issuance, and keeping the transaction processing the same for merchants, ACE
        avoids dependency across components, allowing each to scale independently. This approach enables partners to
        onboard incrementally, extend their use cases over time, and operate at scale without requiring changes to the
        core model.</p>
      <p><em>Developer Experience and Integration Simplicity</em></p>
      <p>While the ACE Developer Kit enforces complex validation and security controls, the integration model is
        intentionally designed to remain straightforward. Developers interact with a small set of well-defined
        capabilities: enrollment, intent creation, credential retrieval, and lifecycle management, which map cleanly
        to existing application flows. This reduces the need for custom orchestration logic and allows developers to focus
        on building agentic experiences.</p>
      <p>To simplify integration, in the future, the platform will provide supporting tools such as SDKs, Agent Skills, an
        MCP server, and reference implementations. These abstractions encapsulate common patterns, allowing developers to
        focus on building user experiences rather than managing low-level details. Structured error responses and
        consistent request and response patterns help ensure that integrations are predictable and easier to debug.</p>
      <p>This balance between strong controls and simple integration is critical for enabling adoption at scale.</p>
      <h2 id="what-we-learned-key-principles">What We Learned: Key Principles</h2>
      <p>As we worked through these challenges, a few principles became clear.</p>
      <ul>
        <li>Delegation should always be explicit and verifiable, rather than implied.</li>
        <li>Intent should capture the Card Member’s goal in a way that can be enforced.</li>
        <li>Payment credentials should be scoped to the intent.</li>
      </ul>
      <p>These principles shaped how we approached the problem.</p>
      <h2 id="conclusion">Conclusion</h2>
      <p>The ACE Developer Kit brings a new payments model to life, providing developers with the capabilities
        to enable secure delegation, enforceable intent, and controlled execution on the American Express network.</p>
      <p>Developers can explore the APIs, integration patterns, and supporting tools on the American Express developer
        portal: https://developer.americanexpress.com</p>
