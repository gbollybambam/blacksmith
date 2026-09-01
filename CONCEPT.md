# Blacksmith: Image Metadata API

## The Problem
Frontend applications and automated scripts need a reliable way to read EXIF data (like camera model and date taken) from images. Downloading massive image files just to parse their headers on the client side wastes bandwidth, and local script parsing requires installing and maintaining heavy dependencies.

## The Users
Frontend developers integrating photo viewers, and automated digital asset scripts that need to catalog image data.

## The Core Feature
Blacksmith is a backend JSON API that accepts image file uploads (strictly JPEG/PNG, max 5MB), extracts the EXIF metadata, returns that data as a JSON response, and then immediately discards the file.

## What It Is NOT (Anti-Goals)
This is strictly a metadata extractor. It is NOT a permanent image hosting service like AWS S3. Files are held in temporary memory or a temp directory just long enough to be processed and are never permanently saved. It will not perform any visual image editing.

## Scope and Timeline
The scope is limited to a single endpoint handling constrained file uploads (JPEG/PNG only) and returning parsed data. This is small enough for one person to build end-to-end before September 15.