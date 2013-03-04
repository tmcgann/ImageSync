ImageSync
=========

ImageSync is a library I developed to facilitate the synchronization of images based on a database timestamp. ImageSync compares the devices local image timestamps with those of the remote server and downloads images asynchronously as needed. After all images are downloaded, ImageSync updates the image timestamp record for future synchronization.