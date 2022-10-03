# React-based Theme Selector for Redux Toolkit (RTK) with Next.JS.

## About

This script's goal is to create a site-wide dark- and light-theme, without having to add `dark` or `light` classnames to your HTML elements. Thus in that sense, it is very easy to maintain.

And by using a global store, you can set your theme selector anywhere you desire without having to prop drill or clutter your project with another useContext.

The theme will persist when the user clicks a <Link>, but not if they directly navigate to a URL or if they otherwise leave the website. If you want it to persist, store the variable in localStorage.

## Structure

- /app/store.ts
- /components/GetTheme.tsx
- /features/theme/themeSlice.ts
- /pages/_app.tsx
- /pages/index.tsx
- /public/dark-theme.css
- /public/light-theme.css
- /styles/globals.css

## Notes

Your `globals.css` should operate as your default style; this is where you will set flexbox styles, height, margin, etc.

In your `dark-theme.css` and `light-theme.css` files, you'll set your colors, background colors, etc.   

## Create store

```
// store.ts

import { configureStore } from '@reduxjs/toolkit'
import themeReducer from '../features/theme/themeSlice';

export const store = configureStore({
    reducer: {
        theme: themeReducer,
    },
})

export type RootState = ReturnType<typeof store.getState>
export type AppDispatch = typeof store.dispatch

```


## Create slice

```
//themeSlice.ts

import {createSlice, Draft} from '@reduxjs/toolkit'

export interface ThemeOptions {
    dark: "dark-theme";
    light: "light-theme";
}

const themeOptions: ThemeOptions = {
    dark: "dark-theme",
    light: "light-theme",
}

export interface ThemeState {
    value: "dark" | "light";
}

const initialState: ThemeState = {
    value: themeOptions.dark,
}

export const themeSlice = createSlice({
    name: 'theme',
    initialState,
    reducers: {
        lightTheme: (state: Draft<ThemeState>) => {
            state.value = themeOptions.light;
        },

        darkTheme: (state: Draft<ThemeState>) => {
            state.value = themeOptions.dark;
        },
    },
});

export const { lightTheme, darkTheme } = themeSlice.actions;
export default themeSlice.reducer;
```

## Create GetTheme component

```
//GetTheme.tsx

import {Provider, useSelector} from "react-redux";
import {RootState, store} from "../app/store";
import {ThemeOptions} from "../features/theme/themeSlice";

function GetTheme(){
    return (
        <Provider store={store}>
            <ThemeLink/>
        </Provider>
    );
}

function ThemeLink(){
    const themeType: ThemeOptions = useSelector((state: RootState) => state.theme.value);
    return (
        <link rel={"stylesheet"} href={`/${themeType}.css`}/>
    );
}

export default GetTheme;
```

## Change App file

``` 
//_app.tsx 
// Most important code to notice: Provider and GetTheme.

import '../styles/globals.css'
import type { AppProps } from 'next/app'
import {store} from '../app/store'
import {Provider} from "react-redux";
import GetTheme from "../components/GetTheme";

function MyApp({ Component, pageProps }: AppProps) {
  return (
      <Provider store={store}>
          <GetTheme/>
        <Component {...pageProps} />
      </Provider>
  );
}

export default MyApp;
```

## Create dark-theme and light-theme files.

```
// dark-theme.css

body{
    background: #000;
    color: #fff;
}

```

```
// light-theme.css

body{
    background: #fff;
    color: #000;
}
```

## Add theme-selector buttons to any page or component you desire, e.g. 

```
// index.tsx

function Home<NextPage>(){
  const dispatch = useDispatch();

// ...

<button onClick={() => { dispatch(lightTheme()); }}>Light</button>
<button onClick={() => { dispatch(darkTheme()); }}>Dark</button>

```

