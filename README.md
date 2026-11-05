# Bank-Management-System
C++ BANK MANAGEMENT SYSTEM
#include <iostream>
#include <vector>
#include <string>
#include <memory>
#include <limits>
#include <iomanip>
using namespace std;

// Transaction Class
class Transaction {
public:
    string type;
    double amount;

    Transaction(string t, double a) {
        type = t;
        amount = a;
    }

    void display() const {
        cout << type << " : Rs. " << fixed
             << setprecision(2) << amount << endl;
    }
};

// Base Class - Account
class Account {
protected:
    int accountNumber;
    string customerName;
    double balance;
    vector<Transaction> transactions;

public:
    Account(int number, string name, double initialBalance) {
        accountNumber = number;
        customerName = name;
        balance = initialBalance;
    }

    virtual ~Account() = default;

    int getAccountNumber() const {
        return accountNumber;
    }

    virtual string getAccountType() const {
        return "General Account";
    }

    virtual bool withdraw(double amount) {
        if (amount <= 0 || amount > balance) {
            cout << "Invalid amount or insufficient balance!\n";
            return false;
        }

        balance -= amount;
        transactions.emplace_back("Withdrawal", amount);

        cout << "Withdrawal successful!\n";
        return true;
    }

    void deposit(double amount) {
        if (amount <= 0) {
            cout << "Enter a valid amount!\n";
            return;
        }

        balance += amount;
        transactions.emplace_back("Deposit", amount);

        cout << "Money deposited successfully!\n";
    }

    void displayDetails() const {
        cout << "\n---------- ACCOUNT DETAILS ----------\n";
        cout << "Account Number : " << accountNumber << endl;
        cout << "Customer Name  : " << customerName << endl;
        cout << "Account Type   : " << getAccountType() << endl;
        cout << "Balance        : Rs. "
             << fixed << setprecision(2) << balance << endl;
        cout << "-------------------------------------\n";
    }

    void showTransactions() const {
        cout << "\n---------- TRANSACTION HISTORY ----------\n";

        if (transactions.empty()) {
            cout << "No transactions available.\n";
            return;
        }

        for (const auto& t : transactions) {
            t.display();
        }
    }
};

// Derived Class - Savings Account
class SavingsAccount : public Account {
public:
    SavingsAccount(int number, string name, double amount)
        : Account(number, name, amount) {}

    string getAccountType() const override {
        return "Savings Account";
    }
};

// Derived Class - Current Account
class CurrentAccount : public Account {
public:
    CurrentAccount(int number, string name, double amount)
        : Account(number, name, amount) {}

    string getAccountType() const override {
        return "Current Account";
    }
};

// Bank Management Class
class Bank {
private:
    vector<unique_ptr<Account>> accounts;

    Account* findAccount(int number) {
        for (auto& account : accounts) {
            if (account->getAccountNumber() == number) {
                return account.get();
            }
        }

        return nullptr;
    }

public:
    void createAccount() {
        int number;
        string name;
        double amount;
        int type;

        cout << "\nEnter Account Number: ";
        cin >> number;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid account number!\n";
            return;
        }

        if (findAccount(number) != nullptr) {
            cout << "Account number already exists!\n";
            return;
        }

        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        cout << "Enter Customer Name: ";
        getline(cin, name);

        if (name.empty()) {
            cout << "Name cannot be empty!\n";
            return;
        }

        cout << "Enter Initial Deposit: Rs. ";
        cin >> amount;

        if (cin.fail() || amount < 0) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid deposit amount!\n";
            return;
        }

        cout << "\nSelect Account Type:\n";
        cout << "1. Savings Account\n";
        cout << "2. Current Account\n";
        cout << "Choice: ";
        cin >> type;

        if (cin.fail() || (type != 1 && type != 2)) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid account type!\n";
            return;
        }

        if (type == 1) {
            accounts.push_back(
                make_unique<SavingsAccount>(number, name, amount)
            );
        } else {
            accounts.push_back(
                make_unique<CurrentAccount>(number, name, amount)
            );
        }

        cout << "\nAccount created successfully!\n";
    }

    void depositMoney() {
        int number;
        double amount;

        cout << "Enter Account Number: ";
        cin >> number;

        Account* account = findAccount(number);

        if (account == nullptr) {
            cout << "Account not found!\n";
            return;
        }

        cout << "Enter Deposit Amount: Rs. ";
        cin >> amount;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid amount!\n";
            return;
        }

        account->deposit(amount);
    }

    void withdrawMoney() {
        int number;
        double amount;

        cout << "Enter Account Number: ";
        cin >> number;

        Account* account = findAccount(number);

        if (account == nullptr) {
            cout << "Account not found!\n";
            return;
        }

        cout << "Enter Withdrawal Amount: Rs. ";
        cin >> amount;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid amount!\n";
            return;
        }

        account->withdraw(amount);
    }

    void checkBalance() {
        int number;

        cout << "Enter Account Number: ";
        cin >> number;

        Account* account = findAccount(number);

        if (account != nullptr) {
            account->displayDetails();
        } else {
            cout << "Account not found!\n";
        }
    }

    void transactionHistory() {
        int number;

        cout << "Enter Account Number: ";
        cin >> number;

        Account* account = findAccount(number);

        if (account != nullptr) {
            account->showTransactions();
        } else {
            cout << "Account not found!\n";
        }
    }

    void displayAllAccounts() {
        if (accounts.empty()) {
            cout << "No accounts available!\n";
            return;
        }

        for (const auto& account : accounts) {
            account->displayDetails();
        }
    }
};

// Main Function
int main() {
    Bank bank;
    int choice;

    do {
        cout << "\n====================================\n";
        cout << "       BANK MANAGEMENT SYSTEM       \n";
        cout << "====================================\n";

        cout << "1. Create New Account\n";
        cout << "2. Deposit Money\n";
        cout << "3. Withdraw Money\n";
        cout << "4. Check Account Details\n";
        cout << "5. View Transaction History\n";
        cout << "6. Display All Accounts\n";
        cout << "7. Exit\n";

        cout << "====================================\n";
        cout << "Enter Your Choice: ";

        cin >> choice;

        if (cin.fail()) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid input! Enter a number.\n";
            continue;
        }

        switch (choice) {
            case 1:
                bank.createAccount();
                break;

            case 2:
                bank.depositMoney();
                break;

            case 3:
                bank.withdrawMoney();
                break;

            case 4:
                bank.checkBalance();
                break;

            case 5:
                bank.transactionHistory();
                break;

            case 6:
                bank.displayAllAccounts();
                break;

            case 7:
                cout << "\nThank you for using our Bank!\n";
                break;

            default:
                cout << "Invalid choice! Try again.\n";
        }

    } while (choice != 7);

    return 0;
}
