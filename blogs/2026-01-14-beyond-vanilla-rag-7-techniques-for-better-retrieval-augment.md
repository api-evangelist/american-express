---
title: "Beyond Vanilla RAG: 7 Techniques for Better Retrieval-Augmented Generation"
url: "https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/"
date: "2026-01-14T00:00:00-05:00"
author: ""
feed_url: "https://americanexpress.io/feed.xml"
---
<hr />
    <div class="fix-self-rag">
      <p>Large Language Models (LLMs) are trained on vast datasets, yet they
        still struggle when queries require information outside their training
        data. Correct responses to challenging queries might involve proprietary
        information, recent events, or specialized knowledge not captured during
        training. One popular approach to mitigate this issue is
        Retrieval-Augmented Generation (RAG), which enhances LLMs by leveraging
        external knowledge to deliver better responses.</p>
      <p>The standard, or “vanilla,” RAG process involves the following steps:</p>
      <ol>
        <li><strong>Document Chunking</strong>: Splitting a document or article into smaller,
          manageable chunks.</li>
        <li><strong>Vectorization</strong>: Using an embedding model to transform these
          chunks into vector representations and store them in a vector store
          along with relevant metadata.</li>
        <li><strong>Similarity Search</strong>: When a query is received, the system
          vectorizes the query using the same embedding model and performs a
          similarity search to retrieve the top k chunks that are most
          relevant to the query.</li>
        <li><strong>Response Generation</strong>: The query, along with the top k chunks, is
          passed to an LLM to generate a response based on the retrieved
          information.</li>
      </ol>
    </div>
    <p>While vanilla RAG is effective in many cases, it is not without
      limitations. It may fail to retrieve the most relevant chunks or
      generate accurate responses, especially for more complex or nuanced
      questions. These limitations have driven significant research efforts
      aimed at enhancing the basic RAG approach.</p>
    <p>In this blog post, we will explore seven advanced RAG approaches grouped
      by core strategies, including: Reasoning-based, Retrieval reliability,
      and Knowledge structure-enhanced. Each of these is an improvement from
      vanilla RAG and each results in better responses from the LLM. By the
      end of this post, you’ll have a clearer understanding of these advanced
      techniques and the types of applications they are best suited for.</p>
    <p><strong>Reasoning-based</strong>: Self-RAG, ActiveRAG, Chain-of-Note, RAFT</p>
    <p><strong>Retrieval reliability</strong>: CorrectiveRAG, Adaptive-RAG</p>
    <p><strong>Knowledge structure-enhanced:</strong> Graph-Enhanced RAG</p>
    <h2 id="reasoning-based">Reasoning-Based</h2>
    <h3 id="self-rag">Self-RAG</h3>
    <p><img alt="Self Rag Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage1.jpg" /></p>
    <div class="adjust-bullet-spacing">
      <p>The Self-RAG [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-self-rag">1</a>] approach leverages a fine-tuned model to make more
        informed decisions during the question-answering process. Unlike the
        vanilla RAG approach, which always retrieves additional context,
        Self-RAG introduces a conditional retrieval mechanism. Here’s how it
        works:</p>
      <ol>
        <li>
          <p><strong>Initial Query and Conditional Retrieval</strong>:
            The process starts with a query being sent to the model, which then
            decides whether extra context needs to be retrieved from the vector
            store. If retrieval is necessary, the model retrieves relevant
            chunks.</p>
        </li>
        <li><strong>Chunk Evaluation and Response Generation</strong>:
          For each retrieved chunk, a two-fold evaluation takes place:
          <ul>
            <li>The model checks if the chunk is relevant to the query.</li>
            <li>Regardless of the chunk’s relevance, the model generates a preliminary response.</li>
          </ul>
        </li>
        <li><strong>Self-Reflection and Validation</strong>:<br />
          The generated response, alongside the query and the chunk, is then
          passed through the model again to evaluate whether:
          <ul>
            <li>The response is supported by the chunk.</li>
            <li>The response is useful for answering the question.</li>
          </ul>
        </li>
        <li>
          <p><strong>Re-Ranking Based on “Tokens”</strong>:<br />
            Self-RAG ranks the retrieved chunks based on three key factors
            (tokens): Relevance, Usefulness, and Supportiveness (though this
            ranking step is not depicted in diagram). The top <em>k</em> re-ranked
            chunks are selected.</p>
        </li>
        <li><strong>Final Answer Generation</strong>:<br />
          Finally, the top <em>k</em> ranked chunks are sent back to the model one
          last time, along with the original query, to generate the final,
          refined answer (not depicted in diagram).</li>
      </ol>
    </div>
    <p>Self-RAG excels at handling single-hop questions, where the answer can
      be found within a single retrieved chunk. Its success across various
      benchmarks, such as PopQA, TriviaQA, PubHealth, ARC-Challenge,
      Biography, and ASQA, is attributed to the multiple rounds of
      self-reflection and reasoning achieved through repeated LLM calls. This
      iterative process significantly enhances the model’s reasoning capacity
      and ensures higher accuracy.</p>
    <div class="fix-self-rag">
      <p>However, there is a trade-off. Self-RAG requires:</p>
      <ul>
        <li>Fine-tuning two large language models.</li>
        <li>Multiple calls to one fine-tuned LLM during the inference stage.</li>
      </ul>
    </div>
    <p>While these factors contribute to its superior performance, they also
      make Self-RAG less cost-effective, especially for applications requiring
      real-time responses or operating under strict computational budgets.</p>
    <h3 id="activerag">ActiveRAG</h3>
    <p><img alt="Active Rag Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage2.jpg" /></p>
    <p>ActiveRAG [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-active-rag">2</a>] is a unique approach that can be thought of as dual
      tasking in parallel. On one hand, a Chain-of-Thought (CoT) query is sent
      to an LLM to generate a step-by-step reasoning response for the
      question. Simultaneously, after retrieving relevant chunks based on the
      query, these chunks are sent to the LLM along with one of four knowledge
      construction prompting strategies, which enhances the LLM’s reasoning
      process. For example, one strategy helps the LLM better understand the
      query and context leveraging the retrieved context.</p>
    <p>In the final cognitive nexus step, ActiveRAG integrates the reasoning
      result from the reasoning process to identify potential errors in the
      original CoT response, ultimately producing the final, refined answer.</p>
    <p>ActiveRAG has outperformed several benchmarks, including Natural
      Questions (NQ), TriviaQA, PopQA, and WebQ, demonstrating its strength in
      single-hop questions. This improvement is largely due to the explicit
      expansion of the LLM’s reasoning capabilities in the knowledge
      construction step, coupled with the cognitive nexus step, which
      self-checks the CoT response against the retrieved information.</p>
    <p>Unlike some other approaches, ActiveRAG does not require fine-tuning of
      any large or small LMs. However, it does involve multiple calls to LLMs,
      which can lead to higher latency and increased computational costs.</p>
    <h3 id="chain-of-note">Chain-of-Note</h3>
    <p><img alt="Chain-of-Note Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage3.jpg" /></p>
    <div class="fix-self-rag">
      <p>The Chain-of-Note [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-chain-of-note">3</a>] approach leverages a fine-tuned model. In this
        approach, retrieved chunks of information along with the query are
        passed to the model. The model’s response not only provides the final
        answer but also includes explanatory notes on how the answer was derived
        from the retrieved chunks, reducing the risk of hallucination. There are
        three types of notes that can be generated:</p>
      <ul>
        <li><strong>Relevant (contains the answer)</strong>: The chunk directly provides the
          correct answer.</li>
        <li><strong>Irrelevant (model knows the answer)</strong>: The model already knows the
          answer independently of the retrieved chunk.</li>
        <li><strong>Irrelevant (model doesn’t know the answer)</strong>: The chunk does not
          help, and the model acknowledges uncertainty.</li>
      </ul>
    </div>
    <p>Chain-of-Note has outperformed key benchmarks such as Natural Questions
      (NQ), TriviaQA, and WebQ, particularly excelling at single-hop
      questions. This improvement is due to the model’s additional
      self-refinement steps before producing the final answer, which
      strengthens its reasoning abilities.</p>
    <p>During inference, only a single call to the fine-tuned model is
      required, which keeps operational efficiency high. However, the data
      collection process for fine-tuning a model can be resource intensive.
      For instance, Chain-of-Note leveraged ChatGPT to generate answers with
      notes for 10,000 questions sampled from the NQ dataset. While the
      approach is effective, using a more robust, commercial LLM for
      production deployments may offer better performance. Chain-of-Note could
      be expensive when developers need to fine-tune a model with their
      dataset for their use cases.</p>
    <h3 id="raft">RAFT</h3>
    <p><img alt="RAFT Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage4.jpg" /></p>
    <p>RAFT [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-raft">4</a>] employs a fine-tuned model. During the training phase, in
      addition to relevant chunks, RAFT intentionally includes irrelevant
      chunks in the training datasets. The model generates responses in a
      Chain-of-Thought (CoT) style, incorporating reasoning and citing
      relevant documents. This training strategy equips the model to identify
      and disregard irrelevant chunks during inference, enabling it to provide
      accurate answers even when such chunks are mistakenly retrieved.</p>
    <p>RAFT has surpassed benchmarks such as PubMed, HotPotQA, HuggingFace,
      Torch Hub, and TensorFlow Hub, showcasing its effectiveness in tackling
      multi-hop questions. This capability means that generating a correct
      answer often requires synthesizing information from multiple chunks
      located in different contexts. RAFT’s exceptional performance on
      multi-hop questions stems from its enhanced reasoning capacity, allowing
      it to analyze both relevant and irrelevant chunks concurrently.</p>
    <p>At first glance, RAFT may resemble the Chain-of-Note approach; however,
      its distinctive training methodology sets it apart. By deliberately
      including irrelevant chunks during training, RAFT bolsters the
      robustness of its fine-tuned model, ensuring better performance in
      inference scenarios where irrelevant information may arise. Moreover, it
      is specifically trained to excel at multi-hop questions rather than just
      single-hop inquiries.</p>
    <h2 id="retrieval-reliability">Retrieval Reliability</h2>
    <h3 id="correctiverag">CorrectiveRAG</h3>
    <p><img alt="CorrectiveRag Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage5.jpg" /></p>
    <p>The CorrectiveRAG [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-corrective-rag">4</a>] approach introduces a Retrieval Evaluator,
      fine-tuned using T5-large, to assess the relevance of retrieved chunks
      to the query. If the retrieved chunks are accurate, the query and chunks
      are sent to a large language model (LLM) to generate the final response.
      However, if the chunks are deemed incorrect, CorrectiveRAG rolls back to
      a web search, and the query along with the search results are then
      passed to the LLM for response generation.</p>
    <p>In cases where ambiguity arises, CorrectiveRAG combines both the
      retrieved chunks from the vector store and the search results from the
      web search, along with the query, and sends them to the LLM for
      generating a response.</p>
    <p>CorrectiveRAG has outperformed benchmarks such as PopQA, Biography,
      PubHealth, and ARC-Challenge, demonstrating its effectiveness at
      handling single-hop questions. This performance boost is due to the
      added evaluation of the correctness of retrieved chunks and the
      integration of web search results when necessary.</p>
    <p>Compared to previous approaches, CorrectiveRAG only requires fine-tuning
      a small language model (LM) as a classifier. During inference, it
      requires just one call to a small LM and one call to an LLM, with
      occasional web searches. This makes CorrectiveRAG more cost-effective
      than methods that rely on fine-tuning one or more LLMs.</p>
    <p>However, the reliance on web searching, when retrieved chunks are
      ambiguous or incorrect, is a double-edged sword. While web searches can
      improve the accuracy of answers, certain use cases prohibit external web
      searching, limiting the applicability of CorrectiveRAG in such
      environments.</p>
    <h3 id="adaptive-rag">Adaptive-RAG</h3>
    <p><img alt="Adaptive-Rag Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage6.jpg" /></p>
    <p>Adaptive-RAG [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-adaptive-rag">4</a>] employs a classifier, fine-tuned on T5-large, to
      assess the complexity of incoming queries. If a query is classified as
      native, the query is sent directly to an LLM for a response. For simple
      queries, the system retrieves chunks only once; the query and the
      retrieved chunks are then sent to the LLM for a response. In the case of
      complex queries, Adaptive-RAG retrieves chunks multiple times before
      passing the query and chunks to the LLM for final response generation.</p>
    <p>Adaptive-RAG has outperformed benchmarks such as SQuAD, Natural
      Questions (NQ), TriviaQA, MuSiQue, HotPotQA, and 2WikiMultiHopQA,
      achieving superior results in either accuracy or cost-effectiveness.
      This indicates that Adaptive-RAG is efficient at handling both
      single-hop and multi-hop questions, particularly when incoming queries
      may vary in complexity.</p>
    <p>While both Adaptive-RAG and CorrectiveRAG utilize a small LM as a
      classifier, they differ in their approaches. Adaptive-RAG classifies the
      query prior to any chunk retrieval. For native questions, the system
      sends them directly to the LLM without retrieving additional
      information. For complex questions, multiple retrievals are performed,
      but Adaptive-RAG does not revert to web searching. We are curious about
      the potential outcomes of combining Adaptive-RAG with CorrectiveRAG to
      leverage their respective strengths.</p>
    <h2 id="knowledge-structure-enhanced">Knowledge Structure-Enhanced</h2>
    <h3 id="graph-enhanced-rag">Graph-Enhanced RAG</h3>
    <p>Knowledge Graphs can significantly enhance the capabilities of vanilla
      RAG. To differentiate this general approach from Microsoft’s GraphRAG,
      which is not the focus of this blog post, we refer to this approach as
      Graph-Enhanced RAG [<a href="https://americanexpress.io/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/#ref-graph-enhanced-rag">7</a>].</p>
    <p><img alt="Graph-Enhanced Rag Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage7.jpg" /></p>
    <p>The vanilla RAG approach begins with a vector database that stores the
      vectors of chunks (e.g., c1, c2, and c3) extracted from articles or
      documents. The texts of these chunks are stored as metadata linked to
      their corresponding vectors (this metadata is not depicted in the
      diagram). When a query is received, the system retrieves the top k
      chunks from the vector database based on their similarity to the query.</p>
    <p><img alt="Graph-Enhanced Rag2 Image" src="https://americanexpress.io/_post_assets/beyond-vanilla-rag-7-techniques-for-better-retrieval-augmented-generation/img/ragimage8.jpg" /></p>
    <p>By incorporating a knowledge graph, we can conceptualize the chunks as
      nodes (e.g., n1, n2, and n3), with the chunk texts and vectors
      represented as properties of these nodes. Relationships can then be
      established among the nodes, such as a NEXT relationship that describes
      the sequence of the chunks.</p>
    <p>Subsequently, LLMs can be employed to extract entities from the nodes,
      including people (e.g., p1 and p2), organizations, companies (e.g., c1),
      locations, and more. These entities become new nodes within the graph.
      Relationships between the extracted entities and the original chunks can
      also be added (e.g., MENTIONS).</p>
    <p>Additionally, relationships among the newly created nodes can be
      identified using LLMs. For example, person p1 works for company c1, and
      person p2 also works for company c1. We can infer that p1 and p2 are
      colleagues based on this information.</p>
    <p>One of the advantages of Graph-Enhanced RAG is that it does not require
      fine-tuning any models. When a new context is introduced, developers can
      easily extend the existing knowledge graph. This approach is
      particularly effective for multi-hop questions and overarching tasks,
      where responses may need to synthesize content from across an entire
      article or document, such as generating a summary.</p>
    <p>However, Graph-Enhanced RAG does necessitate calls to LLMs for entity
      and relationship extraction from the texts of the nodes. Additionally,
      depending on the size and content of the documents, storing the
      knowledge graph may require significant space, which can increase costs.</p>
    <h2 id="summary">Summary</h2>
    <table class="advanced-rag-table">
      <colgroup>
        <col style="width: 4%;" />
        <col style="width: 23%;" />
        <col style="width: 22%;" />
        <col style="width: 23%;" />
        <col style="width: 25%;" />
      </colgroup>
      <thead>
        <tr>
          <th>#</th>
          <th>Name</th>
          <th>Need to fine-tune model(s)?</th>
          <th>Fine-tuned model size x amount</th>
          <th>Applications</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>1</td>
          <td>Self-RAG</td>
          <td>Yes</td>
          <td>7b x 2</td>
          <td>single-hop QA</td>
        </tr>
        <tr>
          <td>2</td>
          <td>ActiveRAG</td>
          <td>No</td>
          <td>n/a</td>
          <td>single-hop QA</td>
        </tr>
        <tr>
          <td>3</td>
          <td>Chain-of-Note</td>
          <td>Yes</td>
          <td>7b x 1</td>
          <td>single-hop QA</td>
        </tr>
        <tr>
          <td>4</td>
          <td>RAFT</td>
          <td>Yes</td>
          <td>7b x 1</td>
          <td>
            <p>single-hop QA</p>
            <p>multi-hop QA</p>
          </td>
        </tr>
        <tr>
          <td>5</td>
          <td>CorrectiveRAG</td>
          <td>Yes</td>
          <td>0.77b x 1</td>
          <td>single-hop QA+</td>
        </tr>
        <tr>
          <td>6</td>
          <td>Adaptive-RAG&nbsp;</td>
          <td>Yes</td>
          <td>0.77b x 1</td>
          <td>
            <p>single-hop QA</p>
            <p>multi-hop QA</p>
          </td>
        </tr>
        <tr>
          <td>7</td>
          <td>Graph-Enhanced RAG</td>
          <td>No</td>
          <td>n/a</td>
          <td>
            <p>single-hop QA</p>
            <p>multi-hop QA</p>
            <p>overarching task</p>
          </td>
        </tr>
      </tbody>
    </table>
    <p>The table above provides a high-level summary of the seven advanced RAG
      approaches, highlighting whether each method requires fine-tuning a
      model, the model size, and the applications for which each approach is
      best suited.</p>
    <p>Overall, there is no single RAG approach that universally fits all use
      cases. Developers should choose a RAG approach based on their specific
      question types (e.g., single-hop and multi-hop) and other requirements
      (e.g., latency is a critical factor and can fine-tune a model.) For
      instance, if the question set includes a mix of single-hop and multi-hop
      questions, and latency is a critical factor, developers might consider
      starting with Adaptive-RAG. Conversely, if the questions are complex and
      necessitate information drawn from various parts of the context, and
      fine-tuning a model is not feasible, Graph-Enhanced RAG may be the
      better option. Advanced RAG approaches will continually evolve with new
      ones emerging in the future. By leveraging these advanced RAG
      techniques, we can improve the quality of LLM answers for our use cases.</p>
    <p><em>This article summarizes publicly available research on
        retrieval-augmented generation (RAG) techniques. It is provided for
        informational purposes only.</em></p>
    <h3 id="academic-papers"><strong>Academic Papers</strong></h3>
    <p><a id="ref-self-rag"></a>
      <strong>1. Self-RAG</strong><br />
      Asai, A., Wu, Z., Wang, Y., Sil, A., &amp; Hajishirzi, H. (2023). <em>Self-RAG:
        Learning to Retrieve, Generate, and Critique through Self-Reflection.</em>
      arXiv preprint arXiv:2310.11511.
      <a href="https://arxiv.org/pdf/2310.11511?utm_source=chatgpt.com">https://arxiv.org/pdf/2310.11511</a></p>
    <p><a id="ref-active-rag"></a>
      <strong>2. ActiveRAG</strong><br />
      Xu, Z., Liu, Z., Liu, Y., Xiong, C., Yan, Y., Wang, S., Yu, S., Liu, Z.,
      &amp; Yu, G. (2024). <em>ActiveRAG: Autonomous Knowledge Assimilation and
        Accommodation through Active Retrieval.</em> arXiv preprint
      arXiv:2402.13547.
      <a href="https://ar5iv.labs.arxiv.org/html/2402.13547?utm_source=chatgpt.com">https://ar5iv.labs.arxiv.org/html/2402.13547</a></p>
    <p><a id="ref-chain-of-note"></a>
      <strong>3. Chain-of-Note</strong><br />
      Yu, W., Zhang, H., Pan, X., Cao, P., Ma, K., Li, J., Wang, H., &amp; Yu, D.
      (2023). <em>Chain-of-Note: Enhancing Retrieval-Augmented Generation with
        Knowledge Organization.</em> arXiv preprint arXiv:2311.09210.
      <a href="https://arxiv.org/pdf/2311.09210?utm_source=chatgpt.com">https://arxiv.org/pdf/2311.09210</a></p>
    <p><a id="ref-raft"></a>
      <strong>4. RAFT</strong><br />
      Zhang, T., Patil, S. G., Jain, N., Shen, S., Zaharia, M., Stoica, I., &amp;
      Gonzalez, J. E. (2024). <em>RAFT: Adapting Language Model to
        Domain-Specific Retrieval-Augmented Generation Tasks.</em> arXiv preprint
      arXiv:2403.10131.
      <a href="https://arxiv.org/pdf/2403.10131?utm_source=chatgpt.com">https://arxiv.org/pdf/2403.10131</a></p>
    <p><a id="ref-corrective-rag"></a>
      <strong>5. CorrectiveRAG</strong><br />
      Yan, S.-Q., Gu, J.-C., Zhu, Y., &amp; Ling, Z.-H. (2024). <em>Corrective
        Retrieval-Augmented Generation.</em> arXiv preprint arXiv:2401.15884.
      <a href="https://arxiv.org/pdf/2401.15884?utm_source=chatgpt.com">https://arxiv.org/pdf/2401.15884</a></p>
    <p><a id="ref-adaptive-rag"></a>
      <strong>6. Adaptive-RAG</strong><br />
      Jeong, S., Baek, J., Cho, S., Hwang, S. J., &amp; Park, J. C. (2024).
      <em>Adaptive-RAG: Learning to Adapt Retrieval-Augmented Large Language
        Models through Question Complexity.</em> arXiv preprint arXiv:2403.14403.
      <a href="https://arxiv.org/pdf/2403.14403?utm_source=chatgpt.com">https://arxiv.org/pdf/2403.14403</a></p>
    <h3 id="graph-enhanced-rag-and-related-resources"><strong>Graph-Enhanced RAG and Related Resources</strong></h3>
    <p><a id="ref-graph-enhanced-rag"></a>
      <strong>7. Neo4j Product Examples – SEC EDGAR Data Prep Repository</strong><br />
      Neo4j Product Examples. (n.d.). <em>Data Preparation for SEC EDGAR
        Knowledge Graph Examples.</em> GitHub repository. Retrieved November 2025,
      from <a href="https://github.com/neo4j-product-examples/data-prep-sec-edgar">https://github.com/neo4j-product-examples/data-prep-sec-edgar</a></p>
    <p><strong>8. DeepLearning.AI Short Course</strong><br />
      DeepLearning.AI. (n.d.). <em>Knowledge Graphs &amp; Retrieval-Augmented
        Generation [Online short course].</em> Retrieved November 2025, from
      <a href="https://www.deeplearning.ai/short-courses/knowledge-graphs-rag/">https://www.deeplearning.ai/short-courses/knowledge-graphs-rag/</a></p>
