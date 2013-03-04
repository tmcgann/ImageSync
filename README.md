ImageSync
=========

ImageSync is an Objective-C/iOS library I developed to facilitate the synchronization of images based on a database timestamp. ImageSync compares the device's local image timestamps (stored in a property list) with those of the remote server and downloads any new/updated images asynchronously. After all images are downloaded, ImageSync updates the image timestamp record for future synchronization.

Eventually I hope to encapsulate the dispatching of asynchronous threads in the ImageSync library, as opposed to having the user/developer write this code. However, in its current state, the code gives the developer granular control over multi-threading functionality.

Example
-------

    - (void)syncIcons {
        TMImageSync *sharedSync = [TMImageSync sharedSync];
        sharedSync.remoteURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@", SERVER_URL, IMAGE_CONTROLLER_PATH]];
        sharedSync.imagePlistName = IMAGE_PLIST_FILENAME;
        
        // Set up asynchronous dispatch group
        dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
        dispatch_group_t group = dispatch_group_create();
        
        for (CAContactCategory *cc in self.fetchedResultsController.fetchedObjects) {
            //NSLog(@"cc.icon: %@", cc.icon);
            if (![cc.icon isEqualToString:@""]) {
                // Add a task to the group
                dispatch_group_async(group, queue, ^{
                   [sharedSync syncImage:cc.icon withTimestamp:cc.modified]; // As of now, just checking to see if parent was modified
                });
            }
        }
        
        // Cannot make any more forward progress until threads finish
        // wait on the group to block the current thread.
        dispatch_group_wait(group, DISPATCH_TIME_FOREVER);
        
        // Update image timestamp record (plist)
        if (sharedSync.newImageTimestampsExist) {
            [sharedSync writeImageTimestamps];
        }
    }
