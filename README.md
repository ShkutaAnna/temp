# temp

```javascript
dataSource = new MatTableDataSource<any>();  // The combined data source

  constructor(private store: Store) {}

  ngOnInit(): void {
    // Combine both data sources
    combineLatest([this.store.select(selectItems1), this.store.select(selectItems2)]).subscribe(([data1, data2]) => {
      // Combine the two arrays
      this.dataSource.data = [...data1, ...data2];
    });
  }

import { Component, OnInit } from '@angular/core';
import { BehaviorSubject, combineLatest } from 'rxjs';
import { MatTableDataSource } from '@angular/material/table';

@Component({
  selector: 'app-item-table',
  templateUrl: './item-table.component.html',
  styleUrls: ['./item-table.component.css']
})
export class ItemTableComponent implements OnInit {
  displayedColumns: string[] = ['id', 'name', 'description'];
  
  // Define two separate data sources
  private dataSource1$ = new BehaviorSubject<any[]>([
    { id: 1, name: 'Item 1', description: 'Description 1' },
    { id: 2, name: 'Item 2', description: 'Description 2' },
  ]);
  
  private dataSource2$ = new BehaviorSubject<any[]>([
    { id: 3, name: 'Item 3', description: 'Description 3' },
    { id: 4, name: 'Item 4', description: 'Description 4' },
  ]);

  // Combined data source for the mat-table
  dataSource = new MatTableDataSource<any>();

  constructor() {}

  ngOnInit(): void {
    // Combine the two data sources using combineLatest
    combineLatest([this.dataSource1$, this.dataSource2$]).subscribe(([data1, data2]) => {
      // Merge both arrays into a single array
      const combinedData = [...data1, ...data2];
      // Update the MatTableDataSource with the combined data
      this.dataSource.data = combinedData;
    });
  }

  // Example method to update dataSource1
  updateDataSource1(): void {
    const updatedData1 = [
      ...this.dataSource1$.value,
      { id: 5, name: 'Item 5', description: 'Description 5' },
    ];
    this.dataSource1$.next(updatedData1); // Update dataSource1
  }

  // Example method to update dataSource2
  updateDataSource2(): void {
    const updatedData2 = [
      ...this.dataSource2$.value,
      { id: 6, name: 'Item 6', description: 'Description 6' },
    ];
    this.dataSource2$.next(updatedData2); // Update dataSource2
  }
}

import { createAction, props } from '@ngrx/store';

export const addItem = createAction(
  '[Item] Add Item',
  props<{ item: any }>()
);

export const addItemSuccess = createAction(
  '[Item] Add Item Success',
  props<{ item: any }>()
);

export const addItemFailure = createAction(
  '[Item] Add Item Failure',
  props<{ error: any }>()
);

export const loadItems = createAction('[Item] Load Items');

export const loadItemsSuccess = createAction(
  '[Item] Load Items Success',
  props<{ items: any[] }>()
);

export const loadItemsFailure = createAction(
  '[Item] Load Items Failure',
  props<{ error: any }>()
);


import { Injectable } from '@angular/core';
import { Actions, ofType } from '@ngrx/effects';
import { Observable } from 'rxjs';
import { catchError, map, switchMap } from 'rxjs/operators';
import { Store } from '@ngrx/store';

import { ItemService } from 'path-to-your-item-service'; // Import your API service
import { addItem, addItemSuccess, addItemFailure, loadItems, loadItemsSuccess, loadItemsFailure } from './item.actions';

@Injectable()
export class ItemEffects {

  constructor(
    private actions$: Actions,
    private itemService: ItemService,
    private store: Store
  ) {}

  // Effect to handle add item
  addItem$ = createEffect(() =>
    this.actions$.pipe(
      ofType(addItem),
      switchMap(action =>
        this.itemService.addItem(action.item).pipe(
          map(item => {
            // Dispatch success and load items after successful add
            this.store.dispatch(loadItems()); // Dispatch loadItems action to reload the data
            return addItemSuccess({ item });
          }),
          catchError(error => [addItemFailure({ error })])
        )
      )
    )
  );

  // Effect to load items after success
  loadItems$ = createEffect(() =>
    this.actions$.pipe(
      ofType(loadItems),
      switchMap(() =>
        this.itemService.getItems().pipe(
          map(items => loadItemsSuccess({ items })),
          catchError(error => [loadItemsFailure({ error })])
        )
      )
    )
  );
}

import { createReducer, on } from '@ngrx/store';
import { addItemSuccess, addItemFailure, loadItemsSuccess, loadItemsFailure } from './item.actions';

export interface ItemState {
  items: any[];
  loading: boolean;
  error: string | null;
}

export const initialState: ItemState = {
  items: [],
  loading: false,
  error: null
};

export const itemReducer = createReducer(
  initialState,
  on(addItemSuccess, (state, { item }) => ({
    ...state,
    items: [...state.items, item],
    loading: false,
    error: null
  })),
  on(addItemFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  })),
  on(loadItemsSuccess, (state, { items }) => ({
    ...state,
    items,
    loading: false,
    error: null
  })),
  on(loadItemsFailure, (state, { error }) => ({
    ...state,
    loading: false,
    error
  }))
);

addItem(item: any) {
    this.store.dispatch(addItem({ item }));
  }
