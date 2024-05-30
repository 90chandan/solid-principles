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


**===== Open Closed Principle==================================**

This principle promotes the idea that you should be able to add new functionality to a component without altering its existing code.

The Open for Extension means we need to design the software modules/classes/functions in a way that the new responsibilities or functionalities can be added easily when new requirements come. On the other hand, Closed for Modification means we should not modify the class/module/function until we find some bugs. 

That means we should not edit the code of a method (until we find some bugs); instead, we should use polymorphism or other techniques to add new functionality by writing new code.


1. The easiest way to implement the Open-Closed Principle in C# is to add new functionalities by creating new derived classes, which should be inherited from the original base class.
2. Another way is to allow the client to access the original class with an abstract interface.

Let’s take an example of a notification system where a user can be notified through various channels.

Without OCP:

```
using System;
namespace OCPDemo
{
    public enum NotificationChannel
    {
        Email,
        SMS
    }

    public class NotificationSender
    {
        public void SendNotification(NotificationChannel channel, string message)
        {
            switch (channel)
            {
                case NotificationChannel.Email:
                    Console.WriteLine($"Sending Email: {message}");
                    break;
                case NotificationChannel.SMS:
                    Console.WriteLine($"Sending SMS: {message}");
                    break;
                default:
                    throw new ArgumentOutOfRangeException();
            }
        }
    }
}

```

With OCP

```
using System;
namespace OCPDemo
{
    //Start with an interface
    public interface INotificationChannel
    {
        void Send(string message);
    }

    //Implement this interface for each channel
    public class EmailNotification : INotificationChannel
    {
        public void Send(string message)
        {
            Console.WriteLine($"Sending Email: {message}");
        }
    }

    public class SMSNotification : INotificationChannel
    {
        public void Send(string message)
        {
            Console.WriteLine($"Sending SMS: {message}");
        }
    }

    //Modify the NotificationSender class to accept any INotificationChannel
    public class NotificationSender
    {
        private readonly INotificationChannel _channel;

        public NotificationSender(INotificationChannel channel)
        {
            _channel = channel;
        }

        public void SendNotification(string message)
        {
            _channel.Send(message);
        }
    }
    
    //Testing the Open-Closed Principle
    public class Program
    {
        public static void Main()
        {
            var emailChannel = new EmailNotification();
            var sender = new NotificationSender(emailChannel);
            sender.SendNotification("Hello via Email!");

            var smsChannel = new SMSNotification();
            sender = new NotificationSender(smsChannel);
            sender.SendNotification("Hello via SMS!");

            Console.ReadKey();
        }
    }
}

```

**=============================Liskov Substitution Principle ================================================**

It states that objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program. That means a subclass should completely adhere to the behavior expected by the superclass. The Liskov Substitution Principle encourages a design where subclasses are substitutable for their base classes.

In simple words, we can say that when we have Inheritance Relationships between two classes, then if we successfully replace the object/instance of a parent class with an object/instance of the child class without affecting the behavior of the base class instance, it is said to be in Liskov Substitution Principle.

**Real-World Example without Liskov Substitution Principle**

Here’s an example in C# that violates the LSP. Consider a scenario with a base class Bird and a derived class Penguin

```
public class Bird
{
    public virtual void Fly()
    {
        Console.WriteLine("Flying");
    }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new NotImplementedException("Penguins can't fly!");
    }
}

public class BirdHandler
{
    public void MakeBirdFly(Bird bird)
    {
        bird.Fly();
    }
}

```
In this example, a Penguin is a Bird, but Penguin cannot fly. When MakeBirdFly is called with a Penguin instance, it will throw an exception, violating the Liskov Substitution Principle.

**Real-World Example with Liskov Substitution Principle**

To adhere to the Liskov Substitution Principle, we can refactor the code so that Penguin is not forced to implement Fly. We introduce a new interface IFlyable for birds that can fly.

```
public interface IFlyable
{
    void Fly();
}

public class Bird
{
    // Common properties and methods for all birds
}

public class Sparrow : Bird, IFlyable
{
    public void Fly()
    {
        Console.WriteLine("Flying");
    }
}

public class Penguin : Bird
{
    // Penguins can't fly, so no Fly method here
}

public class BirdHandler
{
    public void MakeBirdFly(IFlyable bird)
    {
        bird.Fly();
    }
}


```

- **Without LSP:** Penguin inherits Fly method from Bird, but overrides it to throw an exception, which violates LSP because a Penguin cannot be used as a Bird in a context expecting a flying bird.
- **With LSP:** Penguin does not implement the Fly method. Instead, we use an interface IFlyable for birds that can fly. This way, MakeBirdFly only deals with objects that can actually fly, ensuring that any subclass can be substituted without breaking the program.


**========================= Interface Segregation Principle =================================**

This Principle states Clients should not be forced to implement any methods they don’t use. Rather than one fat interface, numerous little interfaces are preferred based on groups of methods, with each interface serving one submodule.

Let us break down the above definition into two parts.

1. First, no class should be forced to implement any method(s) of an interface they don’t use.
2. Secondly, instead of creating large or, you can say, fat interfaces, create multiple smaller interfaces so that the clients only think about the methods that they want to implement.

**Without ISP**

```
    public interface IPrinterTasks
    {
        void Print(string PrintContent);
        void Scan(string ScanContent);
        void Fax(string FaxContent);
        void PrintDuplex(string PrintDuplexContent);
    }

    public class HPLaserJetPrinter : IPrinterTasks
    {
        public void Print(string PrintContent)
        {
            Console.WriteLine("Print Done");
        }

        public void Scan(string ScanContent)
        {
            Console.WriteLine("Scan content");
        }

        public void Fax(string FaxContent)
        {
            Console.WriteLine("Fax content");
        }

        public void PrintDuplex(string PrintDuplexContent)
        {
            Console.WriteLine("Print Duplex content");
        }
    }

    class LiquidInkjetPrinter : IPrinterTasks
    {
        public void Print(string PrintContent)
        {
            Console.WriteLine("Print Done");
        }

        public void Scan(string ScanContent)
        {
            Console.WriteLine("Scan content");
        }

        public void Fax(string FaxContent)
        {
            throw new NotImplementedException();
        }

        public void PrintDuplex(string PrintDuplexContent)
        {
            throw new NotImplementedException();
        }
    }

```

**With ISP**


```
    public interface IPrinterTasks
    {
        void Print(string PrintContent);
        void Scan(string ScanContent);
    }

    interface IFaxTasks
    {
        void Fax(string content);
    }

    interface IPrintDuplexTasks
    {
        void PrintDuplex(string content);
    }


    public class HPLaserJetPrinter : IPrinterTasks, IFaxTasks, IPrintDuplexTasks
    {
        public void Print(string PrintContent)
        {
            Console.WriteLine("Print Done");
        }
        public void Scan(string ScanContent)
        {
            Console.WriteLine("Scan content");
        }
        public void Fax(string FaxContent)
        {
            Console.WriteLine("Fax content");
        }
        public void PrintDuplex(string PrintDuplexContent)
        {
            Console.WriteLine("Print Duplex content");
        }
    }


    class LiquidInkjetPrinter : IPrinterTasks
    {
        public void Print(string PrintContent)
        {
            Console.WriteLine("Print Done");
        }
        public void Scan(string ScanContent)
        {
            Console.WriteLine("Scan content");
        }
    }


    public class Program
    {
        static void Main(string[] args)
        {
            //Using HPLaserJetPrinter we can access all Printer Services
            HPLaserJetPrinter hPLaserJetPrinter = new HPLaserJetPrinter();
            hPLaserJetPrinter.Print("Printing");
            hPLaserJetPrinter.Scan("Scanning");
            hPLaserJetPrinter.Fax("Faxing");
            hPLaserJetPrinter.PrintDuplex("PrintDuplex");

            //Using LiquidInkjetPrinter we can only Access Print and Scan Printer Services
            LiquidInkjetPrinter liquidInkjetPrinter = new LiquidInkjetPrinter();
            liquidInkjetPrinter.Print("Printing");
            liquidInkjetPrinter.Scan("Scanning");

            Console.ReadKey();
        }
    }

    
```