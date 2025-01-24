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

  onWheel(box: HTMLElement, image: HTMLElement) {
     onWheel(event: WheelEvent) {
    event.preventDefault();
    const scaleStep = 0.1;
    const maxScale = 3;
    const minScale = 1;

    this.scale += event.deltaY < 0 ? scaleStep : -scaleStep;
    this.scale = Math.min(maxScale, Math.max(minScale, this.scale));
  }
  }
}
