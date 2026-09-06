# 🍽️ Restaurant Management System

A console-based restaurant order management application built in Java, designed around
classic Gang-of-Four design patterns to demonstrate clean separation of concerns,
extensibility, and maintainable object-oriented architecture.

> Built as a learning/portfolio project focused on **applying design patterns correctly**
> in a realistic domain (order lifecycle, payment processing, menu composition).

---

## ✨ Features

- 📋 **Menu display** — view all available items
- 🧾 **Order creation** — build an order by adding items one at a time
- 🔄 **Order status tracking** — move orders through `UNDER_PREPARATION → COOKED → PREPARED`
- 💳 **Flexible payment** — pay by **Cash** (5% discount) or **Card** (2% surcharge),
  selectable at checkout without touching business logic
- 📑 **Order history / listing** — inspect active and recent orders
- 🖥️ **Console UI** — simple, dependency-free text interface (easy to swap for a GUI or web front end later)

---

## 🛠️ Tech Stack

| Layer         | Technology              |
|---------------|--------------------------|
| Language      | Java (JDK 11+)           |
| Build         | Plain `javac` / any standard Java build tool (Maven-compatible package layout) |
| UI            | Console (`java.util.Scanner`) |
| Architecture  | Layered MVC + Design Patterns |

No external dependencies — it runs with a stock JDK.

---

## 📂 Project Structure

```
src/main/java/
├── org/example/
│   └── Main.java                              # Application entry point
└── restaurantmanagementsystem/
    ├── controller/
    │   └── RestaurantController.java          # Orchestrates the console flow
    ├── model/
    │   ├── core/
    │   │   └── RestaurantManager.java          # Singleton — owns all orders
    │   ├── menu/
    │   │   ├── MenuComponent.java              # Composite base + menu Singleton
    │   │   ├── Categorie.java                  # Composite: group of menu items
    │   │   ├── Plat.java                       # Composite: leaf menu item
    │   │   └── MenuItem.java                   # Simple order-facing item DTO
    │   ├── order/
    │   │   ├── Order.java                      # Observable order aggregate
    │   │   ├── OrderItem.java                  # Order line (item + quantity)
    │   │   └── OrderStatus.java                # Order lifecycle enum
    │   └── payment/
    │       ├── PaymentStrategy.java            # Strategy interface
    │       ├── CashPayment.java                # Strategy: 5% discount
    │       └── CardPayment.java                # Strategy: 2% fee
    ├── observer/
    │   ├── Observable.java                     # Observer subject interface
    │   ├── Observer.java                       # Observer interface
    │   └── OrderObserver.java                  # Concrete observer (logs updates)
    └── view/console/
        ├── RestaurantConsoleView.java          # Main menu / payment / status prompts
        ├── ConsoleOrderView.java                # Order listing view
        └── ConsoleMenuView.java                 # Generic menu/message helper view
```

For a full breakdown of *why* the code is organized this way, see
[`ARCHITECTURE.md`](./ARCHITECTURE.md).

---

## ▶️ Running the Project

```bash
# From the project root
javac -d out $(find src/main/java -name "*.java")
java -cp out org.example.Main
```

You'll be dropped into a text menu:

```
=== RESTAURANT MANAGEMENT SYSTEM ===
1. Show Menu
2. Create Order
3. Select Existing Order
4. Update Order Status
5. Show All Orders
6. Payment
0. Exit
```

**Typical flow:** `1` (browse menu) → `2` (create an order, add item IDs, `0` to finish)
→ `4` (advance status to `PRÊT`/PREPARED) → `6` (pay by cash or card).

> ⚠️ Payment is only accepted once an order's status is `PREPARED` — this is enforced in
> `RestaurantManager.processPayment()`.

---

## 🎯 Design Highlights

This project intentionally applies four design patterns to solve real problems in the
domain rather than as an academic exercise:

| Pattern       | Where                                   | Problem it solves |
|---------------|------------------------------------------|--------------------|
| **Singleton** | `RestaurantManager`, `MenuComponent`     | One authoritative source of truth for orders and the menu |
| **Strategy**  | `PaymentStrategy` (`CashPayment`, `CardPayment`) | Swap payment/pricing rules without touching order logic |
| **Observer**  | `Observable` / `Observer` / `OrderObserver` | Decouple "order changed" from "who needs to know" (kitchen display, logging, notifications) |
| **Composite** | `MenuComponent` / `Categorie` / `Plat`   | Model a menu as arbitrarily nested categories and dishes, treated uniformly |

See `ARCHITECTURE.md` for diagrams, data flow, and an honest list of what's wired up
today vs. what's scaffolded for future use.

---

## 🗺️ Roadmap / Ideas

- [ ] Wire `Order`'s `Observable` interface to a live `OrderObserver` (e.g. auto-register
      one in `RestaurantController` so status changes are actually broadcast)
- [ ] Replace the hardcoded `createMenuItemById` switch with the `Categorie`/`Plat`
      Composite tree so the menu is genuinely data-driven
- [ ] Persist orders (file or embedded DB) instead of in-memory `HashMap`
- [ ] Add unit tests (JUnit) around `RestaurantManager` and the payment strategies
- [ ] Swap the console view layer for a JavaFX or web UI — the controller/model split
      already supports this

---

## 📄 License

MIT — free to use, modify, and learn from.
