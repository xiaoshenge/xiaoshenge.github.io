---
layout: post
title: ios view controller
category : skill
tags : [ios]
---

[View Controller Programming Guide for iOS](http://developer.apple.com/library/ios/#featuredarticles/ViewControllerPGforiPhoneOS/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007457)

###Instantiating another view controller inside the same storyboard###
	
	-(void)viewDidAppear:(BOOL)animated{
		 UIStoryboard *storyboard = self.storyboard;
    SpecialViewController *svc = [storyboard instantiateViewControllerWithIdentifier:@"SpecialViewController"];
 
    // Configure the new view controller here.
 
    [self presentViewController:svc animated:YES completion:nil];
	}
	
	- (IBAction)showAnotherView:(id)sender{
		UIViewController *splitView = [self.storyboard instantiateViewControllerWithIdentifier:@"SplitView"];
        XReaderAppDelegate *app = (XReaderAppDelegate *)[[UIApplication sharedApplication] delegate];
        [app.window setRootViewController:splitView];
    }
###Instantiating a view controller from a new storyboard###
 
    - (IBAction)gotoView2:(id)sender
	{
    UIViewController *testView = [[UIStoryboard storyboardWithName:@"test2" bundle:nil] instantiateInitialViewController];
    [self presentViewController:testView animated:YES completion:nil];
	}
	

	
