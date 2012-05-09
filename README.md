AsyncURLConnection
==================

AsyncURLConnection is a simple Objective-C class for asynchronous download and upload, using blocks for upload/download progress callbacks, donwload finished, etc.

It is based on http://stackoverflow.com/questions/5037545/nsurlconnection-and-grand-central-dispatch by '''Kazuki Sakamoto'''  (splhack)


If you don't need to specify a block (the response block for example), just pass nil as parameter.


# Examples



## Basic example

A simple example to download an image and execute code when it's done. That's what we want to do, most of the time :)

```ObjectiveC

NSString * githubLogo = @"https://a248.e.akamai.net/assets.github.com/images/modules/about_page/github_logo.png?1310086001";

// Create an AsyncURLConnection with the given URL
__block AsyncURLConnection * connection = 
                       [AsyncURLConnection connectionWithString:githubLogo
							   responseBlock:nil
		                       progressBlock:nil
                        completeBlock:^(NSData *data) {

                            NSLog(@" complete %d", [data length]);

                            // Now, we can release the connection
                            [connection release];
                        
                        } errorBlock:^(NSError *error) {
                            
                            NSLog(@" an error occured %@ ", error);

                            // Now, we can release the object                                                         
                            [connection release];
                        
                   }];

           // As the method connectionWithString:... returns an autoreleased object, we retain it before leaving the function...
           [connection retain];

           // Now, let's start the download
           [connection start];

```


## More complicated example ...

Another example on how to download an image, now with blocks for reponses and progress.

```ObjectiveC

NSString * githubLogo = @"https://a248.e.akamai.net/assets.github.com/images/modules/about_page/github_logo.png?1310086001";

// Create an AsyncURLConnection with the given URL
__block AsyncURLConnection * connection = 
[AsyncURLConnection connectionWithString:githubLogo
                       	responseBlock:^(NSURLResponse *response){
                           // The response block is called when the server sent enough data (e.g Mime Type) 
                           // to build an NSURLResponse.
                           // let's log some informations...

                            //response can be an NSHTTPURLResponse
                            if ( [response isKindOfClass:[NSHTTPURLResponse class]] ){
                               NSLog(@" Headers %@", [(NSHTTPURLResponse*)response allHeaderFields] );
                            }
                            
                            NSLog(@" Mime type : %@", [response MIMEType]);
                        
                        }
                        progressBlock:^(NSData *data, NSDate *startDate, NSUInteger totalSize){

                            NSLog(@" progress %d / %d", [data length], totalSize);

                           // In the progress block, we can for example update a UIProgressView.
                           // Remember to update UI on main thread !
                           // dispatch_async(dispatch_get_main_queue(), ^{
                           //    progressView.progress ...
                           // });


                        }
                        completeBlock:^(NSData *data) {

                            NSLog(@" complete %d", [data length]);

                            // Now, we can release the connection
                            [connection release];
                        
                        } errorBlock:^(NSError *error) {
                            
                            NSLog(@" an error occured %@ ", error);

                            // Now, we can release the object                                                         
                            [connection release];
                        
                   }];

           // As the method connectionWithString:... returns an autoreleased object, we retain it before leaving the function...
           [connection retain];

           // Now, let's start the download
           [connection start];

```
