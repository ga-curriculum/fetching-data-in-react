# ![Fetching Data in React - Setup](./assets/hero.png)

## Setup

Open your Terminal application and navigate to your **`~/code/ga/lectures`** directory:

```bash
cd ~/code/ga/lectures
```

Create a new Vite project for your React app:

```bash
npm create vite@latest
```

You'll be prompted to provide a project name. `vite-project` is the default, but when you start typing that default will go away. Choose a name that makes sense for this project; in this case, we'll call the app `fetching-data-in-react`.

You'll be prompted to choose a project name. Let's name it `fetching-data-in-react`.

  Select a framework. Use the arrow keys to choose the `React` option and hit `Enter`.

  Select a variant. Again, use the arrow keys to choose `JavaScript` and hit `Enter`.


Navigate to your new project directory and install the necessary dependencies:

```bash
cd fetching-data-in-react
npm i
```

Open the project's folder in your code editor:

```bash
code .
```

### Clear `App.jsx`

Open the `App.jsx` file in the `src` directory and replace the contents of it with the following:

```jsx
// src/App.jsx

const App = () => {

  return (
    <h1>Hello world!</h1>
  );
}

export default App
```

Start the dev server by running `npm run dev` in your terminal and the go to `localhost:5173` in your browser.