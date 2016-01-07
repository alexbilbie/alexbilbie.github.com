---
title: "Programming a DS9208-1D barcode scanner"
date: "2016-01-07 18:00:00"
layout: post
---

I had 10 minutes of fun today learning how to program a barcode scanner. Specifically a "DS9208-1D" which is an omni-directional scanner that is available under numerous brands (we've got a Symbol branded version but Moterola also sell the same device).

Out of the box this scanner doesn't send a carriage return after sending the scanned data. After a seemingly unnecessary amount of Googling I finally found [a manual](https://atgsupportcentral.motorolasolutions.com/content/emb/docs/manuals/16262603b.pdf) which explains how to do this.

You'll need to print the following pages from [the manual](https://atgsupportcentral.motorolasolutions.com/content/emb/docs/manuals/16262603b.pdf):

* Page 71 "Prefix/Suffix Values"
* Page 72 "Scan Data Transmission Format"
* Pages 245 + 246 "Numeric Bar Codes"

Or just print this blog post out.

Start off with page 71 and scan the barcode `Scan Suffix 1` (data value `06h`). This will put the scanner into a mode which allows for scanning a four digit code which represents a character (or combination of characters).
Appendix E of the manual lists all of these codes and you'll find that `6058` represents "Enter".

![/images/barcode/barcode-scan-suffix-1.png](/images/barcode/barcode-scan-suffix-1.png)

Next scan each of the barcodes that represent `6`, `0`, `5` and `8`. You'll hear a confirmation beep that doesn't sound like a normal scan beep.

![/images/barcode/barcode-scan-suffix-1.png](/images/barcode/6.png)
![/images/barcode/barcode-scan-suffix-1.png](/images/barcode/0.png)
![/images/barcode/barcode-scan-suffix-1.png](/images/barcode/5.png)
![/images/barcode/barcode-scan-suffix-1.png](/images/barcode/8.png)

Finally from page 72 scan the barcode entitled `<DATA><SUFFIX 1>` (data value `01h`).

![/images/barcode/barcode-scan-suffix-1.png](/images/barcode/data-suffix-1.png)

Open a text editor and do a quick test scan of any barcode and you should see the cursor move to the next line after scanning.
