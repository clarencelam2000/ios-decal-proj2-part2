# Project: Snapchat Clone #
# Part 2: Backend #

## Description ##

Already accompished: built a feed of images as well as implemented own camera with AVFoundation. The next step is to store the images that users take and allow them to share with others. For this part of the project, using Firebase's Authentication, Database, and Storage modules: https://firebase.google.com/docs/ios/setup

## Getting Started ##

SnapchatClonePt3.xcworkspace file already includes the Firebase cocoapod. 

## Understanding the Firebase Structure ##
Hierarchy of the JSON tree, shown below:

![](/README-images/jsontree.png)

As you can see, we split the data into two larger structures - Users and Posts. The Users node has nothing to do with authentication (which is taken care of in the authentication module) but only to store additional information about the user which the Auth instance does not support. In this case, we stored a list of id's of all the posts that the user has opened, to prevent them from being able to open them again. In the image above, the user with uid "J0hq0NXe2ONoSuewOSN6CPFRwWe2" (this is their actual uid, not just a child by auto ID) has read all four of the posts in the Posts node. 

Meanwhile, every post is defined by four essential properties: date, imagePath, thread, and username. Note that the imagePath corresponds to the location in the storage module of the actual image data. 

## Authentication ##

Opening the app, you should be greeted with a new login screen. Having user accounts allows us to distinguish who took each picture and makes it much easier to handle some of the logic. 

Key files:
`LoginViewController.swift`
 `SignupViewController.swift`
Note that Firebase's createUser function doesn't allow us to set a displayName property. After creating the user, in the completion handler, we needed to create a profile change request for the returned user and commit its displayName as well. 

## Storing a User's Read Posts ##

Key file: `CurrentUser.swift` in the `Model` folder. 

The `addNewReadPost` function will be called whenever the user clicks on a particular post, and will add the ID of that post as one of the user's readPosts. 

The `getReadPostIDs` function allows us to retrieve all of the ID's of the user's read posts. For reference, this will be called when we attempt to load the tableview with all the posts so that we can identify which ones are clickable and which ones are not. 

## Storing and Retrieving Posts ##

Key file: `ImageFeed.swift`

In the `addPost` function, we saved the data for an image every time the user posts to a feed. 

The `store` function saves the actual image data to a path in the storage module (Firebase's "put" command).

Finally, the `getPosts` function retrieves all of the posts from Firebase as well as the current user's read post ID's, and then create a Post array where each post's read property is defined by checking if the id is contained in the read posts.

## Connecting it all together ##

The final step loads our feed and updates it accordingly. 

Key File: `PostsTableViewController.swift` 

In function `updateData`, we're making a `getPosts` request and storing the results the thread dictionary. We also load the images for each post in the background and store references to them in the dictionary `loadedImagesById` (we can do this in the background because the user won't expect to see the images immediately - only after clicking a cell). 

`didSelectRowAt` functions updates a user's read posts when they click on a cell). 

At this point, working clone of Snapchat! (Feed based, so like disappearing Stories in Snapchat)
