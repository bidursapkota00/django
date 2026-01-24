## Lab: CRUD with JavaScript

**Grocery Bud**

Build a Grocery list app using plain JavaScript.

**Create Project**

```bash
cd Desktop
mkdir grocery-bud
cd grocery-bud
code .
```

**Project Structure**

```text
grocery-bud/
├── css/
│   ├── global.css
│   ├── single-item.css
│   ├── items.css
│   └── form.css
├── js/
│   ├── data.js
│   ├── single-item.js
│   ├── items.js
│   ├── form.js
│   └── app.js
└── index.html
```

---

**Create HTML File**

**Create `index.html`**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Grocery Bud</title>
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/7.0.1/css/all.min.css"
    />
    <link rel="stylesheet" href="css/global.css" />
    <link rel="stylesheet" href="css/single-item.css" />
    <link rel="stylesheet" href="css/items.css" />
    <link rel="stylesheet" href="css/form.css" />
  </head>
  <body>
    <section class="section-center">
      <div id="app"></div>
    </section>

    <script type="module" src="js/app.js"></script>
  </body>
</html>
```

---

**Setup Global Styles**

**Create `css/global.css`**

```css
*,
::after,
::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #eee;
  font-family: Helvetica, sans-serif;
  font-weight: 400;
  line-height: 1.2;
  color: #222;
}

.section-center {
  margin: 0 auto;
  margin-top: 8rem;
  max-width: 30rem;
  background: #fff;
  border-radius: 0.25rem;
  padding: 2rem;
  position: relative;
}
```

---

**Create Test UI**

```html
<div id="app">
  <div class="items">
    <div class="single-item">
      <input type="checkbox" checked />
      <p style="text-decoration: line-through">milk</p>
      <button class="btn icon-btn edit-btn" type="button">
        <i class="fa-regular fa-pen-to-square"></i>
      </button>
      <button class="btn icon-btn remove-btn" type="button">
        <i class="fa-regular fa-trash-can"></i>
      </button>
    </div>
    <div class="single-item">
      <input type="checkbox" checked />
      <p style="text-decoration: line-through">bread</p>
      <button class="btn icon-btn edit-btn" type="button">
        <i class="fa-regular fa-pen-to-square"></i>
      </button>
      <button class="btn icon-btn remove-btn" type="button">
        <i class="fa-regular fa-trash-can"></i>
      </button>
    </div>
    <div class="single-item">
      <input type="checkbox" />
      <p style="text-decoration: none">eggs</p>
      <button class="btn icon-btn edit-btn" type="button">
        <i class="fa-regular fa-pen-to-square"></i>
      </button>
      <button class="btn icon-btn remove-btn" type="button">
        <i class="fa-regular fa-trash-can"></i>
      </button>
    </div>
    <div class="single-item">
      <input type="checkbox" />
      <p style="text-decoration: none">butter</p>
      <button class="btn icon-btn edit-btn" type="button">
        <i class="fa-regular fa-pen-to-square"></i>
      </button>
      <button class="btn icon-btn remove-btn" type="button">
        <i class="fa-regular fa-trash-can"></i>
      </button>
    </div>
  </div>
</div>
```

**Create SingleItem Component**

**Create `css/single-item.css`**

```css
.single-item {
  display: grid;
  grid-template-columns: auto 1fr auto auto;
  column-gap: 1rem;
  align-items: center;
}

.single-item p {
  text-transform: capitalize;
}

.single-item input[type="checkbox"] {
  cursor: pointer;
  width: 1rem;
  height: 1rem;
}

.single-item .btn {
  cursor: pointer;
  color: #fff;
  background: #06b6d4;
  border: transparent;
  border-radius: 0.25rem;
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
}

.single-item .btn:hover {
  background: #0e7490;
}

.single-item .remove-btn {
  background: #222;
}

.single-item .remove-btn:hover {
  background: #900e0e;
}
```

**Create Items Component Styles**

**Create `css/items.css`**

```css
.items {
  margin-top: 2rem;
  display: grid;
  row-gap: 1rem;
}
```

**Output**

At this stage, you should see a list of grocery items displayed with proper spacing.

![Grocery List Output](/grocery-bud-js/screenshots/op1.png)

---

**Create Data File**

**Create `js/data.js`**

```javascript
export const groceryItems = [
  { id: "1", name: "milk", completed: true },
  { id: "2", name: "bread", completed: true },
  { id: "3", name: "eggs", completed: false },
  { id: "4", name: "butter", completed: false },
];
```

---

**Create `js/single-item.js`**

```javascript
// Create SingleItem Element
export function createSingleItem(item) {
  const div = document.createElement("div");
  div.className = "single-item";

  div.innerHTML = `
    <input type="checkbox" ${item.completed ? "checked" : ""} />
    <p style="text-decoration: ${item.completed ? "line-through" : "none"}">
      ${item.name}
    </p>
    <button class="btn icon-btn edit-btn" type="button">
      <i class="fa-regular fa-pen-to-square"></i>
    </button>
    <button class="btn icon-btn remove-btn" type="button">
      <i class="fa-regular fa-trash-can"></i>
    </button>
  `;

  return div;
}
```

**Create `js/items.js`**

```javascript
import { createSingleItem } from "./single-item.js";

// Create Items Container
export function createItems(itemsArray) {
  const container = document.createElement("div");
  container.className = "items";

  itemsArray.forEach((item) => {
    const itemElement = createSingleItem(item);
    container.appendChild(itemElement);
  });

  return container;
}
```

**Create `js/app.js`**

```javascript
import { groceryItems } from "./data.js";
import { createItems } from "./items.js";

let items = groceryItems;

// Render App
function render() {
  const app = document.getElementById("app");
  app.innerHTML = "";

  const itemsElement = createItems(items);
  app.appendChild(itemsElement);
}

// Initialize App
render();
```

**Output**

At this stage, you should again see a list of grocery items displayed with proper spacing.

![Grocery List Output](/grocery-bud-js/screenshots/op1.png)

---

**ADD Edit Completed Feature**

**Update `js/app.js`**

```js
// ....

// Edit Completed Function
export function editCompleted(itemId) {
  items = items.map((item) => {
    if (item.id === itemId) {
      return { ...item, completed: !item.completed };
    }
    return item;
  });
  render();
}
```

**Update `js/single-item.js`**

```javascript
import { editCompleted } from "./app.js";

function createSingleItem(item) {
  // ....

  // Add event listener for checkbox
  const checkbox = div.querySelector('input[type="checkbox"]');
  checkbox.addEventListener("change", () => editCompleted(item.id));

  return div;
}
```

**Output**

Now you can check/uncheck items to mark them as completed.

![Grocery List Output](/grocery-bud-js/screenshots/op2.png)

---

**ADD Delete Feature**

**Update `js/app.js`**

```javascript
// ....

// Remove Item Function
export function removeItem(itemId) {
  items = items.filter((item) => item.id !== itemId);
  render();
  setTimeout(() => alert("Item Deleted Successfully!"), 0);
}
```

**Update `js/single-item.js`**

```javascript
import { editCompleted, removeItem } from "./app.js";

function createSingleItem(item) {
  // ....

  // Add event listener for remove button
  const removeBtn = div.querySelector(".remove-btn");
  removeBtn.addEventListener("click", () => removeItem(item.id));

  return div;
}
```

**Output**

Now you can delete items and see alert notification.

![Grocery List Output](/grocery-bud-js/screenshots/op3.png)

---

**Create Form Component to Add New Grocery Item**

**Create Test Form UI**

**Test Update `index.html`**

```html
<div id="app">
  <form>
    <h2>grocery bud</h2>
    <div class="form-control">
      <input type="text" class="form-input" placeholder="e.g. eggs" />
      <button type="submit" class="btn">add item</button>
    </div>
  </form>
</div>
```

**Create `css/form.css`**

```css
form h2 {
  text-align: center;
  margin-bottom: 1.5rem;
  text-transform: capitalize;
  font-weight: normal;
}

.form-control {
  display: grid;
  grid-template-columns: 1fr 100px;
}

.form-input {
  width: 100%;
  padding: 0.375rem 0.75rem;
  border-radius: 0;
  border-top-left-radius: 0.25rem;
  border-bottom-left-radius: 0.25rem;
  border: 1px solid #ddd;
}

.form-input::placeholder {
  color: #aaa;
}

.form-control .btn {
  cursor: pointer;
  color: #fff;
  background: #06b6d4;
  border: transparent;
  border-radius: 0;
  border-top-right-radius: 0.25rem;
  border-bottom-right-radius: 0.25rem;
  padding: 0.375rem 0.75rem;
  text-transform: capitalize;
}

.form-control .btn:hover {
  background: #0e7490;
}
```

**Create `js/form.js`**

```js
import { addItem } from "./app.js";

// Create Form Element
export function createForm() {
  const form = document.createElement("form");

  form.innerHTML = `
    <h2>grocery bud</h2>
    <div class="form-control">
      <input
        type="text"
        class="form-input"
        placeholder="e.g. eggs"
      />
      <button type="submit" class="btn">
        add item
      </button>
    </div>
  `;

  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const input = form.querySelector(".form-input");
    const value = input.value.trim();

    if (!value) {
      alert("Please provide value");
      return;
    }

    addItem(value);
    input.value = "";
  });

  return form;
}
```

**Update `js/app.js`**

```javascript
// ....
import { createForm } from "./form.js";

// Render App
function render() {
  const app = document.getElementById("app");
  app.innerHTML = "";

  const formElement = createForm();
  const itemsElement = createItems(items);

  app.appendChild(formElement);
  app.appendChild(itemsElement);
}

// Generate unique ID
function generateId() {
  return Date.now().toString(36) + Math.random().toString(36).substr(2);
}

// Add Item Function
export function addItem(itemName) {
  const newItem = {
    name: itemName,
    completed: false,
    id: generateId(),
  };
  items = [...items, newItem];
  render();
  setTimeout(() => alert("Item Added Successfully!"), 0);
}
```

**Output**

Now you can add new grocery items to the list.

![Grocery List Output](/grocery-bud-js/screenshots/op4.png)

---

**ADD Edit Grocery Name Feature**

**Update `js/app.js`**

```javascript
// ....

let editId = null;

// Render App
function render() {
  const app = document.getElementById("app");
  app.innerHTML = "";

  const formElement = createForm(
    editId,
    editId ? items.find((item) => item.id === editId) : null,
  ); // edited line
  const itemsElement = createItems(items);

  app.appendChild(formElement);
  app.appendChild(itemsElement);
}

// Initialize App
render();

// Update Item Name Function
export function updateItemName(newName) {
  items = items.map((item) => {
    if (item.id === editId) {
      return { ...item, name: newName };
    }
    return item;
  });
  editId = null;
  render();
  setTimeout(() => alert("Item Updated Successfully!"), 0);
}

// Set Edit ID Function
export function setEditId(itemId) {
  editId = itemId;
  render();

  // Focus input after render
  setTimeout(() => {
    const input = document.querySelector(".form-input");
    if (input) {
      input.focus();
    }
  }, 0);
}
```

**Update `js/form.js`**

```js
import { addItem, updateItemName } from "./app.js"; // edited

// Create Form Element
export function createForm(editId, itemToEdit) {
  const form = document.createElement("form");

  // added value and dynamic button name
  form.innerHTML = `
    <h2>grocery bud</h2>
    <div class="form-control">
      <input
        type="text"
        class="form-input"
        placeholder="e.g. eggs"
        value="${itemToEdit ? itemToEdit.name : ""}"
      />
      <button type="submit" class="btn">
        ${editId ? "edit item" : "add item"}
      </button>
    </div>
  `;

  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const input = form.querySelector(".form-input");
    const value = input.value.trim();

    if (!value) {
      alert("please provide value", "error");
      return;
    }

    // added conditions
    if (editId) {
      updateItemName(value);
    } else {
      addItem(value);
    }

    input.value = "";
  });

  return form;
}
```

**Update `js/single-item.js`**

```js
import { editCompleted, removeItem, setEditId } from "./app.js";

// Create SingleItem Element
export function createSingleItem(item) {
  // ....

  // Add event listener for edit button
  const editBtn = div.querySelector(".edit-btn");
  editBtn.addEventListener("click", () => setEditId(item.id));
}
```

**Output**

Now you can edit grocery item names by clicking the edit button.

![Grocery List Output](/grocery-bud-js/screenshots/op5.png)

---

**Save Grocery to Local Storage**

**Update `js/app.js`**

```javascript
// Remove this line:
// import { groceryItems } from './data.js';

// Local Storage Functions
function getLocalStorage() {
  const list = localStorage.getItem("grocery-list");
  if (list) {
    return JSON.parse(list);
  }
  return [];
}

function setLocalStorage(itemsArray) {
  localStorage.setItem("grocery-list", JSON.stringify(itemsArray));
}

// Initialize items from local storage
let items = getLocalStorage();
let editId = null;

// ....

// Add Item Function
function addItem(itemName) {
  // ....
  setLocalStorage(items);
  render();
}

// Edit Completed Function
function editCompleted(itemId) {
  // ....
  setLocalStorage(items);
  render();
}

// Remove Item Function
function removeItem(itemId) {
  items = items.filter((item) => item.id !== itemId);
  setLocalStorage(items);
  render();
}

// Update Item Name Function
function updateItemName(newName) {
  // ....
  setLocalStorage(items);
  render();
}

// ....
```

---

**Run the Project**

Open `index.html` in your browser with live server to see your Grocery Bud in action. All data will now persist in local storage across browser sessions.

![Grocery List Output](/grocery-bud-js/screenshots/op6.png)

---

---

---
