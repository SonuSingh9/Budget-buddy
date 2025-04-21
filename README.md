💰 Budget Buddy - Flutter Expense Tracker
A simple and easy-to-use Flutter app for tracking your daily income and expenses. This app helps you manage your money by categorizing transactions and showing your total balance clearly.


🚀 Features
✅ Add, Edit, and Delete Transactions
✅ Categorize transactions as Income or Expense
✅ Local Data Storage using shared_preferences
✅ Displays Total Balance
✅ Clean and User-Friendly UI 



🛠️ Technologies Used
Flutter – Cross-platform UI development
Dart – Programming language for Flutter
shared_preferences – Store data locally on the device
State Management – Using setState() for managing UI updates
Navigator – For screen navigation



📥 Installation & Setup
🔹 1. Clone the repository
🔹 2. Run flutter pub get to install dependencies
🔹 3. Use flutter run to launch the app


## 🚀 Project Structure :-

BudgetBuddy/
│-- lib/
│   ├── screens/
│   │   ├── home_screen.dart       # Displays balance & transaction list
│   │   └── add_expense_screen.dart # Form to add new expenses
│   └── main.dart                  # Main app file with routes
│-- pubspec.yaml                  # Project metadata and dependencies



📌 main.dart (Main File)

import 'package:flutter/material.dart';
import 'screens/home_screen.dart';
import 'screens/add_expense_screen.dart';

void main() => runApp(BudgetBuddyApp());

class BudgetBuddyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Budget Buddy',
      theme: ThemeData(primarySwatch: Colors.teal),
      home: HomeScreen(),
      routes: {
        '/add': (context) => AddExpenseScreen(),
      },
    );
  }
}


📌 home_screen.dart (Displaying Expenses & Balance) :-
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'dart:convert';

class HomeScreen extends StatefulWidget {
  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  List expenses = [];
  int balance = 0;

  @override
  void initState() {
    super.initState();
    loadExpenses();
  }

  void loadExpenses() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    String? data = prefs.getString('expenses');
    if (data != null) {
      List decoded = jsonDecode(data);
      setState(() {
        expenses = decoded;
        calculateBalance();
      });
    }
  }

  void calculateBalance() {
    int total = 0;
    for (var item in expenses) {
      total += item['type'] == 'Income' ? item['amount'] : -item['amount'];
    }
    setState(() => balance = total);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Budget Buddy')),
      body: Column(
        children: [
          Text('Balance: ₹$balance', style: TextStyle(fontSize: 24)),
          Expanded(
            child: ListView.builder(
              itemCount: expenses.length,
              itemBuilder: (context, index) {
                var item = expenses[index];
                return ListTile(
                  title: Text('${item['type']}: ₹${item['amount']}'),
                  subtitle: Text(item['description']),
                  textColor: item['type'] == 'Income' ? Colors.green : Colors.red,
                );
              },
            ),
          ),
          ElevatedButton(
            onPressed: () {
              Navigator.pushNamed(context, '/add').then((_) => loadExpenses());
            },
            child: Text('Add Expense'),
          ),
        ],
      ),
    );
  }
}


📌 add_expense_screen.dart :-

import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'dart:convert';

class AddExpenseScreen extends StatefulWidget {
  @override
  _AddExpenseScreenState createState() => _AddExpenseScreenState();
}

class _AddExpenseScreenState extends State<AddExpenseScreen> {
  final _amountController = TextEditingController();
  final _descriptionController = TextEditingController();
  String _type = 'Expense';

  void saveExpense() async {
    if (_amountController.text.isEmpty || _descriptionController.text.isEmpty) {
      ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('Please fill all fields')));
      return;
    }

    SharedPreferences prefs = await SharedPreferences.getInstance();
    String? data = prefs.getString('expenses');
    List expenses = data != null ? jsonDecode(data) : [];

    expenses.add({
      'amount': int.parse(_amountController.text),
      'description': _descriptionController.text,
      'type': _type,
    });

    prefs.setString('expenses', jsonEncode(expenses));
    Navigator.pop(context);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Add Expense')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            TextField(
              controller: _amountController,
              keyboardType: TextInputType.number,
              decoration: InputDecoration(labelText: 'Amount'),
            ),
            TextField(
              controller: _descriptionController,
              decoration: InputDecoration(labelText: 'Description'),
            ),
            DropdownButton<String>(
              value: _type,
              items: ['Income', 'Expense']
                  .map((val) => DropdownMenuItem(value: val, child: Text(val)))
                  .toList(),
              onChanged: (val) {
                setState(() {
                  _type = val!;
                });
              },
            ),
            ElevatedButton(
              onPressed: saveExpense,
              child: Text('Save'),
            ),
          ],
        ),
      ),
    );
  }
}

