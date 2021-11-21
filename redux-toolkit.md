# Redux Toolkit

- [Redux Toolkit](#redux-toolkit)
  - [Redux Store](#redux-store)
  - [Slice](#slice)
  - [How to use](#how-to-use)
  - [Selector](#selector)

---

## Redux Store

A global data storage of application. As the [Three Principles](https://redux.js.org/understanding/thinking-in-redux/three-principles), data should be an object tree within a single store.

This is an example of the `state` look like:

```javascript
state = {
    cart: {
        general: {},
        gifts:[],
        packages:[]
    },
    alert: {
        isOpen: false,
        message: ""
    },
    product: {
        productName:"",
        productCategory: "",
        productDesc: ""
    },
    productComment: {
        comments:[],
        thumb:[]
    }
}
```

```javascript
/** reduxStore/store.js */
import { configureStore } from "@reduxjs/toolkit";
import reducer from "./reducers";
export const store = configureStore({
  reducer,
});

/** reduxStore/reducers.js */
import alert from "@/component/module/alert/alertAlice";
import cart from "@/component/module/cart/cartSlice";
import product from "@/component/module/product/productSlice";
import productComment from "@/component/module/product/commentSlice";

return {
    alert,
    cart,
    product,
    productComment
};
```

## Slice

A [Slice](https://redux-toolkit.js.org/api/createSlice) include `name`, `initialState`, `reducer` and `action`

- `name`: prefix of the action name, open action would be `@alert/open`. Unique slice name in [store](#redux-store-storeid).

- `initialState`: store look like when the application is created.

- `reducer`: according to action type, change data in store. It using [immer](https://immerjs.github.io/immer/) library to handle [immutable update logic](https://redux-toolkit.js.org/usage/immer-reducers).

- `action`: `createSlice` would create action accroding to `reducers` and `name`. e.g. `open`. action type `@alert/open`.

```javascript
const alertSlice = createSlice({
    name: '@alert',
    initialState: { isOpen:false },
    reducers: {
        open: (state, action) => {
            state.isOpen = true;
            state.message = action.payload;
         },
        close: (state, { payload }) => {
            state.isOpen = false;
            state.message = "";
        },
    },
});

const { actions, reducer } = alertSlice;
export const { open, close } = actions;
export default reducer;
```

## How to use

```javascript
/** Component.hooks.js */
import { useDispatch } from "react-redux";
import { open } from "./slice";

function useOpenAlert(){
    const dispatch = useDispatch()

    // message is passing  to reducer through "payload"
    return (message) => dispatch(open(message));
}

/** Component.js */
import { useOpenAlert } from "./hooks";

// ❌ Cannot dispatch action outside of component 
useOpenAlert("Alert without Component");

export default function Component(props) {
    // 1️⃣ Settup the hook. 
    const openAlert = useOpenAlert();

    // 2️⃣ call openAlert on anywhere
    const handleClick = () => {
        openAlert("Message");
    }
    useEffect(()=>{
        openAlert("Message");
    }, []);
}
```

## Selector

Using `selector` to retrieve data from store. redux-toolkit using [reselect library](https://github.com/reduxjs/reselect)

```javascript
/** cartSlice.js */
const selectSelf = (state) => state.cart;
const selectGeneral = createSelector(selectSelf, (cart) => cart.general);
const selectGeneralByGoodsId = createSelector([selectGeneral, (state, goodsId) => goodsId], (general, goodsId) => general.find(g => g.goods_id === goodsId));
const selectTotalAmount => createSelector([selectGeneral, selectGifts, selectPackages], (genreal, gifts, packages) => {
    let sum = 0;
    general.reduce((sum, g) => sum += parseInt(g.deal_price, 10), sum);
    gifts.reduce((sum, g) => sum += parseInt(g.deal_price, 10), sum);
    packages.map(p => p.package_goods).reduce((sum, g) => sum += parseInt(g.deal_price, 10), sum);
    return sum;
});

/** Component.hooks.js */
import { useSelector } from "react-redux";
import { selectGeneral } from "./cartSlice";

// selector hook
export const useSelectorWithGeneral = () => {
    return useSelector(selectGeneral);
};

// selector hook with parameter.
export const useSelectorWithGeneralByGoodsId = (goodsId) => {
    return useSelector(selectGeneralByGoodsId(goodsId));
}
```
