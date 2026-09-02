# Blacksmith: Tech Stack Rationale

## 1. Language and HTTP Framework: Node.js and Express
**The Choice:** JavaScript running on Node.js, using Express as the HTTP router.
**The Reason:** `CONCEPT.md` states the service streams files to locate segments. Node.js natively handles asynchronous I/O and exposes a Stream API. Because Node processes streams in fixed-size chunks (buffers), the memory footprint is bounded by the chunk size, not the file size—even when scanning deeply into a file that lacks an APP1 segment. Express provides minimal routing without overhead.
**Rejected Alternative:** PHP with Laravel. Rejected because `CONCEPT.md` defines a minimal metadata extractor. Laravel is a batteries-included MVC framework with heavy database booting (Eloquent), which violates our small scope.

## 2. The EXIF Parser: Custom Node.js Buffer Scanner
**The Choice:** A custom-built parser using native Node.js `Buffer` objects and stream events, rather than installing a third-party library.
**The Reason:** `CONCEPT.md` was explicitly written to replace heavy image-processing libraries. By reading the stream chunks for JPEG hex markers (`0xFFE1` for APP1, `0xFFDA` for SOS) directly, we keep the service maximally lightweight.
**Rejected Alternative:** `sharp` or `exiftool-vendored`. Rejected because they require OS-level C++ binaries or spawn separate system processes, bringing massive dependency bloat to a service that only needs to read a text header.

## 3. Test Tools: Mocha and Chai
**The Choice:** Mocha as the test runner and Chai for assertions.
**The Reason:** We need to mechanically verify asynchronous streams and JSON responses. Mocha is lightweight and native to the Node ecosystem, designed specifically for async testing.
**Rejected Alternative:** Jest. Rejected because Jest is built primarily for frontend React DOM testing. It automatically mocks environments and includes a massive dependency tree, making it overkill for testing a raw binary stream parser.

## 4. Knowledge Gap and Learning Plan
**The Gap:** `CONCEPT.md` requires parsing JPEG marker segments directly from a stream. I know how to handle standard JSON and HTTP in Node.js, but I have a gap in handling raw binary data and byte-level manipulation.
**The Plan:** I will close this gap by studying the Node.js `Buffer` API (specifically methods like `indexOf` and `readUInt16BE`) and learning the exact hex signatures of the JPEG specification so I can write the custom parser from scratch.

## 5. Unsettled Choices & Assumptions
**The Assumption:** `CONCEPT.md` bounds memory through stream chunking, but does not define a maximum size for the incoming network payload.
**The Decision:** I am setting a maximum upload limit of 20MB on the Express endpoint. While stream processing protects our RAM, enforcing a body size limit protects the server's temporary disk storage space from being filled up by maliciously massive files before the stream terminates.