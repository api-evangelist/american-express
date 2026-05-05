---
title: "Optimizing Istio for Large-Scale Enterprise Applications"
url: "https://americanexpress.io/optimizing-istio-for-large-scale-enterprise-applications/"
date: "2026-03-30T00:00:00-04:00"
author: "Gurpreet Singh"
feed_url: "https://americanexpress.io/feed.xml"
---
<h2 id="overview">Overview</h2>
    <p>In today’s rapidly evolving cloud-native application landscape, adopting service meshes has become vital for effectively managing the complexities inherent in microservices architectures. Among the leading solutions, Istio stands out by offering a comprehensive suite of features, including traffic management, security, and observability.</p>
    <p>If a large enterprise is expanding its use of Istio, performance optimization should sit front and center in the overall implementation strategy. Below, I’ll delve into proven strategies for enhancing Istio’s performance in large enterprises.</p>
    <h2 id="sidecar-resource-usage-and-sizing">Sidecar resource usage and sizing</h2>
    <p>Managing Istio sidecar resources can pose significant challenges that often go unnoticed, potentially leading to application issues if not addressed properly. The default resource requests for the sidecar are 128Mi for memory and 100m for CPU, with limits set at 1Gi for memory and 2 cores for CPU.</p>
    <h3 id="when-to-update-cpu-requests">When to Update CPU Requests</h3>
    <ul>
      <li>The Envoy proxy consumes approximately 0.5 vCPU per 1000 requests per second. CPU requests should be increased when transactions per second (TPS) are high, and the Istio sidecar approaches its configured CPU limits.</li>
    </ul>
    <h3 id="when-to-update-memory-requests">When to Update Memory Requests</h3>
    <ul>
      <li>The Envoy proxy uses 50 MB of memory per 1,000 requests per second going through the proxy. When numerous entries—such as egress or import and export resources—are added to the namespace, the sidecar will require additional memory to manage these configurations effectively.</li>
    </ul>
    <p>The sample deployment configuration below provides a guide for modifying resource requests and limits. It’s crucial to specify limits for both CPU and memory; omitting these will result in limits being set to unlimited, which could lead to resource contention and instability.</p>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">spec</span><span class="pi">:</span>
  <span class="na">replicas</span><span class="pi">:</span> <span class="m">1</span>
  <span class="na">selector</span><span class="pi">:</span>
    <span class="na">matchLabels</span><span class="pi">:</span>
      <span class="na">app</span><span class="pi">:</span> <span class="s">myapp</span>
  <span class="na">template</span><span class="pi">:</span>
    <span class="na">metadata</span><span class="pi">:</span>
      <span class="na">labels</span><span class="pi">:</span>
        <span class="na">app</span><span class="pi">:</span> <span class="s">myapp</span>
      <span class="na">annotations</span><span class="pi">:</span>
        <span class="na">sidecar.istio.io/proxyMemoryLimit</span><span class="pi">:</span> <span class="s">3Gi</span>
        <span class="na">sidecar.istio.io/proxyCPULimit</span><span class="pi">:</span> <span class="s1">'</span><span class="s">3'</span>
        <span class="na">sidecar.istio.io/proxyCPU</span><span class="pi">:</span> <span class="s1">'</span><span class="s">1'</span>
        <span class="na">sidecar.istio.io/proxyMemory</span><span class="pi">:</span> <span class="s">2G</span>
</code></pre>
      </div>
    </div>
    <h2 id="when-to-use-l4-over-l7">When to use L4 over L7</h2>
    <p>Istio is capable of handling both Layer 7 (L7) and Layer 4 (L4) communications during pod-to-pod interactions, depending on the protocol specified for the destination Kubernetes service. If the <code class="language-plaintext highlighter-rouge">appProtocol</code> is set to ‘tcp’, Istio treats the connection to that service as an L4 connection; otherwise, it is classified as L7.</p>
    <p>In high-traffic scenarios where multiple hops are required between microservices before reaching the final response, each hop adds additional latency. If L7 controls are not required, disabling them can improve latency when using Istio.</p>
    <table>
      <thead>
        <tr>
          <th>Layer</th>
          <th>Use When</th>
          <th>Examples</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>L7</td>
          <td>Advanced traffic routing, observability, or security for HTTP/HTTPS traffic is needed.</td>
          <td>Canary deployments, API routing, fault injection.</td>
        </tr>
        <tr>
          <td>L4</td>
          <td>Protocol-agnostic traffic management or low-latency handling for non-HTTP protocols is needed.</td>
          <td>Database traffic, gRPC, streaming services.</td>
        </tr>
      </tbody>
    </table>
    <h3 id="l7-destination-appprotocol-as-http">L7 destination [appProtocol as ‘http’]:</h3>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">kind</span><span class="pi">:</span> <span class="s">Service</span>
<span class="na">apiVersion</span><span class="pi">:</span> <span class="s">v1</span>
<span class="na">metadata</span><span class="pi">:</span>
  <span class="na">name</span><span class="pi">:</span> <span class="s">myapp</span>
  <span class="na">namespace</span><span class="pi">:</span> <span class="s">mynamespace</span>
<span class="na">spec</span><span class="pi">:</span>
  <span class="na">ipFamilies</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="s">IPv4</span>
  <span class="na">ports</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="na">name</span><span class="pi">:</span> <span class="s">http-8080</span>
      <span class="na">protocol</span><span class="pi">:</span> <span class="s">TCP</span>
      <span class="na">appProtocol</span><span class="pi">:</span> <span class="s">http</span>
      <span class="na">port</span><span class="pi">:</span> <span class="m">8080</span>
      <span class="na">targetPort</span><span class="pi">:</span> <span class="m">8080</span>
  <span class="na">internalTrafficPolicy</span><span class="pi">:</span> <span class="s">Cluster</span>
  <span class="na">type</span><span class="pi">:</span> <span class="s">ClusterIP</span>
  <span class="na">ipFamilyPolicy</span><span class="pi">:</span> <span class="s">SingleStack</span>
  <span class="na">sessionAffinity</span><span class="pi">:</span> <span class="s">None</span>
  <span class="na">selector</span><span class="pi">:</span>
    <span class="na">app</span><span class="pi">:</span> <span class="s">myapp</span>
</code></pre>
      </div>
    </div>
    <h3 id="l4-destination--appprotocol-as-tcp">L4 destination [ appProtocol as ‘tcp’]</h3>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">kind</span><span class="pi">:</span> <span class="s">Service</span>
<span class="na">apiVersion</span><span class="pi">:</span> <span class="s">v1</span>
<span class="na">metadata</span><span class="pi">:</span>
  <span class="na">name</span><span class="pi">:</span> <span class="s">myapp</span>
  <span class="na">namespace</span><span class="pi">:</span> <span class="s">mynamespace</span>
<span class="na">spec</span><span class="pi">:</span>
  <span class="na">ipFamilies</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="s">IPv4</span>
  <span class="na">ports</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="na">name</span><span class="pi">:</span> <span class="s">tcp-8080</span>
      <span class="na">protocol</span><span class="pi">:</span> <span class="s">TCP</span>
      <span class="na">appProtocol</span><span class="pi">:</span> <span class="s">tcp</span>
      <span class="na">port</span><span class="pi">:</span> <span class="m">8080</span>
      <span class="na">targetPort</span><span class="pi">:</span> <span class="m">8080</span>
  <span class="na">internalTrafficPolicy</span><span class="pi">:</span> <span class="s">Cluster</span>
  <span class="na">type</span><span class="pi">:</span> <span class="s">ClusterIP</span>
  <span class="na">ipFamilyPolicy</span><span class="pi">:</span> <span class="s">SingleStack</span>
  <span class="na">sessionAffinity</span><span class="pi">:</span> <span class="s">None</span>
  <span class="na">selector</span><span class="pi">:</span>
    <span class="na">app</span><span class="pi">:</span> <span class="s">myapp</span>
</code></pre>
      </div>
    </div>
    <h3 id="sample-test-results">Sample Test results:</h3>
    <p>In a complex multi-hop microservices architecture (approximately 15 hops), tests show that using L4 yields about 53% better response times compared to L7 for pod-to-pod communication. This highlights the importance of choosing the right traffic management layer to optimize performance in microservices architectures.</p>
    <h3 id="with-layer7">With Layer7</h3>
    <h3 id="tps-graph">TPS graph:</h3>
    <p><img alt="img1.jpg" src="https://americanexpress.io/_post_assets/optimizing-istio-for-large-scale-enterprise-applications/img/img1.jpg" /></p>
    <h3 id="response-time-graph-p90-reaching-till-340ms-at-6ktps">Response time graph [P90 reaching till 340ms at 6kTPS]</h3>
    <p><img alt="img2.jpg" src="https://americanexpress.io/_post_assets/optimizing-istio-for-large-scale-enterprise-applications/img/img2.jpg" /></p>
    <h3 id="with-layer4">With Layer4</h3>
    <h3 id="tps-graph-1">TPS graph</h3>
    <p><img alt="img3.jpg" src="https://americanexpress.io/_post_assets/optimizing-istio-for-large-scale-enterprise-applications/img/img3.jpg" /></p>
    <h3 id="response-time-graph-p90-reaching-only-till-140ms">Response time graph [P90 reaching only till 140ms]</h3>
    <p><img alt="img4.jpg" src="https://americanexpress.io/_post_assets/optimizing-istio-for-large-scale-enterprise-applications/img/img4.jpg" /></p>
    <h2 id="istio-retry-logic">Istio Retry logic</h2>
    <p>The default retry policy for the mesh includes connect-failure, refused-stream, unavailable, cancelled, and retriable-status-codes. It’s important to be cautious about retriable-status-codes, which, when combined with the configuration for http.StatusServiceUnavailable, means that Istio will, by default, retry any 503 error—even those intentionally returned by the service. For applications that may not work with default retries should consider updating retry logic as described in the link below:</p>
    <p>https://istio.io/latest/docs/reference/config/networking/virtual-service/#HTTPRetry</p>
    <h2 id="limiting-the-configuration-sprawl-that-needs-to-be-pushed-out">Limiting the configuration sprawl that needs to be pushed out</h2>
    <p>To optimize control plane performance effectively, the most straightforward approach is to minimize the scope and size of the proxy configurations deployed to the data plane. For example, consider a specific workload ‘myapp’. Instead of pushing configurations for all services within the mesh, it’s possible to significantly improve efficiency by only deploying the proxy configuration relevant to workload ‘myapp’ and its dependent services. Utilizing the Sidecar resource allows for precise control over which configurations are sent, ensuring that only necessary data is pushed to the data plane.</p>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">apiVersion</span><span class="pi">:</span> <span class="s">networking.istio.io/v1beta1</span>
<span class="na">kind</span><span class="pi">:</span> <span class="s">Sidecar</span>
<span class="na">metadata</span><span class="pi">:</span>
  <span class="na">name</span><span class="pi">:</span> <span class="s">sidecar-myapp</span>
  <span class="na">namespace</span><span class="pi">:</span> <span class="s">600000392-colleague360</span>
<span class="na">spec</span><span class="pi">:</span>
  <span class="na">egress</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="na">hosts</span><span class="pi">:</span>
        <span class="pi">-</span> <span class="s1">'</span><span class="s">*/mysecondapp.mysecondappnamespace.svc.cluster.local'</span>
        <span class="pi">-</span> <span class="s1">'</span><span class="s">*/myexternalendpoint.com'</span>
  <span class="na">workloadSelector</span><span class="pi">:</span>
    <span class="na">labels</span><span class="pi">:</span>
      <span class="na">app</span><span class="pi">:</span> <span class="s">myapp</span>
</code></pre>
      </div>
    </div>
    <h2 id="smart-dns-proxy">Smart DNS proxy</h2>
    <p>Smart DNS Proxy is a feature in Istio that enhances DNS resolution for workloads within the service mesh. It allows Istio sidecars to intercept DNS queries and resolve them based on Istio’s service registry. There are a few things that need to be evaluated when setting up ServiceEntries for external access.</p>
    <p>https://istio.io/latest/docs/ops/configuration/traffic-management/dns/#proxy-dns-resolution</p>
    <ul>
      <li>Switch to <code class="language-plaintext highlighter-rouge">resolution: NONE</code> to avoid proxy DNS lookups entirely. This is suitable for many use cases.</li>
      <li>If the domains being resolved are controlled internally, increasing their TTL is recommended.</li>
      <li>If <code class="language-plaintext highlighter-rouge">ServiceEntry</code> is only needed by a few workloads, its scope can be limited with <code class="language-plaintext highlighter-rouge">exportTo</code> or a <a href="https://istio.io/latest/docs/reference/config/networking/sidecar/">Sidecar</a>.</li>
    </ul>
    <h2 id="reduce-stampede-of-dns-requests-to-upstream">Reduce stampede of DNS requests to upstream</h2>
    <p>Unlike most clients, which will handle DNS requests on demand before caching the results, the Istio proxy never conducts synchronous DNS requests. When a resolution: DNS type <code class="language-plaintext highlighter-rouge">ServiceEntry</code> is configured, the proxy will periodically resolve the configured hostnames and use those for all requests. This interval is fixed at 30 seconds and cannot be changed, this occurs even if the proxy never sends any requests to these applications and regardless of TTL values returned by the DNS server. This can create issues in large clusters with multiple service entries and DNS queries upstream.</p>
    <h3 id="the-problem-synchronized-30-second-dns-refreshes">The problem: synchronized 30-second DNS refreshes</h3>
    <p>Because the DNS refresh interval is fixed and identical across all proxies, large Istio meshes can experience highly synchronized DNS lookups. When hundreds or thousands of Envoy sidecars refresh DNS at the same 30-second boundary, a classic thundering herd effect ensues, leading to problems such as:</p>
    <ul>
      <li>Burst spikes in DNS queries every 30 seconds</li>
      <li>Increased load on CoreDNS / kube-dns or external DNS providers</li>
      <li>DNS latency spikes or rate limiting from upstream DNS servers</li>
      <li>Increased control-plane pressure during mass restarts or rollouts</li>
    </ul>
    <h3 id="this-behavior-becomes-especially-problematic-when">This behavior becomes especially problematic when:</h3>
    <ul>
      <li>During events like rolling restarts, deployments, or config pushes:
        <ul>
          <li>Many proxies restart and reinitialize envoy clusters simultaneously</li>
          <li>DNS resolution is triggered immediately during envoy cluster warming</li>
          <li>This stacks on top of periodic refreshes, compounding DNS pressure</li>
        </ul>
      </li>
      <li>Each Envoy sidecar independently maintains its own DNS cache and schedules periodic asynchronous resolution using a timer-driven event loop. However, since the refresh interval is deterministic and starts at roughly the same time (e.g., proxy startup or cluster warming), thousands of sidecars can align their DNS queries on the same boundary.</li>
      <li>Envoy’s DNS refresh behavior is interval-driven and does not strictly honor upstream TTLs in all cases. When TTLs are low (or effectively overridden by dns_refresh_rate), queries are issued more frequently than necessary.</li>
    </ul>
    <h3 id="fix-via-pilot_dns_jitter_duration">Fix via PILOT_DNS_JITTER_DURATION</h3>
    <p>Thankfully, a solution exists. PILOT_DNS_JITTER_DURATION is an Istio configuration that introduces randomized jitter to DNS to refresh scheduling across proxies.</p>
    <p>Instead of all Envoy sidecars refreshing DNS exactly every 30 seconds at the same moment, Istio spreads those refreshes across a configurable time window. Each proxy still refreshes DNS on the same fixed interval, but <strong>the refreshes are intentionally de-synchronized</strong>.</p>
    <p>This means:</p>
    <ul>
      <li>The 30-second DNS refresh interval remains unchanged</li>
      <li>Refresh timing is staggered across proxies</li>
      <li>DNS query traffic is evenly distributed over time</li>
    </ul>
    <h3 id="resulting-benefits">Resulting benefits</h3>
    <ul>
      <li>Eliminates DNS query bursts caused by synchronized refreshes</li>
      <li>Reduces load and rate-limit risk on DNS infrastructure</li>
      <li>Improves DNS latency stability and P99 behavior</li>
      <li>Makes large Istio meshes more resilient during restarts and scaling events</li>
    </ul>
    <h3 id="when-to-use-it">When to use it</h3>
    <p>PILOT_DNS_JITTER_DURATION is strongly recommended for:</p>
    <ul>
      <li>Large Istio deployments with many sidecars</li>
      <li>Heavy use of ServiceEntry with resolution: DNS</li>
      <li>Environments sensitive to DNS performance or quotas</li>
    </ul>
    <h2 id="logging-optimization">Logging Optimization</h2>
    <p>Istio can produce a significant volume of logs when default logging is enabled at the cluster level. This excessive logging can result in performance degradation, increased storage costs, and challenges in log analysis. To optimize logging practices, it is recommended to enable error logging by default while allowing application teams to manage logging settings for their respective applications. The steps below outline how to implement this best practice:</p>
    <ul>
      <li><em>Add <a href="https://istio.io/latest/docs/reference/config/istio.mesh.v1alpha1/#MeshConfig-ExtensionProvider">MeshConfig.ExtensionProvider.EnvoyFileAccessLogProvider</a> at cluster level to enable cluster wide logging</em></li>
    </ul>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">extensionProviders</span><span class="pi">:</span>
   <span class="pi">-</span> <span class="na">envoyFileAccessLog</span><span class="pi">:</span>
       <span class="na">path</span><span class="pi">:</span> <span class="s">/dev/stdout</span>
     <span class="na">name</span><span class="pi">:</span> <span class="s">envoy-access-logs</span>
</code></pre>
      </div>
    </div>
    <ul>
      <li><em>Now create telemetry object cluster wide to only show error logs, below filter can be updated based on usage requirements:</em></li>
    </ul>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">apiVersion</span><span class="pi">:</span> <span class="s">telemetry.istio.io/v1alpha1</span>
<span class="na">kind</span><span class="pi">:</span> <span class="s">Telemetry</span>
<span class="na">metadata</span><span class="pi">:</span>
  <span class="na">name</span><span class="pi">:</span> <span class="s">default-exception-logging</span>
  <span class="na">namespace</span><span class="pi">:</span> <span class="s">istio-system</span>
<span class="na">spec</span><span class="pi">:</span>
  <span class="na">accessLogging</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="na">providers</span><span class="pi">:</span> 
      <span class="pi">-</span> <span class="na">name</span><span class="pi">:</span> <span class="s">envoy-access-logs</span>
      <span class="na">filter</span><span class="pi">:</span>
        <span class="na">expression</span><span class="pi">:</span> <span class="s2">"</span><span class="s">response.code</span><span class="nv"> </span><span class="s">&gt;=</span><span class="nv"> </span><span class="s">400</span><span class="nv"> </span><span class="s">||</span><span class="nv"> </span><span class="s">xds.cluster_name</span><span class="nv"> </span><span class="s">==</span><span class="nv"> </span><span class="s">'BlackHoleCluster'</span><span class="nv"> </span><span class="s">||</span><span class="nv"> </span><span class="s">xds.cluster_name</span><span class="nv"> </span><span class="s">==</span><span class="nv"> </span><span class="s">'PassthroughCluster'"</span>
</code></pre>
      </div>
    </div>
    <ul>
      <li><em>For production setups, it is recommended to set up info access logging for Istio gateways as well for Istio gateways, enable that with below telemetry object:</em></li>
    </ul>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">apiVersion</span><span class="pi">:</span> <span class="s">telemetry.istio.io/v1alpha1</span>
<span class="na">kind</span><span class="pi">:</span> <span class="s">Telemetry</span>
<span class="na">metadata</span><span class="pi">:</span>
<span class="na">name</span><span class="pi">:</span> <span class="s">disable-providers-envoy-access-logs</span>
<span class="na">namespace</span><span class="pi">:</span> <span class="s">istio-gateways</span>
<span class="na">spec</span><span class="pi">:</span>
<span class="na">accessLogging</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="na">providers</span><span class="pi">:</span>
      <span class="pi">-</span> <span class="na">name</span><span class="pi">:</span> <span class="s">envoy-access-logs</span>
</code></pre>
      </div>
    </div>
    <ul>
      <li><em>Application teams will now only see the minimum required error logs as mentioned in the filter at istio-system namespace level and if needed on demand can enable logging for their workloads via below Telemetry object:</em></li>
    </ul>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">apiVersion</span><span class="pi">:</span> <span class="s">telemetry.istio.io/v1alpha1</span>
<span class="na">kind</span><span class="pi">:</span> <span class="s">Telemetry</span>
<span class="na">metadata</span><span class="pi">:</span>
    <span class="na">name</span><span class="pi">:</span> <span class="s">myapp-telemetry</span>
    <span class="na">namespace</span><span class="pi">:</span> <span class="s">mynamespace</span>
<span class="na">spec</span><span class="pi">:</span>
    <span class="na">accessLogging</span><span class="pi">:</span>
    <span class="pi">-</span> <span class="na">providers</span><span class="pi">:</span>
      <span class="pi">-</span> <span class="na">name</span><span class="pi">:</span> <span class="s">envoy-access-logs</span>
    <span class="na">selector</span><span class="pi">:</span>
      <span class="na">matchLabels</span><span class="pi">:</span>
        <span class="na">app</span><span class="pi">:</span> <span class="s">myapp</span>
</code></pre>
      </div>
    </div>
    <h2 id="metrics-optimization">Metrics Optimization</h2>
    <p>Istio offers a wide range of additional metrics that can be easily enabled or disabled, as outlined below. However, leveraging these metrics comes with trade-offs in resource consumption and system complexity. Therefore, it is advisable to enable only the necessary metrics in production environments, while maintaining the flexibility to toggle metrics on or off in development and testing environments. Here’s an overview of the potential impacts:</p>
    <h3 id="enabling-additional-metrics">Enabling additional metrics:</h3>
    <div class="language-yaml highlighter-rouge">
      <div class="highlight">
        <pre class="highlight"><code><span class="na">annotations</span><span class="pi">:</span>
          <span class="na">proxyStatsMatcher</span><span class="pi">:</span>    <span class="c1">## this part is adding additional metrics</span>
            <span class="na">inclusionRegexps</span><span class="pi">:</span>
              <span class="pi">-</span> <span class="s2">"</span><span class="s">.*upstream_rq_.*"</span>
              <span class="pi">-</span> <span class="s2">"</span><span class="s">.*upstream_cx_.*"</span>
              <span class="pi">-</span> <span class="s2">"</span><span class="s">.*downstream_rq_.*"</span>
              <span class="pi">-</span> <span class="s2">"</span><span class="s">.*downstream_cx_.*"</span>
</code></pre>
      </div>
    </div>
    <h3 id="increased-resource-usage">Increased Resource Usage:</h3>
    <ul>
      <li>Collecting and exporting additional metrics increases CPU and memory usage for the Envoy sidecar proxies.</li>
      <li>The Prometheus server may also consume more resources to scrape, store, and query the expanded dataset.</li>
    </ul>
    <h2 id="higher-network-overhead">Higher Network Overhead:</h2>
    <ul>
      <li>Exporting metrics from sidecars to telemetry systems generates additional network traffic.</li>
      <li>This can impact overall cluster performance, especially in high-traffic environments.</li>
    </ul>
    <h2 id="storage-requirements">Storage Requirements:</h2>
    <ul>
      <li>More metrics mean larger storage requirements for time-series databases like Prometheus.</li>
      <li>Long-term retention policies may need adjustment to accommodate the increased data volume.</li>
    </ul>
    <h2 id="conclusion">Conclusion</h2>
    <p>Running Istio at enterprise scale requires deliberate trade-offs rather than enabling every feature by default. As environments grow, unmanaged observability, retries, sidecars, and configuration sprawl can introduce significant performance and operational overhead.</p>
    <p>A more thoughtful approach goes a long way: focus on high-signal telemetry, right-sizing sidecars, lean on L4 over L7 when possible, and be selective about when to use retries. Keeping configuration scope controlled is equally important to ensure the control plane remains scalable and predictable.</p>
    <p>Advanced patterns like Smart DNS proxies can be useful in targeted scenarios, but they introduce additional DNS load and should be adopted cautiously with clear justification and monitoring.</p>
    <p>Ultimately, successful Istio operations depend on continuous tuning—measuring impact, refining configurations, and evolving alongside workload and traffic changes.</p>
    <p><em>*Note: Istio is an open-source technology.</em></p>
