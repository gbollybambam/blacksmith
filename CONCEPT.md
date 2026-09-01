# Blacksmith: Image Metadata API

## The Problem
Companies running automated data pipelines across different programming languages (Python, Go, Node.js) need a reliable way to extract EXIF data. Writing, updating, and maintaining separate EXIF-parsing logic across multiple microservices creates duplicated effort and inconsistent results. 

## The Users
Automated digital asset scripts and internal backend workers that need to catalog image data uniformly across a company's architecture.

## The Core Feature
Blacksmith is a centralized backend JSON API that accepts image file uploads (strictly JPEG). It processes the incoming file stream to locate and extract the EXIF metadata block. Once the data is parsed, it terminates the stream and immediately drops the file to minimize memory overhead per request. It returns the extracted data as a JSON response, or a safe `{}` if the valid JPEG contains no EXIF data.

## What It Is NOT (Anti-Goals)
This is strictly a metadata extractor. It is NOT a permanent image hosting service like AWS S3. Files are held in temporary memory just long enough to be processed and are never permanently saved. It will not perform any visual image editing.

## Scope and Timeline
The scope is limited to a single endpoint handling constrained file uploads (JPEG only) and returning parsed data. This is small enough for one person to build end-to-end before September 15.