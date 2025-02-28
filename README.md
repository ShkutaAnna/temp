# temp

```javascript
import { Component, ElementRef, ViewChild, HostListener } from '@angular/core';

@Component({
  selector: 'app-zoom-drag',
  template: `
    <div #container class="image-container">
      <img #image src="https://source.unsplash.com/random/800x600"
           class="zoom-image"
           (mousedown)="startDrag($event)"
           (mousemove)="onDrag($event)"
           (mouseup)="endDrag()"
           (mouseleave)="endDrag()"/>
    </div>
  `,
  styleUrls: ['./zoom-drag.component.scss']
})
export class ZoomDragComponent {
  @ViewChild('image') image!: ElementRef<HTMLImageElement>;
  @ViewChild('container') container!: ElementRef<HTMLDivElement>;

  scale = 1;
  minScale = 1;
  maxScale = 3;
  translateX = 0;
  translateY = 0;
  dragging = false;
  startX = 0;
  startY = 0;
  lastTranslateX = 0;
  lastTranslateY = 0;

  @HostListener('wheel', ['$event'])
  onScroll(event: WheelEvent) {
    event.preventDefault();
    
    const zoomFactor = 0.1;
    const newScale = event.deltaY < 0 ? this.scale + zoomFactor : this.scale - zoomFactor;
    const boundedScale = Math.max(this.minScale, Math.min(this.maxScale, newScale));

    if (boundedScale !== this.scale) {
      this.adjustZoom(event, boundedScale);
    }
  }

  startDrag(event: MouseEvent) {
    if (this.scale > this.minScale) {
      this.dragging = true;
      this.startX = event.clientX;
      this.startY = event.clientY;
    }
  }

  onDrag(event: MouseEvent) {
    if (!this.dragging) return;

    const deltaX = event.clientX - this.startX;
    const deltaY = event.clientY - this.startY;

    this.translateX = this.lastTranslateX + deltaX;
    this.translateY = this.lastTranslateY + deltaY;

    this.constrainImagePosition();
    this.applyTransform();
  }

  endDrag() {
    this.dragging = false;
    this.lastTranslateX = this.translateX;
    this.lastTranslateY = this.translateY;
  }

  private adjustZoom(event: WheelEvent, newScale: number) {
    const img = this.image.nativeElement;
    const rect = img.getBoundingClientRect();

    const offsetX = event.clientX - rect.left;
    const offsetY = event.clientY - rect.top;

    const percentX = offsetX / rect.width;
    const percentY = offsetY / rect.height;

    const deltaScale = newScale / this.scale;

    this.translateX = (this.translateX - (percentX - 0.5) * rect.width) * deltaScale;
    this.translateY = (this.translateY - (percentY - 0.5) * rect.height) * deltaScale;

    this.scale = newScale;
    this.constrainImagePosition();
    this.applyTransform();
  }

  private constrainImagePosition() {
    const img = this.image.nativeElement;
    const container = this.container.nativeElement
