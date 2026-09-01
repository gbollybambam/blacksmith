# Blacksmith: Image Metadata API

## The Problem
Companies running automated data pipelines across different programming languages (Python, Go, Node.js) need a reliable way to extract EXIF data. Writing, updating, and maintaining separate EXIF-parsing logic across multiple microservices creates duplicated effort and inconsistent results. 

## The Users
Automated digital asset scripts and internal backend workers that need to catalog image data uniformly across a company's architecture.

## The Core Feature
Blacksmith is a centralized backend JSON API that accepts image file uploads (strictly JPEG). To maximize efficiency, it uses stream processing to read only the EXIF header block (the first 64KB of the file) and ignores the rest of the image data, meaning the total file size does not matter and memory is never exhausted. It returns the data as a JSON response (or `{}` if missing) and immediately drops the file.

## What It Is NOT (Anti-Goals)
This is strictly a metadata extractor. It is NOT a permanent image hosting service like AWS S3. Files are held in temporary memory just long enough to be processed and are never permanently saved. It will not perform any visual image editing.

## Scope and Timeline
The scope is limited to a single endpoint handling constrained file uploads (JPEG only) and returning parsed data. This is small enough for one person to build end-to-end before September 15.