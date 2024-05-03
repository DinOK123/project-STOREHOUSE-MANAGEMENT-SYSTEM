# project-STOREHOUSE-MANAGEMENT-SYSTEM
class Item:
    def __init__(self, name, description, price, quantity, category):
        self._name = name
        self._description = description
        self._price = price
        self._quantity = quantity
        self._category = category

    def __str__(self):
        return f"{self._name} - {self._description} - ${self._price} - Quantity: {self._quantity} - Category: {self._category}"

    # Getter methods
    def get_name(self):
        return self._name

    def get_description(self):
        return self._description

    def get_price(self):
        return self._price

    def get_quantity(self):
        return self._quantity

    def get_category(self):
        return self._category

    # Setter methods
    def set_name(self, name):
        self._name = name

    def set_description(self, description):
        self._description = description

    def set_price(self, price):
        self._price = price

    def set_quantity(self, quantity):
        self._quantity = quantity

    def set_category(self, category):
        self._category = category


class Order:
    def __init__(self, customer_name, order_date, item):
        self.customer_name = customer_name
        self.order_date = order_date
        self.item = item

    def __str__(self):
        return f"Order for: {self.customer_name}\nDate: {self.order_date}\nItem:\n{self.item}"


class Storehouse:
    def __init__(self):
        self.items = []
        self.orders = []  # list to store items

    def add_item(self, item):
        self.items.append(item)

    def find_item(self, name):
        for item in self.items:
            if item.get_name().lower() == name.lower():
                return item
        return None

    def update_item_quantity(self, name, quantity):
        item = self.find_item(name)
        if item:
            item.set_quantity(quantity)
        else:
            print(f"Item '{name}' not found in the storehouse.")

    def create_order(self, customer_name):
        while True:
            item_name = input("Enter item name to order: ")

            item = self.find_item(item_name)
            if not item:
                print(f"Item '{item_name}' is not available.")
                continue

            try:
                quantity = int(input(f"Enter quantity (available: {item.get_quantity()}): "))
                if quantity <= 0:
                    print("Please enter a valid quantity.")
                    continue

                if quantity > item.get_quantity():
                    print(f"Sorry, only {item.get_quantity()} units of '{item_name}' are available.")
                    continue

                # Update the quantity of the ordered item
                item.set_quantity(item.get_quantity() - quantity)

                order_item = Item(item.get_name(), item.get_description(), item.get_price(), quantity, item.get_category())
                order_date = input("Enter order date (YYYY-MM-DD): ")

                new_order = Order(customer_name, order_date, order_item)
                self.orders.append(new_order)  # add item to orders

                return new_order

            except ValueError:
                print("Invalid input. Please enter a valid quantity.")

    def get_orders_by_customer(self, customer_name):
        customer_orders = []
        for order in self.orders:
            if order.customer_name.lower() == customer_name.lower():
                customer_orders.append(order)
        return customer_orders


def print_items_table(items):
    # Define the column widths
    column_widths = {
        "Name": 20,
        "Description": 30,
        "Price ($)": 10,
        "Quantity": 10,
        "Category": 15
    }

    # Print column headers
    print(f"{'Name':<{column_widths['Name']}} {'Description':<{column_widths['Description']}} "
          f"{'Price ($)':>{column_widths['Price ($)']}} {'Quantity':>{column_widths['Quantity']}} "
          f"{'Category':<{column_widths['Category']}}")

    # Print separator line
    print("-" * sum(column_widths.values()))

    # Print information about each item
    for item in items:
        print(f"{item.get_name():<{column_widths['Name']}} {item.get_description():<{column_widths['Description']}} "
              f"${item.get_price():>{column_widths['Price ($)']}.2f} {item.get_quantity():>{column_widths['Quantity']}} "
              f"{item.get_category():<{column_widths['Category']}}")


if __name__ == "__main__":
    print("Welcome to the Store!")

    # create the store and add items
    storehouse = Storehouse()
    items = [
        Item("Laptop", "New Acer", 1050, 15, "Electronics"),
        Item("Phone", "iPhone 14 pro max", 700, 60, "Electronics"),
        Item("Bag", "Bags for laptops", 60, 20, "Accessories"),
        Item("Watch", "Luxury watch", 500, 10, "Accessories")
    ]
    for item in items:
        storehouse.add_item(item)

    # represent the information about items
    print("Items Information:")
    print_items_table(storehouse.items)
    print()  # an empty line for better readability

    customer_name = input("Enter your name: ")

    # main cycle of making an order
    while True:
        # creating an order
        order = storehouse.create_order(customer_name)
        if order:
            print("\nOrder Details:")
            print(order)
        else:
            print("No order was created.")

        # making sure if customer wants to make any other order
        answer = input("Do you want to place another order? (yes/no): ").lower()
        if answer != "yes":
            print("Order(s) completed. Thank you!")
            break

    # display a list of all orders of this customer
    customer_orders = storehouse.get_orders_by_customer(customer_name)
    if customer_orders:
        print(f"\nAll Orders by {customer_name}:")
        for idx, order in enumerate(customer_orders, 1):
            print(f"Order {idx}:")
            print(order)
    else:
        print(f"No orders found for {customer_name}.")

    #display the remaining items in store after all orders
    print("\nRemaining Items in Store:")
    print_items_table(storehouse.items)
