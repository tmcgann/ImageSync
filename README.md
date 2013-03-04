ImageSync
=========

ImageSync is a library I developed to facilitate the synchronization of images based on a database timestamp. ImageSync compares the device's local image timestamps (stored in a property list) with those of the remote server and downloads any new/updated images asynchronously. After all images are downloaded, ImageSync updates the image timestamp record for future synchronization.
