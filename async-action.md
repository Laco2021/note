# How to use async action (api) in Redux-toolkit

## `createAsyncThunk`

```javascript
/** module/cart/store/cartslice.js */

// 1️⃣ create thunk
const fetchCartGoods = createAsyncThunk(
    '@cart/fetchCartGoods'
    async (param, thunkAPI) => {
        const response = await cartAPI.fetchCartGoods();
        return response;
    }
);

// 2️⃣ create extraReducers to match the api.
const cartSlice = createSlice({
    name: '@cart',
    initialState,
    reducers: {},
    extraReducers: (builder) => {
        // addCase (pending/fulfilled/rejected)
        builder.addCase(fetchCartGoods.pending, (state, { payload }) => {
            state.isFetching = true;
        });        
        builder.addCase(fetchCartGoods.fulfilled, (state, action) => {
            state.isFetching = false;
            state.isFirstLoadComplete = true;
            state.entities.push(action.payload)
        })
    },
});
```

### Action Name

It would create 3 action, according to the name:

- `pending`: `@cart/fetchCartGoods/pending`
- `fulfilled`: `@cart/fetchCartGoods/fulfilled`
- `rejected`: `@cart/fetchCartGoods/rejected` 

---

### Parameter in `createAsyncThunk.payloadcreator`

`param`: parameter from dispatch

```javascript
dispatch(thunkAction({ goodsId: 1557 }));
```

`thunkAPI`: can get other state or dispatch action.
Find more on [thunkAPI](https://redux-toolkit.js.org/api/createAsyncThunk#payloadcreator)

---

### ExtraReducers

`builder.addMatcher`:

1. If using matcher for the reducer, please move it to end of the extraReducers.
2. The matcher would match all action from store. e.g. includes(cart/alert/product/productComments)

`Matching Utilities`

There are several type-safe action matching utilities.

- `isPending`
- `isFulfilled`
- `isRejected`

Find more on [builder.addMatcher](https://redux-toolkit.js.org/api/createReducer#builderaddmatcher)
Find more on [Matching Utilities](https://redux-toolkit.js.org/api/matching-utilities)
