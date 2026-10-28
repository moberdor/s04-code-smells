# s04-code-smells

## Group Members
Marcus Oberdorfer

## Code Smells
1. Long Parameter List refactored with Introduce Parameter Object (p. 140)
2. Loops refactored with Replace Loop with Pipeline (p. 231)

## 1. Long Parameter List

### Intro

- A function that has too many parameters.
- Functions get bigger over time, and each new feature could add a new parameter
- Calls to the function are hard to read and cluttered
- It's easy to make mistakes in the order of parameters when passing values into the function call
- Changing the list of parameters requires you to fix every function call

### Prevention and Refcatoring

- **Introduce Parameter Object:** (p. 140)
  1. Make a class for all of the function parameter values
  2. Edit the function to take one object
  3. Update the function calls

### Examples

#### Python

Before:
```python
def ship(from_city, from_state, from_zip,
                 to_city, to_state, to_zip, weight):
    print(f"From: {from_city}, {from_state} {from_zip}")
    print(f"To:   {to_city}, {to_state} {to_zip}")
    print(f"Weight: {weight} lb")

ship("Santa Cruz", "CA", "95064", "Austin", "TX", "78701", 3)
```

After:
```python
class Address:
    def __init__(self, city, state, zip_code):
        self.city = city
        self.state = state
        self.zip_code = zip_code

    def __str__(self):
        return f"{self.city}, {self.state} {self.zip_code}"

def ship(origin, destination, weight):
    print(f"From: {origin}")
    print(f"To:   {destination}")
    print(f"Weight: {weight} lb")

santa_cruz = Address("Santa Cruz", "CA", "95064")
austin = Address("Austin", "TX", "78701")
ship(santa_cruz, austin, 3)
```

#### Java

Before:
```java
static void shipPackage(String fromCity, String fromState, String fromZip,
                        String toCity, String toState, String toZip, int weight) {
    System.out.println("From: " + fromCity + ", " + fromState + " " + fromZip);
    System.out.println("To:   " + toCity + ", " + toState + " " + toZip);
    System.out.println("Weight: " + weight + " lb");
}

shipPackage("Santa Cruz", "CA", "95064", "Austin", "TX", "78701", 3);
```

After:
```java
class Address {
    String city, state, zip;

    Address(String city, String state, String zip) {
        this.city = city;
        this.state = state;
        this.zip = zip;
    }

    public String toString() {
        return city + ", " + state + " " + zip;
    }
}

static void shipPackage(Address origin, Address destination, int weight) {
    System.out.println("From: " + origin);
    System.out.println("To:   " + destination);
    System.out.println("Weight: " + weight + " lb");
}

Address santaCruz = new Address("Santa Cruz", "CA", "95064");
Address austin = new Address("Austin", "TX", "78701");
shipPackage(santaCruz, austin, 3);
```

## 2. Loops

### Intro

- Using a for loop for a job that a simpler tool can do more clearly and efficiently
- Loops are the first tool that most of us learn so we use them instinctively (p. 231)
- Loops are hard to read, you have to go through the loop when analyzing its code to predict its output
- Variables that are changed by loop increments are easy to make mistakes with

### Prevention and Refactoring

- Before writing a loop, check if you are either filtering, transforming, or adding up items. Any of those tasks would be better as a pipeline.
- **Replace Loop with Pipeline:** (p. 231)
  - Replace the loop with a chain of events like (for example, filter -> map -> sum)
- Not every loop is bad. If the pipeline is more complex, use the loop

### Examples

#### Python

Before:

```python
orders = [
    {"amount": 25, "status": "completed"},
    {"amount": 40, "status": "cancelled"},
    {"amount": 15, "status": "completed"},
]

total = 0
for order in orders:
    if order["status"] == "completed":
        total += order["amount"]

print(total)
# 40
```

After:

```python
total = sum(order["amount"] for order in orders if order["status"] == "completed")

print(total)
# 40
```

#### Java

Before:
```java
int[] orderPrices = {25, 40, 15};
int bigOrdersTotal = 0;
for (int price : orderPrices) {
    if (price >= 20) {
        bigOrdersTotal += price;
    }
}
```

After:
```java
int bigOrdersTotal = Arrays.stream(orderPrices)
        .filter(price -> price >= 20)
        .sum();
```
