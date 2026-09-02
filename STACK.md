# Blacksmith: Tech Stack Rationale

## 1. Language and HTTP Framework: Node.js and Express

**The Choice:** JavaScript running on Node.js, using Express as the HTTP router.

**The Reason:** `CONCEPT.md` defines a service that processes uploaded JPEG files to extract EXIF metadata via streaming. Node.js natively handles asynchronous I/O and exposes a Stream API that processes data in fixed-size chunks (buffers). This means memory footprint is bounded by chunk size, not file size—even when scanning deeply into a file. Express provides minimal HTTP routing without framework overhead, matching the single-endpoint scope.

**Rejected Alternative (Language):** TypeScript. Rejected because `CONCEPT.md` requires shipping a working parser in one sprint, and adding a type system introduces compile overhead and a learning curve for type annotations around Buffer operations. The service has a tiny API surface (one endpoint, binary input, JSON output), so the safety and documentation benefits don't justify the added complexity within this timeline.

**Rejected Alternative (Framework):** Koa. Rejected because while Koa is minimal, it requires middleware composition for basic routing and body parsing that Express provides directly. `CONCEPT.md` defines a single-route service with no middleware chain complexity, so Express's simpler model matches the scope better.

## 2. The EXIF Parser: Custom Node.js Buffer Scanner

**The Choice:** A custom-built parser using native Node.js `Buffer` objects and stream events, rather than installing a third-party library.

**The Reason:** `CONCEPT.md` defines a service that locates and extracts EXIF segments from JPEG streams without full image processing. The definition specifies replacing manual inspection workflows by streaming to find segments. A custom buffer scanner reads only the marker segments needed (`0xFFE1` for APP1, `0xFFDA` for SOS) and stops, avoiding the decode/encode/transform machinery that general image libraries ship. This keeps the service maximally lightweight and focused solely on metadata extraction.

**Rejected Alternative:** `sharp` or `exiftool-vendored`. Rejected because they require OS-level C++ binaries or spawn separate system processes, bringing massive dependency bloat to a service that only needs to read EXIF headers. `CONCEPT.md` explicitly scopes the service to metadata extraction only—no resizing, transformation, or full image decoding—making these tools severe overengineering.

## 3. Test Tools: Mocha and Chai

**The Choice:** Mocha as the test runner and Chai for assertions.

**The Reason:** `CONCEPT.md` requires mechanical verification of stream behavior and JSON responses from binary input. Mocha is lightweight and designed for async testing, providing just test running and structure without additional tooling.

**Rejected Alternative:** Jest. Rejected because Jest bundles coverage reporting, snapshot testing, and auto-mocking—none of which `CONCEPT.md`'s scope requires. The service has one endpoint with binary-to-JSON transformation. Mocha provides just async test running and assertion capability, matching the minimal service definition without carrying unused features.

## 4. Knowledge Gap and Learning Plan

**The Gap:** `CONCEPT.md` requires parsing JPEG marker segments directly from a stream. I know how to handle standard JSON and HTTP in Node.js, but I have a gap in handling raw binary data and byte-level manipulation.

**The Plan:** I will close this gap by:
1. Studying the Node.js `Buffer` API, specifically methods like `indexOf`, `readUInt16BE`, and `slice` for marker detection and length parsing
2. Learning the JPEG specification's hex signatures (`0xFFD8` for SOI, `0xFFE1` for APP1, `0xFFDA` for SOS) so I can write the custom parser from scratch
3. Building a test harness with known-good EXIF samples before writing the parser, so I can verify correct extraction

## 5. Unsettled Choices & Assumptions

**The Assumption:** `CONCEPT.md` uses streaming to bound memory per request but does not specify a maximum incoming file size.

**The Decision:** I am setting a 20MB request body limit in Express. This cap does not affect memory (streaming already bounds that) but prevents a malicious client from holding a socket open indefinitely with a multi-gigabyte stream, which would exhaust available connections and block legitimate requests. The 20MB ceiling accommodates typical high-resolution JPEG files from modern cameras while protecting server resources.