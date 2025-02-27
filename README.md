# temp

```javascript
function getBounds(container: HTMLElement, img: HTMLElement, scale: number) {
    const containerRect = container.getBoundingClientRect();
    const imgWidth = img.naturalWidth * scale;
    const imgHeight = img.naturalHeight * scale;

    const minX = Math.min(0, (containerRect.width - imgWidth) / 2);
    const maxX = Math.max(0, (imgWidth - containerRect.width) / 2);
    const minY = Math.min(0, (containerRect.height - imgHeight) / 2);
    const maxY = Math.max(0, (imgHeight - containerRect.height) / 2);

    return { minX, maxX, minY, maxY };
}

function updatePosition(newX: number, newY: number, bounds: any) {
    const clampedX = Math.max(bounds.minX, Math.min(bounds.maxX, newX));
    const clampedY = Math.max(bounds.minY, Math.min(bounds.maxY, newY));

    return { x: clampedX, y: clampedY };
}
