# Pdfium Android binding with Bitmap rendering
Use pdfium library [from AOSP](https://android.googlesource.com/platform/external/pdfium/)

The demo app (for not modified lib) is [here](https://github.com/mshockwave/PdfiumAndroid-Demo-App)

Forked specially for use with [PdfViewPager](https://github.com/barteksc/PdfViewPager) project.

## Changes in this fork:
* Added method for rendering PDF page on bitmap

    ``` java
    void renderPageBitmap(PdfDocument doc, Bitmap bitmap, int pageIndex,
                          int startX, int startY, int drawSizeX, int drawSizeY);
    ```
* Added methods to get width and height of page in points (1/72") (like in `PdfRenderer.Page` class):
    * `int getPageWidthPoint(PdfDocument doc, int index);`
    * `int getPageHeightPoint(PdfDocument doc, int index);`
* `newDocument()` throws IOException

API is fully compatible with original version, only additional methods were created.

## What's new in 1.0.3'?
* probably fixed bug when pdf should open as normal but was throwing exception
* added much more descriptive exception messages

## Installation
Add to _build.gradle_:

`compile 'com.github.barteksc:pdfium-android:1.0.3'`

Library is available in jcenter and Maven Central repositories.

## Simple example
``` java
ImageView iv = (ImageView) findViewById(R.id.imageView);
FileDescriptor fd = ...;
int pageNum = 0;
PdfiumCore pdfiumCore = new PdfiumCore(context);
try {
    PdfDocument pdfDocument = pdfiumCore.newDocument(fd);

    pdfiumCore.openPage(pdfDocument, pageNum);

    int width = pdfiumCore.getPageWidthPoint(pdfDocument, pageNum);
    int height = pdfiumCore.getPageHeightPoint(pdfDocument, pageNum);

    Bitmap bitmap = Bitmap.createBitmap(width, height,
            Bitmap.Config.ARGB_8888);
    pdfiumCore.renderPageBitmap(pdfDocument, bitmap, pageNum, 0, 0,
            width, height);

    iv.setImageBitmap(bitmap);

    pdfiumCore.closeDocument(pdfDocument);
} catch(IOException ex) {
    ex.printStackTrace();
}
```
## Build native part
Go to `PROJECT_PATH/src/main/jni` and run command `$ ndk-build`.
This step may be executed only once, every future `.aar` build will use generated libs.
