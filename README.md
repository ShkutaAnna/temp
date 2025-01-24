# temp

```javascript
import { Component, ElementRef, ViewChild, HostListener } from '@angular/core';

@Component({
  selector: 'app-image-zoom',
  templateUrl: './image-zoom.component.html',
  styleUrls: ['./image-zoom.component.scss'],
})
export class ImageZoomComponent {
  @ViewChild('container', { static: true }) container!: ElementRef;
  @ViewChild('image', { static: true }) image!: ElementRef;

  scale: number = 1; // Current zoom scale
  translateX: number = 0; // X position of the image
  translateY: number = 0; // Y position of the image
  isDragging: boolean = false; // Drag state
  lastMouseX: number = 0;
  lastMouseY: number = 0;

  onWheel(event: WheelEvent) {
    event.preventDefault();
    const scaleStep = 0.1;
    const maxScale = 3;
    const minScale = 1;

    this.scale += event.deltaY < 0 ? scaleStep : -scaleStep;
    this.scale = Math.min(maxScale, Math.max(minScale, this.scale));
  }

  onMouseDown(event: MouseEvent) {
    event.preventDefault();
    this.isDragging = true;
    this.lastMouseX = event.clientX;
    this.lastMouseY = event.clientY;
  }

  onMouseUp() {
    this.isDragging = false;
  }

  onMouseMove(event: MouseEvent) {
    if (!this.isDragging) return;

    const deltaX = event.clientX - this.lastMouseX;
    const deltaY = event.clientY - this.lastMouseY;

    const containerRect = this.container.nativeElement.getBoundingClientRect();
    const imageRect = this.image.nativeElement.getBoundingClientRect();

    // Calculate new positions
    const newTranslateX = this.translateX + deltaX;
    const newTranslateY = this.translateY + deltaY;

    // Ensure the image stays within the container boundaries
    const minX = Math.min(0, containerRect.width - imageRect.width);
    const minY = Math.min(0, containerRect.height - imageRect.height);
    const maxX = 0;
    const maxY = 0;

    this.translateX = Math.max(minX, Math.min(maxX, newTranslateX));
    this.translateY = Math.max(minY, Math.min(maxY, newTranslateY));

    this.lastMouseX = event.clientX;
    this.lastMouseY = event.clientY;
  }

  getImageStyle() {
    return {
      transform: `translate(${this.translateX}px, ${this.translateY}px) scale(${this.scale})`,
    };
  }
}


<div class="image-container" #container>
  <img
    src="your-image.jpg"
    alt="Zoomable Image"
    [ngStyle]="getImageStyle()"
    (mousedown)="onMouseDown($event)"
    (mouseup)="onMouseUp()"
    (mousemove)="onMouseMove($event)"
    (wheel)="onWheel($event)"
    #image
  />
</div>
