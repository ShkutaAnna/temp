# temp

```javascript
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
