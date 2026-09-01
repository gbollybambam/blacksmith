# Blacksmith: Image Metadata API

## The Problem
Automated scripts and backend data pipelines need a reliable way to extract EXIF data from images. Parsing files locally requires installing and maintaining heavy image-processing libraries on every single worker machine, which bloats the environment and slows down automated workflows.

## The Users
Automated digital asset scripts and internal backend workers that need to catalog image data.

## The Core Feature
Blacksmith is a backend JSON API that accepts image file uploads (strictly JPEG, max 5MB) and extracts the EXIF metadata. It returns that data as a JSON response. If a valid JPEG contains no EXIF data, the API safely returns an empty JSON object `{}` with a 200 OK status. After processing, it immediately discards the file.

## What It Is NOT (Anti-Goals)
This is strictly a metadata extractor. It is NOT a permanent image hosting service like AWS S3. Files are held in temporary memory or a temp directory just long enough to be processed and are never permanently saved. It will not perform any visual image editing.

## Scope and Timeline
The scope is limited to a single endpoint handling constrained file uploads (JPEG only) and returning parsed data. This is small enough for one person to build end-to-end before September 15.