# temp

```javascript
import { Component, ElementRef, ViewChild, HostListener } from '@angular/core';

@Component({
  selector: 'app-zoom',
  template: `
    <div #container class="image-container">
      <img #image src="https://source.unsplash.com/random/800x600" class="zoom-image" />
    </div>
  `,
  styleUrls: ['./zoom.component.scss']
})
export class ZoomComponent {
  @ViewChild('image') image!: ElementRef<HTMLImageElement>;
  @ViewChild('container') container!: ElementRef<HTMLDivElement>;

  scale = 1;
  minScale = 1;
  maxScale = 3;
  translateX = 0;
  translateY = 0;

  @HostListener('wheel', ['$event'])
  onScroll(event: WheelEvent) {
    event.preventDefault();
    const zoomFactor = 0.1;

    // Determine new scale
    const newScale = event.deltaY < 0 ? this.scale + zoomFactor : this.scale - zoomFactor;
    const boundedScale = Math.max(this.minScale, Math.min(this.maxScale, newScale));

    // Zoom relative to cursor position
    if (boundedScale !== this.scale) {
      this.adjustZoom(event, boundedScale);
    }
  }

  private adjustZoom(event: WheelEvent, newScale: number) {
    const img = this.image.nativeElement;
    const rect = img.getBoundingClientRect();

    // Get mouse position relative to the image
    const offsetX = event.clientX - rect.left;
    const offsetY = event.clientY - rect.top;

    // Calculate percentage of image width/height
    const percentX = offsetX / rect.width;
    const percentY = offsetY / rect.height;

    // Adjust translation to keep zoom centered at cursor
    this.translateX += (percentX - 0.5) * rect.width * (1 - this.scale / newScale);
    this.translateY += (percentY - 0.5) * rect.height * (1 - this.scale / newScale);

    this.scale = newScale;
    this.applyTransform();
  }

  private applyTransform() {
    this.image.nativeElement.style.transform = `translate(${this.translateX}px, ${this.translateY}px) scale(${this.scale})`;
  }
}
