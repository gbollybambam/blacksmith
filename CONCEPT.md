# Blacksmith: Image Metadata API

## The Problem
Frontend developers building photography or portfolio apps need to read the hidden EXIF data (camera model, date taken, exposure) from photos, but running image processing in the browser is slow and resource-heavy.

## The Users
Frontend applications and digital asset workflows that need to extract information from images without permanently storing them.

## The Core Feature
Blacksmith is a backend API that accepts an image file upload, processes the file to extract its EXIF metadata, returns that data as a JSON response, and then immediately deletes the file.

## What It Is NOT (Anti-Goals)
This is strictly a metadata extractor. It is NOT an image hosting service like AWS S3. It will NOT store the images, host them for public viewing, or perform any image editing/filters.

## Scope and Timeline
The scope is limited to a single endpoint handling file uploads and returning parsed data. This is small enough for one person to build end-to-end before September 15.