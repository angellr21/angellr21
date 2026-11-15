# Architecture and Technical Standards

This document outlines the core architectural principles, performance benchmarks, and development patterns implemented across systems developed by Angel Armando Lopez Ramirez (ANGELLR21).

---

## 1. Zero-SaaS & Sovereign Infrastructure

The primary architectural goal for all business solutions is to eliminate monthly recurring software-as-a-service (SaaS) overhead for clients while maintaining enterprise-grade reliability and scalability.

- Serverless Edge Compute: Utilizing Cloudflare Workers and serverless edge functions to run routing, edge redirects, and API middleware without dedicated server management.
- Database & Security Boundaries: Direct utilization of PostgreSQL with Supabase Row Level Security (RLS) to enforce authorization at the database layer rather than relying on heavy application servers.
- Custom Business Engines: Building custom parametric quote calculators, scheduling engines, and automated notification pipelines natively rather than embedding expensive third-party subscription widgets.

---

## 2. Edge AI and Privacy-First Ingestion

Where AI capabilities are required, preference is given to client-side edge compute:

- In-Browser Inference: Utilizing WebAssembly (WASM) and ONNX Runtime to execute localized models (such as biometric background extraction, OCR preprocessing, and tabular scoring) directly on user hardware.
- Privacy Preservation: Processing sensitive documents (passports, tax IDs, medical records) entirely within memory on the client machine before encrypted transmission.
- Latency Reduction: Bypassing cloud queue latency and eliminating variable API inference cost per user interaction.

---

## 3. Web Performance Standards (Core Web Vitals)

All client-facing interfaces adhere to strict sub-second performance budgets:

- Largest Contentful Paint (LCP): Must execute under 1.0s on standard 4G mobile connections.
- First Contentful Paint (FCP): Must execute under 0.8s.
- Cumulative Layout Shift (CLS): Exactly 0.000 through explicit dimension reservation on all media, fonts, and dynamic embeds.
- Interaction to Next Paint (INP): Under 150ms through non-blocking JavaScript execution and microtask deferral.
- Framework Selection: Minimal runtime overhead using Astro with progressive component hydration (React/Preact islands only where client interactivity is mandatory).

---

## 4. Code Standards and Verification

- TypeScript Strictness: All codebase implementations require TypeScript strict mode with explicit interface contracts and zero implicit any types.
- Semantic HTML and Accessibility: Adherence to WCAG 2.2 Level AA accessibility standards with complete keyboard navigation and screen reader semantics.
- Version Control Hygiene: Atomic commits, feature branches, and formal pull request merging to guarantee reproducible deployment states.


---

## 5. Telemetry, Conversion Tracking & Search Observability

All production systems incorporate high-precision observability architectures designed to capture user behavior and business metrics without compromising sub-second performance budgets.

### A. Dual-Layer Conversion Telemetry (Google Ads & GA4)
- Non-Blocking Event Dispatch: Analytics scripts are injected asynchronously with strict resource hints (dns-prefetch, preconnect) to prevent main thread blocking during critical rendering paths.
- Conversion Pipeline: Dual-layer dispatch for conversion actions (such as lead generation forms and checkout completions) triggering Google Ads conversion tags (AW-18570575171) alongside custom Google Analytics 4 events with client-side deduplication.
- Server-Side Data Ingestion: Telemetry pipelines utilize the Google Analytics Data API (`analyticsdata.googleapis.com`) to extract real-time and historical dimensional reporting across multi-property environments without client runtime penalties.

### B. Programmatic Search Indexing & Crawler Dispatch
- Zero-Delay Googlebot Notification: Rather than relying on passive sitemap crawling, deployment pipelines trigger the Google Web Search Indexing API (`indexing.googleapis.com`) via authenticated Google Cloud Service Accounts (`siteOwner` IAM scope).
- Automated Crawl Triggers: Automated dispatch sends `URL_UPDATED` payloads immediately upon static site builds or CMS publication, ensuring canonical updates and new journal case studies are crawled within minutes of release.

### C. Geospatial & Routing APIs Optimization
- Quota & Field Mask Hygiene: Google Maps Platform integrations (Geocoding, Directions, Places Autocomplete) utilize strict field masks (`X-Goog-FieldMask`) to ingest only necessary latency and location nodes, eliminating unneeded egress bandwidth and reducing API consumption by up to 70%.
- Client-Side Debouncing: Interactive location search inputs enforce 300ms trailing debouncing with session tokens to minimize redundant API calls during keystrokes.
