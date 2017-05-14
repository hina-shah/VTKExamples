### Description
This example demonstrates a (rather manual) way of finding which cells of a vtkImageData a finite line intersects. Note that this is not exact - a cell-centric approach is used. That is, a discrete line (DDA-like http://en.wikipedia.org/wiki/Digital_differential_analyzer_%28graphics_algorithm%29) is followed on the cell grid between the cells that contain the intersection points of the line with the bounding box of the image. These are all not necessarily the exact same cells as the line actually intersects, but it gives a reasonable "path" of cells through the image which might be suitable for some applications.