# Pdfium Android binding with Bitmap rendering
Use pdfium library [from AOSP](https://android.googlesource.com/platform/external/pdfium/)

The demo app (for not modified lib) is [here](https://github.com/mshockwave/PdfiumAndroid-Demo-App)

Forked for use with [AndroidPdfViewer](https://github.com/barteksc/AndroidPdfViewer) project.

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
* `newDocument()` requires `ParcelFileDescriptor` instead of `FileDescriptor`
* synchronize PdfiumCore between instances, which allows to render multiple PDFs at the same time
* compile Pdfium with SONAME `libmodpdfium` - it ensures that our build of pdfium is not replaced by system version available since Lollipop

API is highly compatible with original version, only additional methods were created.

## What's new in 1.1.0?
* fixed rendering multiple PDFs at the same time thanks to synchronization between instances
* compile Pdfium with SONAME `libmodpdfium`
* `newDocument()` requires `ParcelFileDescriptor` instead of `FileDescriptor` to keep file descriptor open
* changed method of loading PDFs, which should be more stable

## Installation
Add to _build.gradle_:

`compile 'com.github.barteksc:pdfium-android:1.1.0'`

Library is available in jcenter and Maven Central repositories.

## Simple example
``` java
ImageView iv = (ImageView) findViewById(R.id.imageView);
ParcelFileDescriptor fd = ...;
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

    pdfiumCore.closeDocument(pdfDocument); // important!
} catch(IOException ex) {
    ex.printStackTrace();
}
```
## Build native part
Go to `PROJECT_PATH/src/main/jni` and run command `$ ndk-build`.
This step may be executed only once, every future `.aar` build will use generated libs.
