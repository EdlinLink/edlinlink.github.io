---
layout: post
title:  "iOS开发 - 调用相册"
date:   2015-02-10 12:00:00
tags:	tech
myTag:	iOS

---

# iOS开发 - 调用相册

-----------------------------

1\. 在**.h**中添加**Delegate**，并声明一个UIImageView变量以显示图片

	#import <UIKit/UIKit.h>

	@interface ViewController : UIViewController <UIImagePickerControllerDelegate, 
		UINavigationControllerDelegate> {
			
		__weak IBOutlet UIImageView *_imageView;

	}

	@end

2\. 在**.m**文件实现**UIImagePickerController**中的**imagePickerController:didFinishPickingMediaWithInfo:**方法


	- (void)imagePickerController:(UIImagePickerController *)picker 
		didFinishPickingMediaWithInfo:(NSDictionary *)info{    
		
		UIImage *image= [info objectForKey:@"UIImagePickerControllerOriginalImage"];
		NSData *compressImage = UIImageJPEGRepresentation(image,0.01);			    
		image = [UIImage imageWithData: compressImage];
				    
		[_imageView setImage:image];
		[self dismissViewControllerAnimated:picker completion:nil];				    
	}

3\. 实现点击按钮触发打开相册的函数

	- (IBAction)getPhotoFromLibarary:(id)sender {
		UIImagePickerController *pickerImage = [[UIImagePickerController alloc] init];
		if([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypePhotoLibrary]){
			pickerImage.sourceType = UIImagePickerControllerSourceTypePhotoLibrary;
			pickerImage.mediaTypes = [UIImagePickerController 
				availableMediaTypesForSourceType:pickerImage.sourceType]; 
		}
					
		pickerImage.delegate = self;
		[self presentViewController:pickerImage animated:YES completion:nil];
	}

--------------------------------------------------------------------------------------------

此外选择从相册选取图片是调用**UIImagePickerControllerSourceTypePhotoLibrary**，选择从摄像头选取照片是从**UIImagePickerControllerSourceTypeCamera**。


## 图片压缩

因为图片较大，所以传输的时候如果能进行压缩，那么就能便于传输。图片可以通过以下方式压缩成jpeg:

	NSData *imageData = UIImageJPEGRepresentation(image,0.01);			    
	image = [UIImage imageWithData: imageData];

其中UIImageJPEGRepresentation的第一个参数为UIImage图片的引用，第二个参数为压缩率(值越小，压缩越多，但压缩到一定程度都无法再压缩)。

除了将图片压缩成jpeg还可以把它压缩成png:

	NSData *imageData = UIImagePNGRepresentation(image);


## 参考资料

1. [NSData与UIImage之间的转换. 归为虚无. cnblogs.com](http://www.cnblogs.com/wxy325/archive/2013/03/06/2946971.html)
