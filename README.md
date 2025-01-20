# temp

```javascript
@ViewChild('container') containerRef!: ElementRef;
  @ViewChild('image') imageRef!: ElementRef;

  scale = 1;
  isDragging = false;
  startX = 0;
  startY = 0;
  translateX = 0;
  translateY = 0;

  onWheel(event: WheelEvent): void {
    event.preventDefault();
    const zoomSpeed = 0.1;

    const rect = this.containerRef.nativeElement.getBoundingClientRect();
    const offsetX = event.clientX - rect.left;
    const offsetY = event.clientY - rect.top;

    if (event.deltaY < 0) {
      this.scale += zoomSpeed;
    } else {
      this.scale = Math.max(this.scale - zoomSpeed, 1);
    }

    const image = this.imageRef.nativeElement as HTMLElement;
    image.style.transformOrigin = `${offsetX}px ${offsetY}px`;
    image.style.transform = `scale(${this.scale}) translate(${this.translateX}px, ${this.translateY}px)`;
  }

  onMouseDown(event: MouseEvent): void {
    this.isDragging = true;
    this.startX = event.clientX - this.translateX;
    this.startY = event.clientY - this.translateY;
    this.containerRef.nativeElement.style.cursor = 'grabbing';
  }

  onMouseMove(event: MouseEvent): void {
    if (!this.isDragging) return;

    const container = this.containerRef.nativeElement as HTMLElement;
    const image = this.imageRef.nativeElement as HTMLElement;

    const rect = container.getBoundingClientRect();
    const imgRect = image.getBoundingClientRect();

    // Calculate new translation values based on cursor movement
    const newTranslateX = event.clientX - this.startX;
    const newTranslateY = event.clientY - this.startY;

    // Calculate the maximum amount the image can move within the container
    const maxTranslateX = ((imgRect.width * this.scale) - rect.width) / 2;
    const maxTranslateY = ((imgRect.height * this.scale) - rect.height) / 2;

    // Clamp translations to ensure the image does not move outside the container
    this.translateX = Math.min(Math.max(newTranslateX, -maxTranslateX), maxTranslateX);
    this.translateY = Math.min(Math.max(newTranslateY, -maxTranslateY), maxTranslateY);

    // Apply the transform with the new translation values
    image.style.transform = `scale(${this.scale}) translate(${this.translateX}px, ${this.translateY}px)`;
  }

  onMouseUp(): void {
    this.isDragging = false;
    this.containerRef.nativeElement.style.cursor = 'grab';
  }

  onMouseLeave(): void {
    this.isDragging = false;
    this.containerRef.nativeElement.style.cursor = 'grab';
  }


  <div
  class="image-container"
  #container
  (wheel)="onWheel($event)"
  (mousedown)="onMouseDown($event)"
  (mousemove)="onMouseMove($event)"
  (mouseup)="onMouseUp()"
  (mouseleave)="onMouseLeave()"
>
  <img src="assets/your-image.jpg" alt="Zoomable Image" class="zoom-image" #image />
</div>


.image-container {
  width: 600px; /* Set your desired container width */
  height: 400px; /* Set your desired container height */
  overflow: hidden;
  position: relative;
  border: 2px solid #ccc;
  cursor: grab;
}

.zoom-image {
  width: 100%;
  height: auto;
  transform-origin: center center;
  transition: transform 0.3s ease;
}
