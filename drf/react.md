## Basic Project: Grocery Bud

Build a Grocery list app.

**Create Vite Project**

```bash
cd Desktop
npm create vite@latest grocery-bud -- --template react
code grocery-bud
```

**Project Structure**

```text
grocery-bud/
├── src/
│   ├── components/
│   │   ├── Form.jsx
│   │   ├── Form.css
│   │   ├── Items.jsx
│   │   ├── Items.css
│   │   ├── SingleItem.jsx
│   │   └── SingleItem.css
│   ├── data/
│   │   └── groceryItems.js
│   ├── App.jsx
│   ├── App.css
│   ├── index.css
│   └── main.jsx
```

---

**Create Data File**

**Create `src/data/groceryItems.js`**

```javascript
export const groceryItems = [
  { id: "1", name: "milk", completed: true },
  { id: "2", name: "bread", completed: true },
  { id: "3", name: "eggs", completed: false },
  { id: "4", name: "butter", completed: false },
];
```

---

**Setup Global Styles**

**Create `src/index.css`**

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
```

---

**Create SingleItem Component**

**Install react-icons**

```bash
npm install react-icons
```

**Create `src/components/SingleItem.jsx`**

```javascript
import { FiEdit, FiTrash2 } from "react-icons/fi";
import "./SingleItem.css";

const SingleItem = ({ item }) => {
  return (
    <div className="single-item">
      <input type="checkbox" checked={item.completed} onChange={() => null} />
      <p
        style={{
          textTransform: "capitalize",
          textDecoration: item.completed ? "line-through" : "none",
        }}
      >
        {item.name}
      </p>

      <button className="btn icon-btn" type="button" onClick={() => null}>
        <FiEdit size={18} />
      </button>

      <button
        className="btn icon-btn remove-btn"
        type="button"
        onClick={() => null}
      >
        <FiTrash2 size={18} />
      </button>
    </div>
  );
};

export default SingleItem;
```

**Create `src/components/SingleItem.css`**

```css
.single-item {
  display: grid;
  grid-template-columns: auto 1fr auto auto;
  column-gap: 1rem;
  align-items: center;
}

.single-item .btn {
  cursor: pointer;
  color: #fff;
  background: #06b6d4;
  border: transparent;
  border-radius: 0.25rem;
  padding: 0.375rem 0.75rem;
  font-size: 0.75rem;
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

---

**Create Items Component**

**Create `src/components/Items.jsx`**

```javascript
import SingleItem from "./SingleItem";
import "./Items.css";

const Items = ({ items }) => {
  return (
    <div className="items">
      {items.map((item) => {
        return <SingleItem key={item.id} item={item} />;
      })}
    </div>
  );
};

export default Items;
```

**Create `src/components/Items.css`**

```css
.items {
  margin-top: 2rem;
  display: grid;
  row-gap: 1rem;
}
```

---

**Update Main App Component**

**Update `src/App.jsx`**

```javascript
import Items from "./components/Items";
import { groceryItems } from "./data/groceryItems";
import "./App.css";

const App = () => {
  return (
    <section className="section-center">
      <Items items={groceryItems} />
    </section>
  );
};

export default App;
```

**Create `src/App.css`**

```css
.section-center {
  margin: 0 auto;
  margin-top: 8rem;
  max-width: 30rem;
  background: #fff;
  border-radius: 0.25rem;
  padding: 2rem;
}
```

**Output**

![Grocery List Output](/grocery-bud/screenshots/grocery-op1.png)

---

**ADD Edit Completed Feature**

**Update `src/App.jsx`**

```javascript
import Items from "./components/Items";
import { groceryItems } from "./data/groceryItems";
import { useState } from "react";
import "./App.css";

const App = () => {
  const [items, setItems] = useState(groceryItems);

  const editCompleted = (itemId) => {
    const newItems = items.map((item) => {
      if (item.id === itemId) {
        return { ...item, completed: !item.completed };
      }
      return item;
    });
    setItems(newItems);
  };

  return (
    <section className="section-center">
      <Items items={items} editCompleted={editCompleted} />
    </section>
  );
};

export default App;
```

---

**Update `src/components/Items.jsx`**

```javascript
import SingleItem from "./SingleItem";
import "./Items.css";

const Items = ({ items, editCompleted }) => {
  return (
    <div className="items">
      {items.map((item) => {
        return (
          <SingleItem key={item.id} item={item} editCompleted={editCompleted} />
        );
      })}
    </div>
  );
};

export default Items;
```

---

**Update `src/components/SingleItem.jsx`**

```javascript
// ...
const SingleItem = ({ item, editCompleted }) => {
  return (
    <div className="single-item">
      <input
        type="checkbox"
        checked={item.completed}
        onChange={() => editCompleted(item.id)}
      />
      // ....
    </div>
  );
};

export default SingleItem;
```

**Output**

![Grocery List Output with Edit](/grocery-bud/screenshots/grocery-op2.png)

---

**ADD Delete Feature**

**Install react-toastify**

```bash
npm install react-toastify
```

**Update `src/App.jsx`**

```javascript
// ...
import { ToastContainer, toast } from "react-toastify";
import "react-toastify/dist/ReactToastify.css";
import "./App.css";

const App = () => {
  const [items, setItems] = useState(groceryItems);

  // ...

  const removeItem = (itemId) => {
    const newItems = items.filter((item) => item.id !== itemId);
    setItems(newItems);
    toast.success("item deleted");
  };

  return (
    <section className="section-center">
      <ToastContainer position="top-center" />
      <Items
        items={items}
        editCompleted={editCompleted}
        removeItem={removeItem}
      />
    </section>
  );
};

export default App;
```

**Update `src/App.css`**

```css
/* .... */

.Toastify__toast {
  text-transform: capitalize;
}
```

---

**Update `src/components/Items.jsx`**

```javascript
import SingleItem from "./SingleItem";
import "./Items.css";

const Items = ({ items, editCompleted, removeItem }) => {
  return (
    <div className="items">
      {items.map((item) => {
        return (
          <SingleItem
            key={item.id}
            item={item}
            editCompleted={editCompleted}
            removeItem={removeItem}
          />
        );
      })}
    </div>
  );
};

export default Items;
```

---

**Update `src/components/SingleItem.jsx`**

```javascript
// ...
const SingleItem = ({ item, editCompleted, removeItem }) => {
  return (
    <div className="single-item">
      // ....
      <button
        className="btn icon-btn remove-btn"
        type="button"
        onClick={() => removeItem(item.id)}
      >
        <FiTrash2 size={18} />
      </button>
    </div>
  );
};

export default SingleItem;
```

**Output**

![Grocery List Output with Edit](/grocery-bud/screenshots/grocery-op3.png)

---

**Create Form Component to add new grocery item**

**Create `src/components/Form.jsx`**

```javascript
import { useState } from "react";
import { toast } from "react-toastify";
import "./Form.css";

const Form = ({ addItem }) => {
  const [newItemName, setNewItemName] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!newItemName) {
      toast.error("please provide value");
      return;
    }
    addItem(newItemName);
    setNewItemName("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>grocery bud</h2>
      <div className="form-control">
        <input
          type="text"
          className="form-input"
          value={newItemName}
          placeholder="e.g. eggs"
          onChange={(event) => setNewItemName(event.target.value)}
        />
        <button type="submit" className="btn">
          add item
        </button>
      </div>
    </form>
  );
};

export default Form;
```

**Create `src/components/Form.css`**

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

---

**Update Main App Component to add Grocery**

**Install `nanoid`**

```bash
npm install nanoid
```

**Update `src/App.jsx`**

```javascript
//...
import { nanoid } from "nanoid";
import Form from "./components/Form";

const App = () => {
  const [items, setItems] = useState(groceryItems);

  const addItem = (itemName) => {
    const newItem = {
      name: itemName,
      completed: false,
      id: nanoid(),
    };
    const newItems = [...items, newItem];
    setItems(newItems);
    toast.success("grocery item added");
  };

  //...

  return (
    <section className="section-center">
      <ToastContainer position="top-center" />
      <Form addItem={addItem} />
      <Items
        items={items}
        editCompleted={editCompleted}
        removeItem={removeItem}
      />
    </section>
  );
};

export default App;
```

**Output**

![Output with Menus](/grocery-bud/screenshots/grocery-op4.png)

---

**ADD Edit Grocery Name Feature**

**Update `src/App.jsx`**

```javascript
//...
import { useEffect, useRef, useState } from "react";
//....

const App = () => {
  const [items, setItems] = useState(groceryItems);
  const [editId, setEditId] = useState(null);
  const inputRef = useRef(null);

  useEffect(() => {
    if (editId && inputRef.current) {
      inputRef.current.focus();
    }
  }, [editId]);

  // ...

  const updateItemName = (newName) => {
    const newItems = items.map((item) => {
      if (item.id === editId) {
        return { ...item, name: newName };
      }
      return item;
    });
    setItems(newItems);
    setEditId(null);
    toast.success("item updated");
  };

  return (
    <section className="section-center">
      <ToastContainer position="top-center" />
      <Form
        addItem={addItem}
        updateItemName={updateItemName}
        editItemId={editId}
        itemToEdit={items.find((item) => item.id === editId)}
        inputRef={inputRef}
      />
      <Items
        items={items}
        editCompleted={editCompleted}
        removeItem={removeItem}
        setEditId={setEditId}
      />
    </section>
  );
};

export default App;
```

**Update `src/components/Items.jsx`**

```javascript
import SingleItem from "./SingleItem";
import "./Items.css";

const Items = ({ items, editCompleted, removeItem, setEditId }) => {
  return (
    <div className="items">
      {items.map((item) => {
        return (
          <SingleItem
            key={item.id}
            item={item}
            editCompleted={editCompleted}
            removeItem={removeItem}
            setEditId={setEditId}
          />
        );
      })}
    </div>
  );
};

export default Items;
```

**Update `src/components/SingleItem.jsx`**

```javascript
// ....
const SingleItem = ({ item, editCompleted, removeItem, setEditId }) => {
  return (
    <div className="single-item">
      // ....
      <button
        className="btn icon-btn"
        type="button"
        onClick={() => setEditId(item.id)}
      >
        <FiEdit size={18} />
      </button>
      // ....
    </div>
  );
};

export default SingleItem;
```

**Update `src/components/Form.jsx`**

```javascript
import { useState, useEffect } from "react";
import { toast } from "react-toastify";
import "./Form.css";

const Form = ({
  addItem,
  editItemId,
  updateItemName,
  itemToEdit,
  inputRef,
}) => {
  const [newItemName, setNewItemName] = useState("");

  useEffect(() => {
    if (itemToEdit) {
      setNewItemName(itemToEdit.name);
    } else {
      setNewItemName("");
    }
  }, [itemToEdit]);

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!newItemName) {
      toast.error("please provide value");
      return;
    }
    if (editItemId) {
      updateItemName(newItemName);
    } else {
      addItem(newItemName);
    }
    setNewItemName("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <h2>grocery bud</h2>
      <div className="form-control">
        <input
          type="text"
          className="form-input"
          value={newItemName}
          ref={inputRef}
          placeholder="e.g. eggs"
          onChange={(event) => setNewItemName(event.target.value)}
        />
        <button type="submit" className="btn">
          {editItemId ? "edit item" : "add item"}
        </button>
      </div>
    </form>
  );
};

export default Form;
```

**Output**

![Grocery List Output with Edit Name](/grocery-bud/screenshots/grocery-op5.png)

---

**Save Grocery to local storage**

**Update `src/App.jsx`**

```javascript
// import { groceryItems } from "./data/groceryItems";
// ....
const getLocalStorage = () => {
  let list = localStorage.getItem("grocery-list");
  if (list) {
    return JSON.parse(list);
  }
  return [];
};

const setLocalStorage = (items) => {
  localStorage.setItem("grocery-list", JSON.stringify(items));
};

const initialList = getLocalStorage();

const App = () => {
  const [items, setItems] = useState(initialList);
  // ....

  const addItem = (itemName) => {
    // ....
    setLocalStorage(newItems);
    toast.success("item added to the list");
  };

  const editCompleted = (itemId) => {
    // ...
    setLocalStorage(newItems);
  };

  const removeItem = (itemId) => {
    // ....
    setLocalStorage(newItems);
    toast.success("item deleted");
  };

  const updateItemName = (itemEditName) => {
    // ....
    setLocalStorage(newItems);
    toast.success("item updated");
  };
};
```

---

**Run the Project**

```bash
npm run dev
```

Visit `http://localhost:5173` to see your Grocery bud in action.
