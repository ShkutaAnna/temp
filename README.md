# temp

```javascript
import { Component, ElementRef, HostListener, ViewChild } from '@angular/core';

@Component({
  selector: 'app-image-zoom-pan',
  templateUrl: './image-zoom-pan.component.html',
  styleUrls: ['./image-zoom-pan.component.scss']
})
export class ImageZoomPanComponent {
  @ViewChild('image', { static: true }) image!: ElementRef<HTMLImageElement>;
  @ViewChild('container', { static: true }) container!: ElementRef<HTMLDivElement>;

  scale = 1;
  translateX = 0;
  translateY = 0;
  minScale = 1;
  maxScale = 5;
  startX = 0;
  startY = 0;
  isDragging = false;

  ngAfterViewInit() {
    this.resetImagePosition();
  }

  resetImagePosition() {
    this.translateX = 0;
    this.translateY = 0;
  }

  @HostListener('wheel', ['$event'])
  onWheel(event: WheelEvent) {
    event.preventDefault();
    
    const zoomFactor = 0.1;
    const newScale = this.scale + (event.deltaY < 0 ? zoomFactor : -zoomFactor);
    if (newScale < this.minScale || newScale > this.maxScale) return;

    const rect = this.container.nativeElement.getBoundingClientRect();
    const imgRect = this.image.nativeElement.getBoundingClientRect();

    // Cursor position relative to the container
    const mouseX = event.clientX - rect.left;
    const mouseY = event.clientY - rect.top;

    // Calculate cursor position relative to the image
    const imgX = (mouseX - this.translateX) / this.scale;
    const imgY = (mouseY - this.translateY) / this.scale;

    // Adjust translation to keep zoom centered on cursor position
    this.translateX -= imgX * (newScale - this.scale);
    this.translateY -= imgY * (newScale - this.scale);

    this.scale = newScale;
    this.applyBoundaries();
  }

  @HostListener('mousedown', ['$event'])
  onMouseDown(event: MouseEvent) {
    this.isDragging = true;
    this.startX = event.clientX - this.translateX;
    this.startY = event.clientY - this.translateY;
  }

  @HostListener('mousemove', ['$event'])
  onMouseMove(event: MouseEvent) {
    if (!this.isDragging) return;

    this.translateX = event.clientX - this.startX;
    this.translateY = event.clientY - this.startY;

    this.applyBoundaries();
  }

  @HostListener('mouseup')
  @HostListener('mouseleave')
  onMouseUp() {
    this.isDragging = false;
  }

  applyBoundaries() {
    const rect = this.container.nativeElement.getBoundingClientRect();
    const imgRect = this.image.nativeElement.getBoundingClientRect();

    const scaledWidth = imgRect.width * this.scale;
    const scaledHeight = imgRect.height * this.scale;

    // Calculate min/max bounds
    const minX = Math.min(0, rect.width - scaledWidth);
    const maxX = Math.max(0, scaledWidth - rect.width);
    const minY = Math.min(0, rect.height - scaledHeight);
    const maxY = Math.max(0, scaledHeight - rect.height);

    this.translateX = Math.max(minX, Math.min(this.translateX, maxX));
    this.translateY = Math.max(minY, Math.min(this.translateY, maxY));
  }
}
