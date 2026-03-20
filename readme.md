# 🍜 API Testing Lab: Auntie Som’s Noodle Stall

> **Objective:** Prove that Nephew Lek’s "it works on my machine" attitude is a recipe for disaster.

---

## 📖 The Backstory

Auntie Som runs a **legendary noodle stall**. Her Tom Yum is world-class, her customers are loyal, and her business is booming.  

Recently, her nephew **Lek** (who just finished a 2-week coding bootcamp) decided to "digitize" the business. He built a backend API to handle online orders. Auntie Som is thrilled, but the code… well, Lek says:  

> *"It’s fine lah Auntie! I tested it once with my own phone. No need for professional testing!"*

**You are the Last Line of Defense.**  
Before Auntie Som launches this app to 1,000+ hungry customers, you must audit the system, find the logic flaws, and automate the validation using **Bruno**.

---

## 🎯 Learning Objectives

By the end of this lab, you should be able to:
- 🖋️ Write **Bruno test scripts** for automated validation.
- 📦 Extract values from API responses using **Post-response scripts**.
- 🔐 Manage and use **Environment Variables**.
- 🧠 Verify **Business Rules**, not just HTTP status codes.
- 🚀 Catch regressions automatically before they hit production.

---

## 🛠️ Setup Instructions

### 1️⃣ Prepare the Project
- **Fork this repository** to your own GitHub account.
- **Clone it** locally to your machine.

### 2️⃣ Start the API Server
The backend is a lightweight Flask application.
- **Requirements:** Python 3.10+ installed.
- **Navigate** to the `auntie-som-noodle-api` folder.
- **Install dependencies:**  
  ```bash
  pip install flask
  ```
- **Run the server:**  
  ```bash
  python app.py
  ```
- The server will run at: `http://localhost:5000`

### 3️⃣ Setup Bruno
- Download and install [Bruno](https://www.usebruno.com).
- Create a **New Collection** named `Auntie Som Lab`.
- Create an **Environment** (e.g., `local`) and set a variable `baseUrl` to `http://localhost:5000`.

---

## 📑 API Reference

### 🔐 Authentication
`POST /auth/login` - Get an access token.
- **Body (JSON):**
  ```json
  {
    "username": "student",
    "password": "hungry"
  }
  ```
- **Response (200 OK):**
  ```json
  {
    "accessToken": "uuid-token-string",
    "expiresIn": 3600
  }
  ```

---

### 🍜 Menu
`GET /menu` - View available items and stock levels.
- **Response (200 OK):**
  ```json
  [
    { "id": 1, "name": "Tom Yum Noodles", "price": 50, "stock": 2 },
    { "id": 2, "name": "Fried Rice", "price": 45, "stock": 1 }
  ]
  ```

---

### 🛒 Orders
`POST /orders` - Place a new order.
- **Headers:** `Authorization: Bearer <token>`
- **Body (JSON):**
  ```json
  {
    "itemId": 1,
    "quantity": 1
  }
  ```
- **Response (200 OK):**
  ```json
  {
    "orderId": "ORD-abc123",
    "status": "created",
    "totalPrice": 45
  }
  ```

`GET /orders/<orderId>` - Retrieve order details.
- **Response (200 OK):**
  ```json
  {
    "orderId": "ORD-abc123",
    "status": "created",
    "totalPrice": 45
  }
  ```

---

## 🕵️ Your Mission: The Silent Auditor

Your task is to create a comprehensive Bruno collection that validates the entire workflow.  

⚠️ **The Catch:** Nephew Lek left several **logic bugs** in the system. Some endpoints might return `200 OK` even when the business logic is completely broken.

**Your collection must include:**
1.  **Auth Flow**: Automatically store the `accessToken` from login and use it for subsequent requests.
2.  **Order Validation**: Ensure orders can only be placed with valid auth, valid items, and available stock.
3.  **Data Integrity**: Check that the `totalPrice` calculated by the API matches your expectations.
4.  **Edge Cases**: What happens if you order more than what's in stock? What happens if you request a non-existent order?

> [!TIP]
> **Clicking "Send" is not testing.** Your tests must contain scripts (Assertions or Javascript) that **FAIL** when the system behaves incorrectly.

---

## 🧠 Rules of the Lab
- ❌ **Do NOT** modify the `app.py` backend code.
- ❌ **Do NOT** rely on manual checking.
- ✅ **Use environment variables** for the Base URL and Tokens.
- ✅ **Use Scripts/Assertions** for all validations.

---

## 📦 What to Submit

1.  **Push your Bruno collection folder** to your GitHub repository.
2.  **Update your README.md** (the one in your fork) with:
    - A summary of the bugs you discovered.
    - How your tests detect these bugs.
4.  **Send the GitHub link** to your instructor via Discord.

---
*Good luck. Auntie Som is counting on you!* 🍜🔥

---

### 🔍 Bugs Found

1. **Incorrect totalPrice Calculation**
   - Orders are created with an incorrect total price.
   - The totalPrice returned by the API is always 5 less than the expected value.

2. **Invalid Quantity Handling**
   - The API allows creating orders with quantity = 0 or negative values.
   - When a negative quantity is used, the system incorrectly increases the stock instead of decreasing it.

3. **Inconsistent Stock Data**
   - The API reports stock = 0 in `/menu`, but the actual stock in the database is greater than 0.
   - This indicates a mismatch between displayed data and actual data.

4. **Allows Ordering Beyond Available Stock**
   - The API allows ordering more items than available stock.
   - After placing such an order, the stock becomes negative.

5. **Incorrect Status Code for Non-existent Orders**
   - Requesting a non-existent order returns status `200 OK` instead of `404 Not Found`.

---

### 🧠 How Tests Detect These Bugs

1. **Total Price Validation**
   - The test calculates the expected total price using:
     ```
     expected = price × quantity
     ```
   - It then compares this value with `totalPrice` returned by the API.
   - The test fails when the values do not match.

2. **Invalid Quantity Testing**
   - Test cases send requests with `quantity = 0` and negative values.
   - The test verifies:
     - The API should reject these inputs.
     - If the order is created successfully, it is flagged as a bug.
   - Additional checks confirm that stock should not increase after ordering.

3. **Stock Consistency Check**
   - The test retrieves stock from `/menu`.
   - After operations, it compares expected stock behavior with actual results.
   - Any inconsistency indicates incorrect system logic.

4. **Out-of-Stock Validation**
   - The test attempts to order more than the available stock.
   - It verifies:
     - The request should fail.
     - Stock should never become negative.
   - If the API allows the operation, the test fails.

5. **Non-existent Order Validation**
   - The test sends a request with an invalid `orderId`.
   - It verifies that:
     - The API should return `404`.
     - If `200` is returned, it is marked as a bug.

---

# 6733062221 Natthanicha Jamjruee 
