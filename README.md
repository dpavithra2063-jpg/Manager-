# Manager-
import java.io.*;
import java.util.*;

public class InventoryManager {

    private static HashMap<Integer, Product> inventory = new HashMap<>();
    private static final String FILE_NAME = "inventory.txt";
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        loadFromFile();

        while (true) {
            System.out.println("\n1.Add Product\n2.View Products\n3.Update Product\n4.Delete Product\n5.Summary\n6.Exit");
            System.out.print("Choose option: ");
            int choice = getValidIntInput();

            switch (choice) {
                case 1 -> addProduct();
                case 2 -> viewProducts();
                case 3 -> updateProduct();
                case 4 -> deleteProduct();
                case 5 -> displaySummary();
                case 6 -> {
                    saveToFile();
                    System.out.println("Exiting...");
                    return;
                }
                default -> System.out.println("Invalid choice!");
            }
        }
    }

    private static void addProduct() {
        System.out.print("Enter ID: ");
        int id = getValidIntInput();

        if (inventory.containsKey(id)) {
            System.out.println("Product ID already exists!");
            return;
        }

        System.out.print("Enter Name: ");
        String name = scanner.next();

        System.out.print("Enter Quantity: ");
        int quantity = getValidIntInput();

        System.out.print("Enter Price: ");
        double price = scanner.nextDouble();

        inventory.put(id, new Product(id, name, quantity, price));
        System.out.println("Product added successfully.");
    }

    private static void viewProducts() {
        if (inventory.isEmpty()) {
            System.out.println("Inventory is empty.");
            return;
        }

        for (Product p : inventory.values()) {
            System.out.println("ID: " + p.getId() +
                    " Name: " + p.getName() +
                    " Qty: " + p.getQuantity() +
                    " Price: " + p.getPrice());
        }
    }

    private static void updateProduct() {
        System.out.print("Enter Product ID to update: ");
        int id = getValidIntInput();

        if (!inventory.containsKey(id)) {
            System.out.println("Product not found.");
            return;
        }

        Product p = inventory.get(id);

        System.out.print("Enter New Quantity: ");
        p.setQuantity(getValidIntInput());

        System.out.print("Enter New Price: ");
        p.setPrice(scanner.nextDouble());

        System.out.println("Product updated successfully.");
    }

    private static void deleteProduct() {
        System.out.print("Enter Product ID to delete: ");
        int id = getValidIntInput();

        if (inventory.remove(id) != null) {
            System.out.println("Product deleted successfully.");
        } else {
            System.out.println("Product not found.");
        }
    }

    private static void displaySummary() {
        int totalItems = 0;
        double totalValue = 0;

        for (Product p : inventory.values()) {
            totalItems += p.getQuantity();
            totalValue += p.getQuantity() * p.getPrice();
        }

        System.out.println("Total Products: " + inventory.size());
        System.out.println("Total Quantity: " + totalItems);
        System.out.println("Total Inventory Value: " + totalValue);
    }

    private static void saveToFile() {
        try (PrintWriter writer = new PrintWriter(new FileWriter(FILE_NAME))) {
            for (Product p : inventory.values()) {
                writer.println(p.toString());
            }
        } catch (IOException e) {
            System.out.println("Error saving file.");
        }
    }

    private static void loadFromFile() {
        File file = new File(FILE_NAME);
        if (!file.exists()) return;

        try (Scanner fileScanner = new Scanner(file)) {
            while (fileScanner.hasNextLine()) {
                String[] data = fileScanner.nextLine().split(",");
                int id = Integer.parseInt(data[0]);
                String name = data[1];
                int quantity = Integer.parseInt(data[2]);
                double price = Double.parseDouble(data[3]);

                inventory.put(id, new Product(id, name, quantity, price));
            }
        } catch (Exception e) {
            System.out.println("Error loading file.");
        }
    }

    private static int getValidIntInput() {
        while (!scanner.hasNextInt()) {
            System.out.print("Invalid input. Enter number: ");
            scanner.next();
        }
        return scanner.nextInt();
    }
}
