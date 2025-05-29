Note: this article is not fact-checked by RawTherapee devs and should not be regarded as part of the official RawPedia Content and it may have (lots of) problems. Also be advised it is written by someone whose 1st is not English, therefore the words used may not be accurate in describing things/actions. Also the author is a Markdown noob so the formatting may not be optimal. This is very much a work-in-progress and you're free to modify/file an issue for comments. The contents of this article is shared under CC-BY-SA 3.0 license.

# Pixel shift

In digital photography, most image sensors feature a color filter array (CFA) in order to obtain color information, and Bayer filter is the most common one used, which features a repetitive 2x2 matrix of green & red at upper 1x2, and blue & green at lower 1x2 (more about this in Rawpedia's [Demosaicing]([Demosaicing - RawPedia](http://rawpedia.rawtherapee.com/Demosaicing)) article). This allows each pixel to have color information for one channel (red, green or blue, depends on which color of the pixel filter is covered over the "pixel"), and software can use demosaicing algorithms to make an educated guess on the values of 2 remaining channels of each pixel to display a fully colored image. However, a guess is a guess and demosaicing process can introduce artifacts, false colors, moire, etc,. To combat them, some manufactures make use of in-camera shake reduction fuction to shake the sensor for 1 pixel 4 times when capturing images, so that every 2x2 matrix can have full red, green (sampled twice) and blue information, thus negate the need of demosaicing and its drawbacks. RawTherapee's Pixel Shift "demosaicing" algorithm aim to support this kind of images and provide useful tools in tackling with the defects in them.

## Supported Images

RawTherapee's Pixel Shift function support below raw formats:

+ Pentax's pixel shift raw files (in either .DNG and .PEF format, all cameras supported as of April 2025).

+ Sony's 4-frame pixel shift raw files (in .ARQ format, use Sony's Imaging Edge to convert .ARW files to .ARQ. 16-frame pixel shift not supported since individual frames' infomation is combined and not available for RawTherapee to read).

Other pixel shift raw files from other vendors are not supported, with reason similar to why Sony's 16-frame pixel shift raw files are not supported.

## Usage

Open you pixel shift image in RawTherapee's editor, then go to Raw tab -> select Pixel Shift as Method of Demosacing. If you use Pixel Shift on a non-pixel-shifted raw, RawTherapee will fall back to use AMaZE demosaicing algorithm.

### Sub-image

A pixel shift image is consisted of 4 images, RawTherapee allows you to access each individual images (instead of being stuck at the 1st image of the sequence) as base image for further processes. In an ideal situation, the pixel shift image is shot on a perfectly stablized platform (like a tripod) at a perfectly stationary subject. However, things can go wrong and one or multiple images in the pixel shift sequence may suffer from motion blur. Being able to choose sub-images enables users to salvage an otherwise useless image by choosing the sharpest frame, rather than being locked at the blurry & unusable 1st image (which is what most post-processing softwares do).

In the cropped sample image of grass being blown by wind below, the sub-image 1 suffers a bit of motion blur, the 2nd image is perfectly sharp, the 3rd image is blurry, and the 4th is even more so. We will use this pixel shift sequence to demonstrate Motion Correction tools in later chapters.

[1st image](./ps_grass_sub1.png)

[2nd image](./ps_grass_sub2.png)

[3rd image](./ps_grass_sub3.png)

[4th image](./ps_grass_sub4.png)

### Equalize brightness of frames / Equalize per channel

When shooting in Pixel Shift mode, it might takes seconds or even minutes to take all 4 images, and during this time, the lighting condition can change (e.g. cloud covering sun), which will cause "motion" inbetween the 4 images and introduce motion artifacts. You could correct this by using Motion Correction (more details later), however, these 2 options can even out the brightness difference and provide a preliminary correction to reduce motion artifacts caused by lighting variation between 4 frames, without using a motion mask.

The "Equalize per channel" option is supposely a more advanced method to equalize the brightness of frames and further reduces artifacts that "Equalize brightness of frames" fails to eliminate. This option is not available when "Equalize brightness of frames" checkbox is not checked.

In general, if you found your pixel shift raw's frames have uneven brightness issue, check "Equalize brightness of frames" and "Equalize per channel" checkboxes, and untick "Equalize per channel" (or both) of them, to compare & see if artifacts are reduced and if it's worth to leave them on.

### Motion Correction

There are 3 options to choose for motion correction:

+ **Off**: No motion correction will be applied to the pixel shifted image. If your image does not have any motions, this option can prevent any potential image quality loss introduced by motion correction, speed up processing and maximize image quality. However, if there are motions in the frames, artifacts will visibly appear. Below is the previous grass image with Motion Correction set to Off.

        [Motion Correction Off](ps_grass_sub2_nomc.png)

+ **Automatic**: RawTherapee will auto-detect motions and apply a motion mask (with automatic calculated masking parameters) to mask out those parts with the currently selected sub-image. **Show motion mask** and **Show only motion mask** options will be available. Below is the previous grass image with Motion Correction set to Automatic.
  
  [Auto Motion Correction](ps_grass_sub2_amc.png)
  
  + **Show motion mask**: RawTherapee will display the current motion mask within image in light green color. The brighter the green, the stronger the masking is (i.e. how much of the sub-image is showing in that area instead of the original image, which may contain artifacts, or you can think it like a masking "opacity", the brighter the mask, the higher the opacity, the more aggressive the motion correction is, and the more the selected sub-image is replacing the uncorrected part in the paticular area; the darker the mask, the more conservative the motion correction is, and the more the uncorrected image is showing). This gives user an indication about which parts of the image is being masked out. If user is using **Custom** option, this option will assist user in visualizing the changes (to where and how strong the masking is) to masking parameter and options. Below is the motion mask of the previous grass image with Motion Correction set to Automatic.
  
         [Motion Mask](ps_grass_sub2_mc_mask.png)
  
  + **Show only motion mask options**: RawTherapee will show motion masked area in white, and non-motion-masked area in black. When you choose to save the image/put image into queue, this black-and white image will be exported, instead of the original image. This image is useful when an user wants to utilize RawTherapee's motion mask to mask out motions of the image in an external image editor that supports masking (GIMP, Photoshop, Affinity Photo, etc.,). Below is the "motion mask only" version of the previous grass image with Motion Correction set to Automatic.
  
         [Motion Mask only](ps_grass_sub2_mc_maskonly.png)

+ **Custom**: Offer you additional controls for motion masking parameters in addition to **Show motion mask** and **Show only motion mask** options. These options and parameters include:
  
  + Demosaicing method for motion: Choose from **AMaZE**, **AMaZE + VNG4**, **RCD + VNG4**, **LMMSE** to demosaic areas covered by motion mask. To understand the pros and cons of each option, refer to [Demosaicing Methods]([Demosaicing - RawPedia]([Demosaicing - RawPedia](http://rawpedia.rawtherapee.com/Demosaicing#Demosaicing_Methods))) and Dual Demosaic section of the page. Generally, AMaZE are more detailed but more prone to noise, RCD is AMaZE toned down a bit in both aspects and demosaics faster. LMMSE is good as a start for noisy images but more prone to false color. VNG4 can smooth everything out, so dual demosaicing algorithms like AMaZE + VNG4/RCD + VNG4 theoretically allows you to achieve better details while suppressing noises and artifacts in non-detailed areas.
  
  + Check green channel for motion: As mentioned above, in the 2x2 bayer matrix, there are 2 green pixels, 1 red pixel and 1 blue pixel, which means green channel has more information compared to the other two. Not only that, but all green pixels are sampled twice, makes it easier to figure out if a pixel has movement or not. Therefore , compared to "Check red/blue channel for motion" option, this option is better at detecting motions.
    
    Below is the motion mask of the previous grass image with "Check green channel for motion" checked and "Check red/blue channel for motion" unchecked.
    
    [Motion Mask](ps_grass_sub2_md_g.png)
  
  + Check red/blue channel for motion: This option is not nearly as sensitive as "Check green channel for motion". Generally speaking, it can be supplimental to "Check green channel for motion" option if you found it not good enough, but using it alone won't do much for motion detection.
    
    Below is the motion mask of the previous grass image with "Check red/blue channel for motion" checked and "Check green channel for motion" unchecked.
    
    [Motion Mask](ps_grass_sub2_md_rb.png)
    
    Here is it with both options checked:
    
    [Motion Mask](ps_grass_sub2_md_rgb.png)
  
  + Fill holes in mask: Sometimes RawTherapee cannot detect large motion area as a whole, only its outline (like a walking pedestrian, the center part is not as detectable since there might not be enough change in clothes' color, but the ouside sillouette changes quite a bit and detectable.) This option is able to help in such cases, covering the center of the motion area that are outlined.
    
    [Motion Mask](ps_grass_sub2_md_fillhole.png)
  
  + Blur motion mask: If enabled, "Blur radius" and "Smooth transitions" sliders will be available for adjustments. When disabled, the two sliders will also be disabled, and no motion mask blur will be applied.
  
  + Blur radius: The higher the value, the more expansive each motion mask patch is. Combined with "Smooth transition" value, a higher Blur radius could smoothing the transition area of motion and non-motions, as the cost of introducing false positives.
    
    Below is the motion mask of the previous grass image with 0.5 and 25 "Blur radius" for comparison. These are extreme values being used only to emphasize its effect.
    
    [Motion Mask 0.5 radius](ps_grass_sub2_0.5rad.png)
    
    [Motion Mask 25 radius](ps_grass_sub2_25rad.png)
  
  + Smooth transition: RawTherapee shows your current sub-image in moving parts, and due to motions, the interline between motion area and still area may have a significant difference, causing a rough transition. Increase this value can masking more of the interline area at a relatively low masking strength, so your sub-image and pixel shift image with artifact can blend together in motion area, which will help in making the transition more natural, but at the cost of sacrificing some technically perfectly good areas.
    
    Below is the motion mask of the previous grass image with 0 and 0.7 "Smooth transition" for comparison. Extreme values are used and "Fill holes in mask" is disabled to emphasize its effect.
    
    [Motion Mask 0 smooth transition](ps_grass_sub2_no_smoothing.png)
    
    [Motion Mask 0.7 smooth transition](ps_grass_sub2_0.7_smoothing.png)
  
  + Sensitivity: Control how aggressive you want the motion detection to be. The higher the value, the more aggressive, and the more false positive can happen.
    
    Below is the motion mask of the previous grass image with 0 and 2 Sensitivity for comparison.
    
    [Motion Mask 0 Sensitivity](ps_grass_sub2_0_sens.png)
    
    [Motion Mask 2 Sensitivity](ps_grass_sub2_2_sens.png)
  
  + Use median for moving parts: If enabled, instead of using the sub-image of your choice, RawTherapee will show the middle value of the pixels of 4 images in the motion area. Can be effective at removing moving parts, may result in loss of detail (or smoothing things out) in the motion area. Can't be enabled with "Use average for moving parts" also enabled.
  
  + Use average for moving parts: If enabled, instead of using the sub-image of your choice, RawTherapee will show the average value of the pixels of 4 images in the motion area. Can deliver a long-exposure effect in motion area, but loss of detail and false color at highlight (if highlight's motion is significant), too.

## Limitation and recommendation of shooting/practices of processing pixel shift images

+ Shooting pixel shift images requires the scene and camera to be perfectly stationary. A moving subject, even at pixel level, will produce artifact and nullnify the benefit of pixel shift (so for example, taking pixel shift pictures of moon with a telephoto lens doesn't work since moon is actively moving in frame). Also, hand holding your camera to take pixel shift images is unrealistic since no one's hand can be stable pixel perfectly for a few 0.x seconds (mostly depends on the electronic shutter speed and multiply it by 4, since pixel shift mode takes 4 images with electronic shutter).

+ Another type of "camera" movement is camera noise, since it can also result in pixel-to-pixel variation, therefore it is best to use the lowest possible ISO when shooting in pixel shift mode. This should not be much of a problem since you camera will be on a stable platform anyways.

+ As mentioned before, pixel shift images are taken in electronic shutter mode, so any moving part will suffer from jello effect, if your sensor's read-out speed is slow.

+ RawTherapee's motion correction takes one single frame to cover moving part, which means areas with motion mask do not enjoy the benefit of pixel shift. Therefore, it is better not to set aggressive values for the three motion correction sliders if the image mostly looks fine, instead, increase "False color suppression" to reduce/eliminate artifacts and minimize image quality degregation. Below is the grass image but a different part with small block-shaped residual artifacts.

        [Artifacts](ps_grass_sub2_amc_nofcs.png)

        This is what it looks like when bumping False Color Suppression to 1.

        [Less Artifacts](ps_grass_sub2_amc_1fcs.png)

+ Toggle "Show motion mask" option on to assess the coverage of motion mask, then toggle it off to assess how well is it doing at eliminating artifact and find remaining artifacts, then use different mask options/sliders based to the artifacts' position and size.

+ "Use average for moving parts" option is useful for images contain waterfall/cloudy/waves since it can create a long exposure effect in those parts of the image.
