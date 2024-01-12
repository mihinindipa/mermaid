import java.io.*;
import java.util.*;

// Product class
abstract class Product {
    private String productID;
    private String productName;
    private int availableItems;
    private double price;

    public Product(String productID, String productName, int availableItems, double price) {
        this.productID = productID;
        this.productName = productName;
        this.availableItems = availableItems;
        this.price = price;
    }

    // Getters and setters

    public String getProductID() {
        return productID;
    }

    public String getProductName() {
        return productName;
    }

    public int getAvailableItems() {
        return availableItems;
    }

    public void setAvailableItems(int availableItems) {
        this.availableItems = availableItems;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    // Abstract method
    public abstract String getType();
}

// Electronics class
class Electronics extends Product {
    private String brand;
    private int warrantyPeriod;

    public Electronics(String productID, String productName, int availableItems, double price, String brand, int warrantyPeriod) {
        super(productID, productName, availableItems, price);
        this.brand = brand;
        this.warrantyPeriod = warrantyPeriod;
    }

    // Getters and setters for Electronics class

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public int getWarrantyPeriod() {
        return warrantyPeriod;
    }

    public void setWarrantyPeriod(int warrantyPeriod) {
        this.warrantyPeriod = warrantyPeriod;
    }

    @Override
    public String getType() {
        return "Electronics";
    }
}

// Clothing class
class Clothing extends Product {
    private String size;
    private String color;

    public Clothing(String productID, String productName, int availableItems, double price, String size, String color) {
        super(productID, productName, availableItems, price);
        this.size = size;
        this.color = color;
    }

    // Getters and setters for Clothing class

    public String getSize() {
        return size;
    }

    public void setSize(String size) {
        this.size = size;
    }

    public String getColor() {
        return color;
    }

    public void setColor(String color) {
        this.color = color;
    }

    @Override
    public String getType() {
        return "Clothing";
    }
}

// User class
class User {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    // Getters and setters

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

// ShoppingCart class
class ShoppingCart {
    private List<Product> productList;

    public ShoppingCart() {
        this.productList = new ArrayList<>();
    }

    // Methods for adding, removing, and calculating total cost

    public void addProduct(Product product) {
        productList.add(product);
    }

    public void removeProduct(Product product) {
        productList.remove(product);
    }

    public double calculateTotalCost() {
        double totalCost = 0;
        for (Product product : productList) {
            totalCost += product.getPrice();
        }
        return totalCost;
    }

    public List<Product> getProductList() {
        return productList;
    }
}

// ShoppingManager interface
interface ShoppingManager {
    void addProduct(Product product);

    void deleteProduct(String productID);

    void printProductList();

    void saveToFile(String filename);

    void loadFromFile(String filename);
}

// WestminsterShoppingManager class
class WestminsterShoppingManager implements ShoppingManager {
    private List<Product> productList;

    public WestminsterShoppingManager() {
        this.productList = new ArrayList<>();
    }

    @Override
    public void addProduct(Product product) {
        productList.add(product);
    }

    @Override
    public void deleteProduct(String productID) {
        Product productToDelete = null;
        for (Product product : productList) {
            if (product.getProductID().equals(productID)) {
                productToDelete = product;
                break;
            }
        }

        if (productToDelete != null) {
            productList.remove(productToDelete);
            System.out.println("Product deleted: " + productToDelete.getType() + " - " + productToDelete.getProductName());
            System.out.println("Total number of products left: " + productList.size());
        } else {
            System.out.println("Product not found with ID: " + productID);
        }
    }

    @Override
    public void printProductList() {
        productList.sort(Comparator.comparing(Product::getProductID));
        for (Product product : productList) {
            System.out.println("Product ID: " + product.getProductID());
            System.out.println("Product Name: " + product.getProductName());
            System.out.println("Type: " + product.getType());
            System.out.println("Available Items: " + product.getAvailableItems());
            System.out.println("Price: $" + product.getPrice());

            if (product instanceof Electronics) {
                Electronics electronics = (Electronics) product;
                System.out.println("Brand: " + electronics.getBrand());
                System.out.println("Warranty Period: " + electronics.getWarrantyPeriod() + " months");
            } else if (product instanceof Clothing) {
                Clothing clothing = (Clothing) product;
                System.out.println("Size: " + clothing.getSize());
                System.out.println("Color: " + clothing.getColor());
            }

            System.out.println("---------------");
        }
    }

    @Override
    public void saveToFile(String filename) {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(filename))) {
            oos.writeObject(productList);
            System.out.println("Product list saved to file: " + filename);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void loadFromFile(String filename) {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(filename))) {
            productList = (List<Product>) ois.readObject();
            System.out.println("Product list loaded from file: " + filename);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}

// Main class
public class OnlineShoppingSystem {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        WestminsterShoppingManager shoppingManager = new WestminsterShoppingManager();

        while (true) {
            System.out.println("********** Online Shopping System **********");
            System.out.println("1. Add a new product");
            System.out.println("2. Delete a product");
            System.out.println("3. Print product list");
            System.out.println("4. Save product list to file");
            System.out.println("5. Load product list from file");
            System.out.println("6. Exit");

            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    addNewProduct(scanner, shoppingManager);
                    break;
                case 2:
                    deleteProduct(scanner, shoppingManager);
                    break;
                case 3:
                    shoppingManager.printProductList();
                    break;
                case 4:
                    System.out.print("Enter the filename to save: ");
                    String saveFilename = scanner.nextLine();
                    shoppingManager.saveToFile(saveFilename);
                    break;
                case 5:
                    System.out.print("Enter the filename to load: ");
                    String loadFilename = scanner.nextLine();
                    shoppingManager.loadFromFile(loadFilename);
                    break;
                case 6:
                    System.out.println("Exiting the system. Thank you!");
                    System.exit(0);
                default:
                    System.out.println("Invalid choice. Please enter a valid option.");
            }
        }
    }

    private static void addNewProduct(Scanner scanner, WestminsterShoppingManager shoppingManager) {
        System.out.print("Enter product type (Electronics/Clothing): ");
        String type = scanner.nextLine();

        System.out.print("Enter product ID: ");
        String productID = scanner.nextLine();

        System.out.print("Enter product name: ");
        String productName = scanner.nextLine();

        System.out.print("Enter available items: ");
        int availableItems = scanner.nextInt();

        System.out.print("Enter price: ");
        double price = scanner.nextDouble();
        scanner.nextLine(); // Consume the newline character

        if ("Electronics".equalsIgnoreCase(type)) {
            System.out.print("Enter brand: ");
            String brand = scanner.nextLine();

            System.out.print("Enter warranty period (in months): ");
            int warrantyPeriod = scanner.nextInt();

            Electronics electronics = new Electronics(productID, productName, availableItems, price, brand, warrantyPeriod);
            shoppingManager.addProduct(electronics);
        } else if ("Clothing".equalsIgnoreCase(type)) {
            System.out.print("Enter size: ");
            String size = scanner.nextLine();

            System.out.print("Enter color: ");
            String color = scanner.nextLine();

            Clothing clothing = new Clothing(productID, productName, availableItems, price, size, color);
            shoppingManager.addProduct(clothing);
        } else {
            System.out.println("Invalid product type. Please enter either Electronics or Clothing.");
        }
    }

    private static void deleteProduct(Scanner scanner, WestminsterShoppingManager shoppingManager) {
        System.out.print("Enter the product ID to delete: ");
        String productID = scanner.nextLine();
        shoppingManager.deleteProduct(productID);
    }
}
]
