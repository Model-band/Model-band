- 👋 Hi, I’m @Model-band
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Model-band/Model-band is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Business Tracker</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 0; padding: 20px; }
        .container { max-width: 1200px; margin: 0 auto; }
        .tabs { display: flex; margin-bottom: 20px; }
        .tab { padding: 10px 20px; cursor: pointer; background: #eee; margin-right: 5px; }
        .tab.active { background: #4CAF50; color: white; }
        .tab-content { display: none; }
        .tab-content.active { display: block; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
        th { background-color: #f2f2f2; }
        .form-group { margin-bottom: 15px; }
        input, select { padding: 8px; width: 100%; box-sizing: border-box; }
        button { background: #4CAF50; color: white; border: none; padding: 10px 15px; cursor: pointer; }
        button:hover { background: #45a049; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Business Notes & Payment Tracker</h1>
        
        <div class="tabs">
            <div class="tab active" onclick="openTab('customers')">Customers</div>
            <div class="tab" onclick="openTab('transactions')">Transactions</div>
            <div class="tab" onclick="openTab('notes')">Notes</div>
            <div class="tab" onclick="openTab('reports')">Reports</div>
        </div>
        
        <div id="customers" class="tab-content active">
            <h2>Customer Management</h2>
            <div class="form-group">
                <input type="text" id="customerName" placeholder="Customer Name">
            </div>
            <div class="form-group">
                <input type="text" id="customerContact" placeholder="Contact Information">
            </div>
            <button onclick="addCustomer()">Add Customer</button>
            
            <table id="customerTable">
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Name</th>
                        <th>Contact</th>
                        <th>Balance</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody></tbody>
            </table>
        </div>
        
        <div id="transactions" class="tab-content">
            <h2>Transaction Management</h2>
            <div class="form-group">
                <select id="transactionCustomer">
                    <option value="">Select Customer</option>
                </select>
            </div>
            <div class="form-group">
                <input type="number" id="transactionAmount" placeholder="Amount">
            </div>
            <div class="form-group">
                <select id="transactionType">
                    <option value="purchase">Purchase</option>
                    <option value="payment">Payment</option>
                </select>
            </div>
            <div class="form-group">
                <input type="date" id="transactionDate">
            </div>
            <div class="form-group">
                <input type="text" id="transactionNotes" placeholder="Notes">
            </div>
            <button onclick="addTransaction()">Add Transaction</button>
            
            <table id="transactionTable">
                <thead>
                    <tr>
                        <th>Date</th>
                        <th>Customer</th>
                        <th>Type</th>
                        <th>Amount</th>
                        <th>Notes</th>
                    </tr>
                </thead>
                <tbody></tbody>
            </table>
        </div>
        
        <div id="notes" class="tab-content">
            <h2>Business Notes</h2>
            <div class="form-group">
                <select id="noteCustomer">
                    <option value="">Select Customer (optional)</option>
                </select>
            </div>
            <div class="form-group">
                <textarea id="noteContent" rows="4" style="width:100%" placeholder="Enter your note here..."></textarea>
            </div>
            <button onclick="addNote()">Add Note</button>
            
            <div id="notesList" style="margin-top:20px"></div>
        </div>
        
        <div id="reports" class="tab-content">
            <h2>Reports</h2>
            <div>
                <h3>Outstanding Balances</h3>
                <table id="balanceTable">
                    <thead>
                        <tr>
                            <th>Customer</th>
                            <th>Balance</th>
                        </tr>
                    </thead>
                    <tbody></tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        // Data storage
        let customers = JSON.parse(localStorage.getItem('customers')) || [];
        let transactions = JSON.parse(localStorage.getItem('transactions')) || [];
        let notes = JSON.parse(localStorage.getItem('notes')) || [];
        
        // Initialize the app
        function init() {
            renderCustomers();
            renderTransactions();
            renderNotes();
            updateCustomerSelects();
            generateReports();
        }
        
        // Tab navigation
        function openTab(tabName) {
            const tabs = document.querySelectorAll('.tab-content');
            tabs.forEach(tab => tab.classList.remove('active'));
            
            const tabButtons = document.querySelectorAll('.tab');
            tabButtons.forEach(button => button.classList.remove('active'));
            
            document.getElementById(tabName).classList.add('active');
            event.currentTarget.classList.add('active');
        }
        
        // Customer functions
        function addCustomer() {
            const name = document.getElementById('customerName').value;
            const contact = document.getElementById('customerContact').value;
            
            if (!name) {
                alert('Please enter a customer name');
                return;
            }
            
            const customer = {
                id: Date.now(),
                name,
                contact,
                balance: 0
            };
            
            customers.push(customer);
            saveData();
            renderCustomers();
            updateCustomerSelects();
            
            document.getElementById('customerName').value = '';
            document.getElementById('customerContact').value = '';
        }
        
        function renderCustomers() {
            const tbody = document.querySelector('#customerTable tbody');
            tbody.innerHTML = '';
            
            customers.forEach(customer => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${customer.id}</td>
                    <td>${customer.name}</td>
                    <td>${customer.contact}</td>
                    <td>$${customer.balance.toFixed(2)}</td>
                    <td>
                        <button onclick="deleteCustomer(${customer.id})">Delete</button>
                    </td>
                `;
                tbody.appendChild(row);
            });
        }
        
        function deleteCustomer(id) {
            if (confirm('Are you sure you want to delete this customer?')) {
                customers = customers.filter(c => c.id !== id);
                transactions = transactions.filter(t => t.customerId !== id);
                notes = notes.filter(n => n.customerId !== id);
                saveData();
                renderCustomers();
                renderTransactions();
                renderNotes();
                updateCustomerSelects();
                generateReports();
            }
        }
        
        // Transaction functions
        function addTransaction() {
            const customerId = parseInt(document.getElementById('transactionCustomer').value);
            const amount = parseFloat(document.getElementById('transactionAmount').value);
            const type = document.getElementById('transactionType').value;
            const date = document.getElementById('transactionDate').value;
            const notes = document.getElementById('transactionNotes').value;
            
            if (!customerId || isNaN(amount) || !date) {
                alert('Please fill all required fields');
                return;
            }
            
            const transaction = {
                id: Date.now(),
                customerId,
                amount,
                type,
                date,
                notes
            };
            
            transactions.push(transaction);
            
            // Update customer balance
            const customer = customers.find(c => c.id === customerId);
            if (customer) {
                if (type === 'purchase') {
                    customer.balance += amount;
                } else {
                    customer.balance -= amount;
                }
            }
            
            saveData();
            renderTransactions();
            renderCustomers();
            generateReports();
            
            // Clear form
            document.getElementById('transactionAmount').value = '';
            document.getElementById('transactionDate').value = '';
            document.getElementById('transactionNotes').value = '';
        }
        
        function renderTransactions() {
            const tbody = document.querySelector('#transactionTable tbody');
            tbody.innerHTML = '';
            
            transactions.forEach(transaction => {
                const customer = customers.find(c => c.id === transaction.customerId);
                const customerName = customer ? customer.name : 'Unknown';
                
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${transaction.date}</td>
                    <td>${customerName}</td>
                    <td>${transaction.type}</td>
                    <td>$${transaction.amount.toFixed(2)}</td>
                    <td>${transaction.notes}</td>
                `;
                tbody.appendChild(row);
            });
        }
        
        // Note functions
        function addNote() {
            const customerId = document.getElementById('noteCustomer').value ?
                parseInt(document.getElementById('noteCustomer').value) : null;
            const content = document.getElementById('noteContent').value;
            
            if (!content) {
                alert('Please enter note content');
                return;
            }
            
            const note = {
                id: Date.now(),
                customerId,
                content,
                date: new Date().toISOString().split('T')[0]
            };
            
            notes.push(note);
            saveData();
            renderNotes();
            
            document.getElementById('noteContent').value = '';
        }
        
        function renderNotes() {
            const container = document.getElementById('notesList');
            container.innerHTML = '';
            
            notes.forEach(note => {
                const customer = note.customerId ? 
                    customers.find(c => c.id === note.customerId) : null;
                
                const noteDiv = document.createElement('div');
                noteDiv.style.border = '1px solid #ddd';
                noteDiv.style.padding = '10px';
                noteDiv.style.marginBottom = '10px';
                
                noteDiv.innerHTML = `
                    <p><strong>${note.date}</strong> ${customer ? `(Customer: ${customer.name})` : ''}</p>
                    <p>${note.content}</p>
                    <button onclick="deleteNote(${note.id})">Delete</button>
                `;
                
                container.appendChild(noteDiv);
            });
        }
        
        function deleteNote(id) {
            if (confirm('Are you sure you want to delete this note?')) {
                notes = notes.filter(n => n.id !== id);
                saveData();
                renderNotes();
            }
        }
        
        // Report functions
        function generateReports() {
            const tbody = document.querySelector('#balanceTable tbody');
            tbody.innerHTML = '';
            
            customers.forEach(customer => {
                if (customer.balance !== 0) {
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td>${customer.name}</td>
                        <td>$${customer.balance.toFixed(2)}</td>
                    `;
                    tbody.appendChild(row);
                }
            });
        }
        
        // Helper functions
        function updateCustomerSelects() {
            const selects = document.querySelectorAll('select[id$="Customer"]');
            
            selects.forEach(select => {
                select.innerHTML = '<option value="">' + 
                    (select.id === 'noteCustomer' ? 'Select Customer (optional)' : 'Select Customer') + 
                    '</option>';
                
                customers.forEach(customer => {
                    const option = document.createElement('option');
                    option.value = customer.id;
                    option.textContent = customer.name;
                    select.appendChild(option);
                });
            });
        }
        
        function saveData() {
            localStorage.setItem('customers', JSON.stringify(customers));
            localStorage.setItem('transactions', JSON.stringify(transactions));
            localStorage.setItem('notes', JSON.stringify(notes));
        }
        
        // Initialize the app when loaded
        window.onload = init;
    </script>
</body>
</html>
