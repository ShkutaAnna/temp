# temp

```javascript
import { Component, AfterViewInit, ElementRef, Renderer2, HostListener } from '@angular/core';

@Component({
  selector: 'app-my-component',
  templateUrl: './my-component.component.html',
  styleUrls: ['./my-component.component.css']
})
export class MyComponent implements AfterViewInit {
  constructor(private el: ElementRef, private renderer: Renderer2) {}

  ngAfterViewInit() {
    const box = this.el.nativeElement.querySelector('.box');
    const images = this.el.nativeElement.querySelectorAll('.image');

    images.forEach((image: HTMLElement) => {
      this.trackImagePosition(box, image);
    });
  }

  trackImagePosition(box: HTMLElement, image: HTMLElement) {
    const boxRect = box.getBoundingClientRect();
    const imageRect = image.getBoundingClientRect();

    let offsetX = 0;
    let offsetY = 0;

    // Check if the image is out of bounds
    if (imageRect.left < boxRect.left) {
      offsetX = boxRect.left - imageRect.left;
    } else if (imageRect.right > boxRect.right) {
      offsetX = boxRect.right - imageRect.right;
    }

    if (imageRect.top < boxRect.top) {
      offsetY = boxRect.top - imageRect.top;
    } else if (imageRect.bottom > boxRect.bottom) {
      offsetY = boxRect.bottom - imageRect.bottom;
    }

    // Apply offset to the box
    this.renderer.setStyle(box, 'transform', `translate(${offsetX}px, ${offsetY}px)`);
  }
}
