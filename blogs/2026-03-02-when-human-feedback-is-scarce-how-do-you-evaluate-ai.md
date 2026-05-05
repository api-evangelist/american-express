---
title: "When Human Feedback Is Scarce, How Do You Evaluate AI?"
url: "https://americanexpress.io/when-human-feedback-is-scarce-how-do-you-evaluate-ai/"
date: "2026-03-02T00:00:00-05:00"
author: ""
feed_url: "https://americanexpress.io/feed.xml"
---
<p>Evaluating AI systems is easy… until it isn’t.</p>
  <p>For many user-facing applications like travel planning, clinical note
    drafting, or conversational agents, there is no single “right answer.”
    The most reliable signal of quality is human feedback: ratings,
    preferences, or real-world behavior. That’s because quality is
    subjective, contextual, and often best judged by people. As a result,
    human feedback such as ratings, preferences, and real-world behavior is
    the most reliable signal we have.</p>
  <p>But in early-stage systems and research prototypes, that signal is often
    too sparse, too expensive, or too slow to guide development. This
    creates a gap between how AI systems are evaluated in research settings
    and how they need to be evaluated in real-world deployment.</p>
  <p>This challenge sits at the heart of a new ICLR paper, <a href="https://arxiv.org/abs/2512.17267"><strong><em>AutoMetrics:
          Approximate Human Judgments with Automatically Generated
          Evaluators</em></strong></a>, authored by
    researchers from American Express and Stanford HAI.</p>
  <p>The work introduces a practical open-source framework for transforming small amounts
    of human feedback into scalable and interpretable evaluation metrics,
    helping teams move from prototypes to production with greater
    confidence.</p>
  <h2 id="from-expensive-human-labels-to-practical-metrics">From Expensive Human Labels to Practical Metrics</h2>
  <p>Today, when evaluating AI systems, there is often a trade-off between two
    imperfect options:</p>
  <ul>
    <li>
      <p><strong>Human evaluation:</strong> Accurate but costly and slow.</p>
    </li>
    <li>
      <p><strong>LLM-as-a-Judge</strong>: Fast and inexpensive but can be brittle and often
        poorly aligned with what users actually care about.</p>
    </li>
  </ul>
  <p>AutoMetrics offers a third path.</p>
  <p>The key idea is simple but powerful: instead of relying on a single
    evaluator, AutoMetrics learns a weighted combination of evaluation
    metrics that best matches human judgment, using fewer than 100 feedback
    points.</p>
  <p><img alt="Evaluate AI" src="https://americanexpress.io/_post_assets/when-human-feedback-is-scarce-how-do-you-evaluate-ai/img/image1.jpg" /></p>
  <p>The framework operates in four steps:</p>
  <ol>
    <li>
      <p><strong>Generate candidate metrics</strong></p>
      <p>AutoMetrics automatically creates task-specific evaluation criteria
        (e.g., clarity, usefulness, policy compliance) using LLMs.</p>
    </li>
    <li>
      <p><strong>Retrieve existing metrics from MetricBank</strong></p>
      <p>The system draws from <strong>MetricBank</strong>, a curated library of 48
        well-documented evaluation metrics spanning tasks like
        summarization, dialogue, code generation, and safety.</p>
    </li>
    <li>
      <p><strong>Learn how to combine them</strong></p>
      <p>Using lightweight regression, AutoMetrics identifies which metrics
        matter most and how they should be weighted to best predict human
        feedback.</p>
    </li>
    <li>
      <p><strong>Report interpretable evaluators</strong></p>
      <p>The output isn’t just a score—it’s a breakdown of <em>why</em> a system is
        performing well or poorly.</p>
    </li>
  </ol>
  <p>The result is an evaluation signal that is data-efficient, adaptive, and
    explainable.</p>
  <h2 id="stronger-alignment-with-human-judgment">Stronger Alignment with Human Judgment</h2>
  <p>The paper analyzes AutoMetrics across five diverse tasks, including
    dialogue, product description generation, code completion, and travel
    planning. Across these settings, AutoMetrics consistently outperforms
    strong baselines.</p>
  <p><img alt="Evaluate AI 2" src="https://americanexpress.io/_post_assets/when-human-feedback-is-scarce-how-do-you-evaluate-ai/img/image2.jpg" /></p>
  <p>It improves correlation with human ratings by up to 33.4% compared to
    standard LLM-as-a-Judge approaches.</p>
  <ul>
    <li>
      <p>Performance saturates with approximately 80 human feedback examples,
        making it practical even for low-data settings.</p>
    </li>
    <li>
      <p>The learned metrics remain stable under irrelevant changes and
        sensitive to real quality degradations, a key requirement for
        trustworthy evaluation.</p>
    </li>
  </ul>
  <p>In other words, AutoMetrics doesn’t assign scores, it behaves like a
    reliable proxy for how people actually judge quality.</p>
  <h2 id="evaluation-that-can-drive-optimization">Evaluation That Can Drive Optimization</h2>
  <p>One of the most compelling findings goes beyond measurement. The authors
    show that AutoMetrics can be used as a proxy reward signal to optimize
    an AI agent, matching or even exceeding the performance of systems
    trained with a fully verifiable reward. In a realistic
    airline-assistance benchmark, agents optimized with AutoMetrics improved
    at the same rate as those trained with explicit ground-truth rewards.</p>
  <p>This opens the door to human-aligned optimization in domains where
    rewards are subjective, ambiguous, or hard to formalize.</p>
  <h2 id="why-this-matters">Why This Matters</h2>
  <p>For practitioners building real-world AI systems, AutoMetrics points to
    a future where:</p>
  <ul>
    <li>
      <p>Evaluation adapts as products evolve</p>
    </li>
    <li>
      <p>Small amounts of user feedback go much further</p>
    </li>
    <li>
      <p>Metrics are understandable enough to guide iteration, not just
        leaderboard scores</p>
    </li>
  </ul>
  <p>By releasing AutoMetrics and MetricBank as open-source tools, the
    authors aim to make adaptive, human-aligned evaluation a standard part
    of the AI development workflow.</p>
  <h2 id="looking-ahead-evaluation-that-keeps-pace-with-ai">Looking Ahead: Evaluation That Keeps Pace with AI</h2>
  <p>As AI systems move faster from prototype to production, evaluation can
    no longer be an afterthought or a bottleneck. AutoMetrics shows that
    it’s possible to ground evaluation in human judgment without requiring
    massive labeling efforts, and to do so in a way that remains explainable
    adaptive, and actionable.</p>
  <p>The broader implication is clear: evaluation itself must become a
    learning system. By discovering what users actually value and
    translating that signal into scalable metrics, AutoMetrics reframes
    evaluation as a first-class component of AI development, rather than a
    scorecard at the end. These metrics can then be used to optimize AI
    Agent configurations.</p>
  <p>For teams building AI in open-ended, user-facing domains, this work
    points toward a future where small amounts of real feedback can drive
    rapid iteration, reliable optimization, and more human-aligned systems
    from day one.</p>
  <p>As the community continues to explore adaptive evaluation, AutoMetrics
    provides both a practical toolkit and a compelling blueprint for how we
    measure progress in AI—when the only true reference is human judgment.</p>
  <p><strong>Read the full ICLR paper:</strong><br />
    <a href="https://arxiv.org/abs/2512.17267"><em>AutoMetrics: Approximate Human Judgments with Automatically Generated
        Evaluators</em></a></p>
  <p><em>Note: This research is part of an industrial affiliate program.</em></p>
