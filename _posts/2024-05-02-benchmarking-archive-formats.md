---
layout: post
title:  "Benchmarking archive formats (7z, zip, gz, bzip2, xz)"
---

# Introduction

I benchmarked a few common archive file formats today to determine their
compression space savings vis-a-vis compression time/speed. The purpose is to
determine which format gives the sweet spot between balancing storage saved and 
compression time required, and whether certain formats are more suitable for 
either spectrum (space or time).

# Results

For my tests, I used a relatively large VirtualBox virtual disk `.vdi` file of
size  4,185,915,392 bytes (~4.2 GB). The software used was 7-Zip. Apart from 
`7z` format which had its ultra and normal mode tested, I tested only the normal
mode for the other formats (i.e. zip, gzip, bzip2 and xz) with default settings.

For the avoidance of doubt, the data columns are defined as follow:

- Savings: Original file size - compressed file size
- Time: time taken to complete the compression for the same test file in seconds
- Efficiency: space savings / time
- Ratio: Compressed file size / Original file size in percentage

Here are the results:

| Method         | Compressed (bytes)   | Savings (bytes)      | Time (sec) | Efficiency   | Ratio |
|:---------------|---------------------:|---------------------:|-----------:|-------------:|------:|
| 7z Ultra       | 2,391,847,780        | 1,794,067,612        | 770        | 2,329,957.94 | 57%   |
| 7z Normal      | 2,447,914,970        | 1,738,000,422        | 525        | 3,310,476.99 | 58%   |
| Zip Normal     | 2,694,353,173        | 1,491,562,219        | 327        | 4,561,352.35 | 64%   |
| Gzip           | 2,694,353,018        | 1,491,562,374        | 325        | 4,589,422.69 | 64%   |
| Bzip2          | 2,693,655,097        | 1,492,260,295        | 396        | 3,768,334.08 | 64%   |
| xz             | 2,447,914,880        | 1,738,000,512        | 527        | 3,297,913.69 | 58%   |

# Discussion

Analyzing the efficiency data for a quick glimpse of the differences, we can 
observe that there are largely two groups, with a faster group with higher 
efficiency and a slower one with lower efficiency.

Zip (Normal) and GZip fall into the high efficiency group with good speed.

7z (Normal), Bzip2 and xz fall into the lower efficiency group with slower speed.

7z (Ultra) ranks at the bottom with the lowest speed.

# Conclusion

We can observe that in the high efficiency group, there is not much of a huge 
difference between choosing Zip (Normal) and Gzip. The difference in space 
saving is only about 250 bytes, while the speed is about the same. However, I
personally prefer the `zip` format given its universal accessibility in most
mainstream operating systems, especially in Microsoft Windows. Unix/Linux users 
may prefer the `GZip` format instead.

In the lower efficiency group, `7z` (Normal) and `xz` performs similarly, and 
are better than `Bzip2`. Thus, one should choose between `7z` (Normal) or `xz` 
when given a choice, if minimizing data storage is the focus.

While `7z` (Ultra) delivers the most compression as expected, the time taken is
not proportionate to the savings delivered. In my opinion, there is almost no
reason to choose `7z` (Ultra) or the ultra mode for other formats, since I 
believe the extreme space savings only come with a disproportionate amount of 
compression time required, unless one is forced to cram data within a certain 
hard storage limit, perhaps like those limited capacity in embedded systems.

Another question we may have is the difference in space savings between the high
and low efficiency group. As observed using this test file, the average 
difference is about *6%* or about *300 MB*. In my opinion, this relatively small 
saving which demands a noticeably large increase in time is not worthwhile. I 
would avoid the low efficiency group if I can.

# Limitations and Caveats

I have not included the `rar` format in testing as it is a commercial software
that not everyone may have access to. This may be an area for future works.