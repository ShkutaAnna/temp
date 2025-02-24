# temp

```javascript
import { Injectable } from '@angular/core';
import JSZip from 'jszip';
import { HttpClient } from '@angular/common/http';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class PhotoService {
  private iteration = 0;
  private images$ = new BehaviorSubject<string[]>([]);

  constructor(private http: HttpClient) {}

  getImages() {
    return this.images$.asObservable();
  }

  async fetchNextZip() {
    if (this.iteration === -1) return;

    try {
      const zipBlob = await this.http.get(`YOUR_API_URL?iteration=${this.iteration}`, { responseType: 'blob' }).toPromise();
      const zip = await JSZip.loadAsync(zipBlob);
      const newImages: string[] = [];

      for (const fileName of Object.keys(zip.files)) {
        const fileData = await zip.files[fileName].async('blob');
        newImages.push(URL.createObjectURL(fileData));
      }

      this.images$.next([...this.images$.value, ...newImages]);
      this.iteration++; // Fetch next iteration
      this.fetchNextZip(); // Continue fetching
    } catch (error) {
      console.error('Error fetching ZIP:', error);
    }
  }
}

import { Component, OnInit } from '@angular/core';
import { PhotoService } from './photo.service';

@Component({
  selector: 'app-photo-slider',
  templateUrl: './photo-slider.component.html',
  styleUrls: ['./photo-slider.component.scss']
})
export class PhotoSliderComponent implements OnInit {
  images: string[] = [];

  constructor(private photoService: PhotoService) {}

  ngOnInit() {
    this.photoService.getImages().subscribe(images => {
      this.images = images;
    });

    this.photoService.fetchNextZip(); // Start fetching
  }
}
