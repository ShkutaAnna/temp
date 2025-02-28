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

    // Adjust translateX and translateY to zoom towards the cursor position
    const deltaScale = newScale / this.scale;
    this.translateX = (this.translateX - (percentX - 0.5) * rect.width) * deltaScale;
    this.translateY = (this.translateY - (percentY - 0.5) * rect.height) * deltaScale;

    this.scale = newScale;
    this.applyTransform();
  }

  private applyTransform() {
    this.image.nativeElement.style.transform = `translate(${this.translateX}px, ${this.translateY}px) scale(${this.scale})`;
  }
