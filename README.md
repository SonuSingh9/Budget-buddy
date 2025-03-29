# 💰 Budget Buddy - React Native Expense Tracker  

A simple and easy-to-use **React Native** app for tracking your daily income and expenses. This app helps you manage your finances by categorizing transactions and calculating your total balance.  


## 🚀 Features  

✅ Add, Edit, and Delete Transactions
✅ Categorize Transactions as Income or Expense  
✅ Local Data Storage using AsyncStorage  
✅ Displays Total Balance  
✅ Simple and User-Friendly UI  



## 🛠️ Technologies Used  

- React Native - UI Development  
- React Navigation - Screen navigation  
- AsyncStorage - Local data storage  
- React Hooks (useState, useEffect) - State management  



## 📥 Installation & Setup  

### 🔹 1. Clone the Repository  


## 🚀 Project Structure :-

BudgetBuddy
│-- screens/
│   ├── HomeScreen.js      # Displays balance & transactions
│   ├── AddExpenseScreen.js # Form to add expenses
│-- App.js                 # Main app file with navigation
│-- package.json           # Dependencies & project metadata
│-- README.md              # Project documentation


📌 1. App.js (Main File)

import React from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createStackNavigator } from "@react-navigation/stack";
import HomeScreen from "./screens/HomeScreen";
import AddExpenseScreen from "./screens/AddExpenseScreen";

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator screenOptions={{ headerTitleAlign: "center" }}>
        <Stack.Screen name="Budget Buddy" component={HomeScreen} />
        <Stack.Screen name="Add Expense" component={AddExpenseScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default App;

📌 2. HomeScreen.js (Displaying Expenses & Balance) :-
import React, { useState, useEffect } from "react";
import { View, Text, FlatList, Button, StyleSheet } from "react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";

const HomeScreen = ({ navigation }) => {
  const [expenses, setExpenses] = useState([]);
  const [balance, setBalance] = useState(0);

  useEffect(() => {
    loadExpenses();
  }, []);

  const loadExpenses = async () => {
    const storedExpenses = await AsyncStorage.getItem("expenses");
    if (storedExpenses) {
      const parsedExpenses = JSON.parse(storedExpenses);
      setExpenses(parsedExpenses);
      calculateBalance(parsedExpenses);
    }
  };

  const calculateBalance = (data) => {
    let total = 0;
    data.forEach((item) => {
      total += item.type === "Income" ? item.amount : -item.amount;
    });
    setBalance(total);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.balance}>Balance: ₹{balance}</Text>
      <FlatList
        data={expenses}
        keyExtractor={(item, index) => index.toString()}
        renderItem={({ item }) => (
          <Text style={item.type === "Income" ? styles.income : styles.expense}>
            {item.type}: ₹{item.amount} - {item.description}
          </Text>
        )}
      />
      <Button title="Add Expense" onPress={() => navigation.navigate("Add Expense", { loadExpenses })} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, padding: 20 },
  balance: { fontSize: 24, fontWeight: "bold", textAlign: "center", marginBottom: 10 },
  income: { color: "green", fontSize: 18, marginVertical: 5 },
  expense: { color: "red", fontSize: 18, marginVertical: 5 },
});

export default HomeScreen;

📌 3. AddExpenseScreen.js (Adding Transactions) :-

import React, { useState } from "react";
import { View, Text, TextInput, Button, StyleSheet } from "react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";

const AddExpenseScreen = ({ navigation, route }) => {
  const [amount, setAmount] = useState("");
  const [description, setDescription] = useState("");
  const [type, setType] = useState("Expense");

  const saveExpense = async () => {
    if (!amount || !description) return alert("Please fill all fields!");

    const newExpense = { amount: parseFloat(amount), description, type };
    const storedExpenses = await AsyncStorage.getItem("expenses");
    const expenses = storedExpenses ? JSON.parse(storedExpenses) : [];
    
    expenses.push(newExpense);
    await AsyncStorage.setItem("expenses", JSON.stringify(expenses));

    route.params.loadExpenses(); // Reload expenses on HomeScreen
    navigation.goBack();
  };

  return (
    <View style={styles.container}>
      <Text style={styles.label}>Amount (₹):</Text>
      <TextInput style={styles.input} keyboardType="numeric" value={amount} onChangeText={setAmount} />
      
      <Text style={styles.label}>Description:</Text>
      <TextInput style={styles.input} value={description} onChangeText={setDescription} />

      <Button title="Toggle Type" onPress={() => setType(type === "Expense" ? "Income" : "Expense")} />
      <Text style={{ textAlign: "center", fontSize: 18, marginVertical: 10 }}>Type: {type}</Text>

      <Button title="Save Expense" onPress={saveExpense} />
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, padding: 20 },
  label: { fontSize: 18, marginVertical: 5 },
  input: { borderWidth: 1, padding: 10, marginBottom: 10, borderRadius: 5 },
});

export default AddExpenseScreen;
