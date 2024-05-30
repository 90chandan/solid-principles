# solid-principles

**1. Single Responsibility Principle**
The SOLID Design Principles in C# help us solve most software design problems. These principles provide multiple ways to remove the tightly coupled code between the software components (between classes), making the software designs more understandable, flexible, and maintainable. 

1. S - stands for the Single Responsibility Principle. This Principle states that each software module or class should have only one reason to change. In other words, each module or class should have only one responsibility.
2. O - stands for the Open-Closed Principle. The Open-Closed Principle states that software entities, such as modules, classes, functions, etc., should be open for extension but closed for modification.
3. L - stands for the Liskov Substitution Principle. This Principle states that the object of a derived class should be able to replace an object of the base class without causing any errors in the system or modifying the behavior of the base class. That means the child class objects should be able to replace parent class objects without changing the correctness or behavior of the program.
4. I - stand for the Interface Segregation Principle. This Principle states that Clients should not be forced to implement any methods they don’t use. Rather than one fat interface, numerous little interfaces are preferred based on groups of methods, with each interface serving one submodule.
5. D stands for Dependency Inversion Principle. This Principle states that high-level modules/classes should not depend on low-level modules/classes. Both should depend upon abstractions. Secondly, abstractions should not depend upon details. Details should depend upon abstractions.


**========Single Responsibility Principle =================================**
The SRP states that a class should have only one reason to change, meaning it should have only one job or responsibility.

Real-Time Example of Single Responsibility Principle in C#: Banking System

A banking system allows customers to maintain a bank account where they can deposit and withdraw money. Additionally, the bank wants to provide a facility to print a statement of the account’s transaction history.

```
    public class BankAccount
    {
        public int AccountNumber { get; private set; }
        public double Balance { get; private set; }
        private List<string> Transactions = new List<string>();

        public BankAccount(int accountNumber)
        {
            AccountNumber = accountNumber;
        }

        public void Deposit(double amount)
        {
            Balance += amount;
            Transactions.Add($"Deposited ${amount}. New Balance: ${Balance}");
        }

        public void Withdraw(double amount)
        {
            Balance -= amount;
            Transactions.Add($"Withdrew ${amount}. New Balance: ${Balance}");
        }

        public void PrintStatement()
        {
            Console.WriteLine($"Statement for Account: {AccountNumber}");
            foreach (var transaction in Transactions)
            {
                Console.WriteLine(transaction);
            }
        }
    }

```
A cleaner approach would separate transaction management from statement printing: BankAccount manages the transactions of the account. StatementPrinter handles printing the transaction statement.

```
  public class BankAccount
    {
        public int AccountNumber { get; private set; }
        public double Balance { get; private set; }
        public List<string> Transactions = new List<string>();

        public BankAccount(int accountNumber)
        {
            AccountNumber = accountNumber;
        }

        public void Deposit(double amount)
        {
            Balance += amount;
            Transactions.Add($"Deposited ${amount}. New Balance: ${Balance}");
        }

        public void Withdraw(double amount)
        {
            Balance -= amount;
            Transactions.Add($"Withdrew ${amount}. New Balance: ${Balance}");
        }
    }

    public class StatementPrinter
    {
        public void Print(BankAccount account)
        {
            Console.WriteLine($"Statement for Account: {account.AccountNumber}");
            foreach (var transaction in account.Transactions)
            {
                Console.WriteLine(transaction);
            }
        }
    }
    
    //Testing the Single Responsibility Principle
    public class Program
    {
        public static void Main()
        {
            BankAccount johnsAccount = new BankAccount(123456);
            johnsAccount.Deposit(500);
            johnsAccount.Withdraw(100);

            StatementPrinter printer = new StatementPrinter();
            printer.Print(johnsAccount);
            
            Console.ReadKey();
        }
    }
    
```