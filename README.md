
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Restaurant Order Queue System</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Arial, sans-serif;
}

body {
    background: #101827;
    color: white;
    padding: 20px;
}

header {
    text-align: center;
    padding: 25px;
    background: linear-gradient(120deg,#e85d04,#ff9f1c);
    border-radius: 15px;
    margin-bottom: 25px;
}

header h1 { font-size: 27px; }
header p { margin-top: 10px; }

.container {
    max-width: 1100px;
    margin: auto;
}

.stats {
    display: grid;
    grid-template-columns: repeat(auto-fit,minmax(150px,1fr));
    gap: 15px;
    margin-bottom: 25px;
}

.card {
    background: #1e293b;
    padding: 20px;
    border-radius: 12px;
    text-align: center;
    border: 1px solid #334155;
}

.card h2 {
    color: #ff9f1c;
    margin-bottom: 10px;
}

.card p {
    font-size: 25px;
    font-weight: bold;
}

.section {
    background: #1e293b;
    padding: 22px;
    border-radius: 12px;
    margin-bottom: 22px;
}

.section h2 {
    color: #ff9f1c;
    margin-bottom: 18px;
}

input, select {
    width: 100%;
    padding: 12px;
    margin: 8px 0;
    border: none;
    border-radius: 7px;
    background: #334155;
    color: white;
    font-size: 15px;
}

button {
    padding: 12px 18px;
    border: none;
    border-radius: 7px;
    cursor: pointer;
    font-weight: bold;
    margin: 5px 3px;
    transition: .2s;
}

button:hover {
    transform: translateY(-2px);
    opacity: .9;
}

.add {
    background: #16a34a;
    color: white;
}

.process {
    background: #2563eb;
    color: white;
}

.reset {
    background: #dc2626;
    color: white;
}

.order {
    background: #334155;
    border-left: 5px solid #ff9f1c;
    padding: 15px;
    border-radius: 8px;
    margin: 10px 0;
    display: flex;
    justify-content: space-between;
    gap: 10px;
    flex-wrap: wrap;
}

.empty {
    text-align: center;
    color: #94a3b8;
    padding: 25px;
}

.status {
    color: #22c55e;
    font-weight: bold;
}

.queue-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit,minmax(110px,1fr));
    gap: 12px;
}

.slot {
    border: 2px dashed #64748b;
    border-radius: 10px;
    min-height: 90px;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 10px;
    color: #94a3b8;
}

.filled {
    border: 2px solid #f59e0b;
    background: #422006;
    color: white;
}

.log {
    background: #0f172a;
    padding: 12px;
    border-radius: 7px;
    margin: 8px 0;
    font-size: 14px;
    border-left: 3px solid #ff9f1c;
}

.semaphore {
    display: grid;
    grid-template-columns: repeat(auto-fit,minmax(120px,1fr));
    gap: 12px;
}

.semaphore div {
    background: #334155;
    padding: 15px;
    text-align: center;
    border-radius: 8px;
}

.semaphore strong {
    display: block;
    font-size: 24px;
    color: #ff9f1c;
    margin-top: 8px;
}

footer {
    text-align: center;
    padding: 20px;
    color: #94a3b8;
}

@media(max-width:500px) {
    body { padding: 10px; }
    header h1 { font-size: 22px; }
    .section { padding: 15px; }
}
</style>
</head>

<body>

<header>
    <h1>🍔 Restaurant Order Queue</h1>
    <p>Producer – Consumer Synchronization System</p>
    <p>Smart Kitchen Management Dashboard</p>
</header>

<div class="container">

<div class="stats">
    <div class="card">
        <h2>Total Orders</h2>
        <p id="total">0</p>
    </div>
    <div class="card">
        <h2>Queue Size</h2>
        <p id="count">0 / 5</p>
    </div>
    <div class="card">
        <h2>Completed</h2>
        <p id="completed">0</p>
    </div>
    <div class="card">
        <h2>Available Slots</h2>
        <p id="available">5</p>
    </div>
</div>

<div class="section">
    <h2>👨‍💼 Producer – Take Customer Order</h2>

    <input id="customer" placeholder="Enter customer name">

    <select id="food">
        <option value="Burger">Burger</option>
        <option value="Pizza">Pizza</option>
        <option value="Sandwich">Sandwich</option>
        <option value="Fried Rice">Fried Rice</option>
        <option value="Noodles">Noodles</option>
        <option value="Pasta">Pasta</option>
    </select>

    <button class="add" onclick="addOrder()">
        + Add Order
    </button>

    <p id="producerStatus" class="status">
        Waiting for new orders...
    </p>
</div>

<div class="section">
    <h2>🍳 Consumer – Kitchen Processing</h2>

    <p id="kitchenStatus">Kitchen is waiting for orders.</p>

    <button class="process" onclick="processOrder()">
        Process Next Order →
    </button>
</div>

<div class="section">
    <h2>📦 Live Order Queue</h2>
    <p style="margin-bottom:15px">
        Fixed Capacity: <span id="capacity">5</span>
    </p>

    <div class="queue-grid" id="queue"></div>
</div>

<div class="section">
    <h2>🔐 Semaphore Synchronization</h2>

    <div class="semaphore">
        <div>
            Empty
            <strong id="empty">5</strong>
        </div>
        <div>
            Full
            <strong id="full">0</strong>
        </div>
        <div>
            Mutex
            <strong id="mutex">1</strong>
        </div>
    </div>

    <p style="margin-top:15px;color:#94a3b8">
        Empty = Available slots | Full = Orders waiting |
        Mutex = Mutual exclusion lock
    </p>
</div>

<div class="section">
    <h2>📋 Order Activity History</h2>
    <div id="history">
        <p class="empty">No activities yet.</p>
    </div>
</div>

<div class="section">
    <h2>⚙️ System Controls</h2>

    <label for="capacityInput">Queue Capacity</label>
    <input type="number" id="capacityInput"
           value="5" min="1" max="20">

    <button class="add" onclick="changeCapacity()">
        Update Capacity
    </button>

    <button class="reset" onclick="resetSystem()">
        Reset System
    </button>
</div>

</div>

<footer>
    Restaurant Order Queue Management System<br>
    Based on Producer–Consumer Problem
</footer>

<script>

let capacity = 5;
let orders = [];
let completed = 0;
let total = 0;
let orderID = 1001;
let history = [];
let mutex = 1;

function log(message) {
    let time = new Date().toLocaleTimeString();
    history.unshift(time + " - " + message);

    document.getElementById("history").innerHTML =
        history.map(item =>
            `<div class="log">${item}</div>`
        ).join("");
}

function addOrder() {

    let name = document.getElementById("customer").value.trim();
    let food = document.getElementById("food").value;

    if (!name) {
        alert("Please enter customer name!");
        return;
    }

    // Producer: wait(empty)
    if (orders.length >= capacity) {
        document.getElementById("producerStatus").innerText =
            "Queue FULL! Cannot add more orders.";
        alert("Queue Overflow Prevented!");
        log("Producer blocked: Queue is full.");
        return;
    }

    if (mutex === 0) return;

    mutex = 0;

    let order = {
        id: orderID++,
        customer: name,
        food: food
    };

    orders.push(order);
    total++;

    mutex = 1;

    document.getElementById("customer").value = "";

    document.getElementById("producerStatus").innerText =
        "Order successfully added!";

    document.getElementById("kitchenStatus").innerText =
        "New order received by kitchen.";

    log("Producer added Order #" + order.id + " - " + food);

    updateUI();
}

function processOrder() {

    // Consumer: wait(full)
    if (orders.length === 0) {
        document.getElementById("kitchenStatus").innerText =
            "Queue EMPTY! No orders available.";
        alert("Queue Underflow Prevented!");
        log("Consumer blocked: Queue is empty.");
        return;
    }

    if (mutex === 0) return;

    mutex = 0;

    let order = orders.shift();
    completed++;

    mutex = 1;

    document.getElementById("kitchenStatus").innerText =
        "Completed Order #" + order.id +
        " for " + order.customer;

    log("Consumer prepared Order #" + order.id +
        " - " + order.food);

    updateUI();
}

function updateUI() {

    document.getElementById("total").innerText = total;
    document.getElementById("count").innerText =
        orders.length + " / " + capacity;

    document.getElementById("completed").innerText = completed;

    document.getElementById("available").innerText =
        capacity - orders.length;

    // Semaphore values
    document.getElementById("empty").innerText =
        capacity - orders.length;

    document.getElementById("full").innerText =
        orders.length;

    document.getElementById("mutex").innerText = mutex;

    document.getElementById("capacity").innerText = capacity;

    // Display fixed queue slots
    let html = "";

    for (let i = 0; i < capacity; i++) {

        if (orders[i]) {
            let o = orders[i];

            html += `
            <div class="slot filled">
                <div>
                    <strong>Order #${o.id}</strong><br>
                    ${o.customer}<br>
                    🍔 ${o.food}
                </div>
            </div>`;
        } else {
            html += `
            <div class="slot">
                Empty Slot ${i + 1}
            </div>`;
        }
    }

    document.getElementById("queue").innerHTML = html;
}

function changeCapacity() {

    let newCapacity = Number(
        document.getElementById("capacityInput").value
    );

    if (!Number.isInteger(newCapacity) ||
        newCapacity < 1 || newCapacity > 20) {
        alert("Enter capacity between 1 and 20.");
        return;
    }

    if (newCapacity < orders.length) {
        alert("Capacity cannot be smaller than current queue size!");
        return;
    }

    capacity = newCapacity;

    log("Queue capacity changed to " + capacity);

    updateUI();
}

function resetSystem() {

    if (!confirm("Are you sure you want to reset the system?"))
        return;

    orders = [];
    completed = 0;
    total = 0;
    orderID = 1001;
    history = [];
    mutex = 1;

    document.getElementById("history").innerHTML =
        '<p class="empty">No activities yet.</p>';

    document.getElementById("producerStatus").innerText =
        "Waiting for new orders...";

    document.getElementById("kitchenStatus").innerText =
        "Kitchen is waiting for orders.";

    log("System has been reset.");

    updateUI();
}

updateUI();

</script>

</body>
</html>
