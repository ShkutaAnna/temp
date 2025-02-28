# temp

```javascript
@HostListener('wheel', ['$event'])
  onScroll(event: WheelEvent) {
    event.preventDefault();

    // Determine zoom direction
    const zoomFactor = 0.1;
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

    // Calculate mouse position relative to the image
    const offsetX = event.clientX - rect.left;
    const offsetY = event.clientY - rect.top;

    // Calculate percentage of image width/height relative to mouse position
    const percentX = offsetX / rect.width;
    const percentY = offsetY / rect.height;

    // Calculate how much to scale the translation by
    const deltaScale = newScale / this.scale;

    // Adjust translation based on zoom direction
    this.translateX = (this.translateX - (percentX - 0.5) * rect.width) * deltaScale;
    this.translateY = (this.translateY - (percentY - 0.5) * rect.height) * deltaScale;

    // Set the new scale and apply transformations
    this.scale = newScale;
    this.applyTransform();
  }

  private applyTransform() {
    const img = this.image.nativeElement;
    const rect = img.getBoundingClientRect();

    // Ensure that the image does not go out of bounds (left, right, top, bottom)
    const maxX = Math.max(0, (img.width * this.scale - this.container.nativeElement.clientWidth) / 2);
    const maxY = Math.max(0, (img.height * this.scale - this.container.nativeElement.clientHeight) / 2);

    // Constrain translateX and translateY within the bounds
    this.translateX = Math.min(maxX, Math.max(-maxX, this.translateX));
    this.translateY = Math.min(maxY, Math.max(-maxY, this.translateY));

    // Apply the final transform
    this.image.nativeElement.style.transform = `translate(${this.translateX}px, ${this.translateY}px) scale(${this.scale})`;
  }

  private applyTransform() {
    this.image.nativeElement.style.transform = `translate(${this.translateX}px, ${this.translateY}px) scale(${this.scale})`;
  }
