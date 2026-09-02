# Blacksmith: Tech Stack Rationale

## 1. Language and Run Tools: Node.js and Express
**The Choice:** JavaScript running on Node.js, using Express as the routing framework.
**The Reason:** `CONCEPT.md` states the core feature "streams the file... reading only the EXIF (APP1) segment and skipping the rest." Node.js is natively designed for asynchronous I/O and stream processing. Its native `fs` and stream APIs allow us to read incoming bytes on the fly and terminate the connection the moment the APP1 segment is parsed, directly satisfying the requirement to keep memory bounded. Express provides the minimal HTTP routing needed for this single-endpoint scope.

## 2. Rejected Alternative: PHP and Laravel
**The Choice:** PHP with the Laravel framework was considered and rejected.
**The Reason:** `CONCEPT.md` explicitly states this service is "strictly a metadata extractor" and "NOT a permanent image hosting service." Laravel is a robust, batteries-included framework built around database interactions (Eloquent) and MVC architecture. Bootstrapping a massive framework for a service with zero database and no frontend violates the small scope defined in our concept.

## 3. Test Tools: Mocha and Chai
**The Choice:** Mocha as the test runner and Chai for assertions.
**The Reason:** We need to mechanically verify that our parser correctly extracts EXIF data and safely returns `{}` for missing data. Mocha and Chai are lightweight, native to the Node ecosystem, and excel at testing asynchronous data streams and JSON API responses without unnecessary overhead.

## 4. Knowledge Gap and Learning Plan
**The Gap:** While I have experience building backend endpoints with Node.js and Express, `CONCEPT.md` states this service must run "uniformly across a company's architecture." 
**The Plan:** My plan is to bridge this gap by mastering Docker to containerize the Node.js application, ensuring it can be deployed anywhere as an isolated microservice. I will also deepen my hands-on experience with Mocha/Chai to write strict unit tests that prove the memory bound works as designed.

## 5. Unsettled Choices & Assumptions
**The Assumption:** `CONCEPT.md` states that memory is bounded because the APP1 segment cannot exceed 64KB. However, it does not define a maximum upload size for the network payload.
**The Decision:** I am assuming a maximum network upload limit of 20MB. While the server's memory won't crash from a larger file due to the stream terminating, enforcing a 20MB limit at the Express router level prevents network bandwidth exhaustion.