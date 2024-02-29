---
layout: post
title:  "Fitting Windows 10 22H2 ISO image on 4.7 GB DVD-R with NTLite"
---

The last official release of Windows 10 is 22H2, and I wanted to make an 
archived copy of the installation media for Windows 10 installation on my 
devices.

# Using optical disc as installation media

In 2024, why am I still using DVD-R optical discs for software installation? 
This must be unthinkable for most modern techies who live on the cutting edge.

The simple truth is that I do not have many spare USB flash drives lying around 
at home, so I am conservative about their use unless it's necessary. Also, I 
have some old DVD-Rs and CD-Rs lying around unused, so why not make use of them 
instead?

In addition, DVD-Rs and CD-Rs are write-once read-many (WORM) media. I can be
comfortably confident that once written, the installation media files cannot be 
subtly modified by malware or corrupted by a bad disk drive in the future.

# Problem: Windows 10 22H2 cannot fit onto a normal 4.7 GB DVD-R

Windows 10 22H2 has [grown too big to fit onto standard sized 4.7 GB DVD-R][build-1803]. 

A couple of years ago with earlier versions of Windows 10 however, they came in 
at under 4.7 GB. So their ISO image could fit onto any DVD-R just fine without 
additional hacking.

Netizens have proposed some solutions and workarounds. These include:

## Solution 1: Use an older Windows 10 ISO image (build 1803) that fits under 4.7 GB

Some suggested [using the Windows 10 build 1803 ISO][build-1803] from 2018 as a 
workaround, but it's not ideal. Why install an older version, and then have to 
waste more time on [Windows Update][windows-update] downloading and applying 
software updates and patches released between 2018 until 2022? That may span 
several gigabytes worth of updates requiring 30 minutes to an hour to run?

It is better to install from an up-to-date installation media and be done with
the latest updates in one go.

## Solution 2: Use a 8.5 GB DVD-R DL disc for Windows 10 22H2

Some suggested [using the less commonly-available DVD-R DL (dual layer) with 8.5 GB capacity][dvd-r-dl] 
as a solution. It is a simple and straightforward option if you have some 
DVD-R DL readily available. However, such DVD-R DL are less commonly found in 
offices and homes.

Thus, I am not going to spend more money and time hunting around for them.

## Solution 3: Create and use a modified, stripped down Windows 10 22H2 ISO image

This idea came from Redditor *heartprairie* who reported customizing the
installation media ISO image created by Windows 10 Media Creation Tool 
successfully using [NTLite][ntlite]. However, the [one paragraph reply][heartprairie] 
does not go into detail how he/she went about doing it.

![Windows 10 22H2 ISO size comparison](/blog/assets/images/2024-02-29-w10-22h2-dvd-r-ntlite.png)

I had no prior experience with [NTLite][ntlite] but decided to give it a try.

Here's how I did it based on the brief description.

1. Download and install [NTLite][ntlite]. Choose the **Free license** for 
   individual / home / private use. It is already sufficient.
   
2. Extract the Windows 10 22H2 ISO image file created by Windows 10 Media 
   Creation Tool into a folder e.g. `C:\temp\W10_22H2` using your preferred 
   archiver or CD/DVD optical disc mastering software (e.g. [7-Zip][7-zip], 
   [WinRAR][winrar], [Nero Burning ROM][nero] or [CDBurnerXP][cdburnerxp])
   
3. Start [NTLite][ntlite]

4. **Add Image (ISO, WIM, ESD, SWM)** > Select `\sources\install.esd` in the 
   folder containing extracted files from ISO image
   
   ![NTLite Add Image](/blog/assets/images/2024-02-29-w10-22h2-ntlite-add.png)
   
   ![NTLite Browse install.esd](/blog/assets/images/2024-02-29-w10-22h2-ntlite-browse-install-esd.png)
   
5. Remove/delete unwanted editions. Repeat for each edition you wish to delete.
   I chose to keep only the common **Home** and **Pro** edition. The N variant 
   editions are not applicable to anyone except perhaps for users in the 
   European Union and South Korea whereby their anti-competition laws mandate 
   the exclusion of certain bundled Microsoft software like Windows Media 
   Player. I also do not foresee needing the **Education** edition and its 
   variants.
   
   ![NTLite Delete](/blog/assets/images/2024-02-29-w10-22h2-ntlite-delete.png)
   
   ![NTLite Deleting](/blog/assets/images/2024-02-29-w10-22h2-ntlite-install-esd-deleting.png)
   
6. *(Optional)* Right-Click **Operating systems | install.esd**, and 
   **Recompress** the `install.esd` file in [NTLite][ntlite]. The `install.esd` 
   may shrink to a more optimal size. However, I have not noticed any notable
   decrease in size from recompressing `install.esd`. YMMV.
   
   ![NTLite Recompress](/blog/assets/images/2024-02-29-w10-22h2-ntlite-recompress.png)
   
   ![NTLite Recompressing](/blog/assets/images/2024-02-29-w10-22h2-ntlite-recompress-esd.png)
   
7. Right-click the `C:\temp\W10_22H2` folder > **Create ISO** (e.g. 
   `W10_22H2_F.iso`) with [NTLite][ntlite]
   
   ![NTLite Create ISO](/blog/assets/images/2024-02-29-w10-22h2-ntlite-iso-create.png)
   
   ![NTLite Creating ISO image](/blog/assets/images/2024-02-29-w10-22h2-ntlite-iso-creating.png)
   
8. Check that the new ISO images `W10_22H2_F.iso` is about 4.46 GB now -- small 
   enough to fit onto a normal 4.7 GB DVD-R -- compared to the original 4.77 GB 
   `W10_22H2.iso` ISO image created by Windows 10 Media Creation Tool.
   
   ![Windows 10 22H2 ISO size comparison](/blog/assets/images/2024-02-29-w10-22h2-iso-size.png)
   
9. Test the modified installation media ISO image. During the setup, notice 
   that the list of operating system options available to choose from 
   correspond with the entries in the `install.esd` in [NTLite][ntlite]
   
   ![Windows 10 22H2 Setup Editions](/blog/assets/images/2024-02-29-w10-22h2-setup-editions.png)
   
10. Verify that **Windows 10 22H2** is installed from within the virtual machine

    ![Windows 10 22H2 Home](/blog/assets/images/2024-02-29-w10-22h2-home.png)
	
    ![Windows 10 22H2 Pro](/blog/assets/images/2024-02-29-w10-22h2-pro.png)
   
Thus I can conclude that my remastered ISO image is able to boot and complete 
Windows 10 22H2 setup successfully.

## Solution 4: Reuse ISO image on [Internet Archive][archive-org] made by others

It has come to my attention that there are some ISO images of Windows 10 22H2, 
21H2, 20H2 etc. being offered on the [Internet Archive][archive-org]. These may 
or may not have undergone similar modifications to shrink them to fit onto a 
DVD-R. The problem is we may never know what has been done to the ISO image.

Some users have reported success in using them for installation in their 
reviews or comments.

While these uploads have undergone some antivirus scanning during their 
uploading process, we can never be sure if the ISO image and the files within 
have been modified, replaced or tampered with. We simply do not know if they 
would even work in the first place, or if they have been tested by the 
uploaders who created them.

This solution is *NOT RECOMMENDED* by me. So you have been forewarned! If you
decide to take the risk and try them, you shall bear the responsibility.

[build-1803]: https://www.reddit.com/r/Windows10/comments/ydyks8/if_you_need_to_install_windows_10_via_dvd/
[windows-update]: https://support.microsoft.com/en-us/windows/windows-update-faq-8a903416-6f45-0718-f5c7-375e92dddeb2
[dvd-r-dl]: https://www.reddit.com/r/windows/comments/jh8obu/bug_windows_10_media_creation_tools_iso_files_for/
[heartprairie]: https://www.reddit.com/r/Windows10/comments/ydyks8/comment/iu17jri/
[ntlite]: https://www.ntlite.com/
[7-zip]: https://www.7-zip.org/
[winrar]: https://www.win-rar.com/open-iso-file.html
[nero]: https://www.nero.com/eng/products/nero-burning-rom/
[cdburnerxp]: https://cdburnerxp.se/en/home
[archive-org]: https://archive.org/



