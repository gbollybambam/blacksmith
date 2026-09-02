# Blacksmith: Tech Stack Rationale

## 1. Language and HTTP Framework: JavaScript, Node.js, and Express
**The Choice:** Vanilla JavaScript running on Node.js, using Express as the HTTP router.
**The Reason:** `CONCEPT.md` states the service "streams the file" to locate segments. Node.js natively handles asynchronous I/O and exposes a Stream API, where memory is bounded by fixed-size chunks regardless of total file size. Express provides minimal routing for the "single endpoint" scope. Vanilla JS is chosen to match the strict "before September 15" timeline.
**Rejected Alternatives:** 
*   *TypeScript* was considered but rejected; the compilation overhead and type-definition setup violate the tight timeline for a single-endpoint script.
*   *NestJS* was considered as a framework alternative but rejected; its heavy dependency injection and MVC architecture far exceed the scope of a minimal metadata extractor.

## 2. The EXIF Parser: Custom Node.js Buffer Scanner
**The Choice:** A custom-built parser using native Node.js `Buffer` objects and stream events.
**The Reason:** `CONCEPT.md` explicitly states the service "parses JPEG marker segments from the start, reading only the EXIF (APP1) segment and skipping the rest." Using native Node buffers allows us to look exactly for the `0xFFE1` marker byte-by-byte without any overhead, perfectly matching the defined mechanism.
**Rejected Alternative:** `sharp` or `exiftool-vendored`. Rejected because `CONCEPT.md` specifies this is "strictly a metadata extractor." These third-party libraries bring full image manipulation suites and OS-level binaries, which violates the scope.

## 3. Test Tools: Mocha and Chai
**The Choice:** Mocha as the test runner and Chai for assertions.
**The Reason:** `CONCEPT.md` requires the service to "return the data as a JSON response (or {} if missing)." Chai's assertion syntax maps perfectly to validating these exact JSON structures, and Mocha provides a lean runner for testing async streams.
**Rejected Alternative:** Jest. While a powerful Node testing framework, Jest injects extensive global environments and auto-mocking systems that add unnecessary weight for testing a single raw binary stream endpoint.

## 4. Knowledge Gap and Learning Plan
**The Gap:** `CONCEPT.md` requires parsing JPEG marker segments directly from a stream. While I am comfortable with Express and JSON, I lack experience manipulating raw binary data on the fly.
**The Plan:** I will close this gap by studying the Node.js `Buffer` API (specifically methods like `indexOf` and `readUInt16BE`) and reviewing the hex signatures of the JPEG specification to build the custom stream parser.

## 5. Unsettled Choices & Assumptions
**The Assumption:** `CONCEPT.md` bounds memory via the ~64KB APP1 segment cap, but does not define a maximum size for the incoming network payload.
**The Decision:** I am setting a maximum upload limit of 20MB. While memory is safely bounded, a massive file missing an APP1 segment would still force the server to receive and process gigabytes of data. Terminating the request at 20MB directly bounds the total bytes received, preventing a single upload from monopolizing CPU cycles.