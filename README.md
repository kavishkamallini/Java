# Java
import java.sql.*;
import java.util.Scanner;

class Tourist {
    protected String name;
    protected int age;
    protected String destination;

    public Tourist(String name, int age, String destination) {
        this.name = name;
        this.age = age;
        this.destination = destination;
    }

    public void displayDetails() {
        System.out.println("Name: " + name);
        System.out.println("Age: " + age);
        System.out.println("Destination: " + destination);
    }
}

class DomesticTourist extends Tourist {
    private String state;

    public DomesticTourist(String name, int age, String destination, String state) {
        super(name, age, destination);
        this.state = state;
    }

    @Override
    public void displayDetails() {
        super.displayDetails();
        System.out.println("State: " + state);
    }
}

class InternationalTourist extends Tourist {
    private String country;

    public InternationalTourist(String name, int age, String destination, String country) {
        super(name, age, destination);
        this.country = country;
    }

    @Override
    public void displayDetails() {
        super.displayDetails();
        System.out.println("Country: " + country);
    }
}

public class TouristManagementSystem {
    private static Connection connection;

    public static void main(String[] args) {
        try {
            // Connect to MySQL database
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/tourist_db", "root", "kavipriya08");
            System.out.println("Connected to the database successfully!");

            Scanner scanner = new Scanner(System.in);
            int choice;

            do {
                System.out.println("\n--- Tourist Management System ---");
                System.out.println("1. Add Domestic Tourist");
                System.out.println("2. Add International Tourist");
                System.out.println("3. Display All Tourists");
                System.out.println("4. Exit");
                System.out.print("Enter your choice: ");
                choice = scanner.nextInt();
                scanner.nextLine(); // consume newline

                switch (choice) {
                    case 1:
                        addDomesticTourist(scanner);
                        break;
                    case 2:
                        addInternationalTourist(scanner);
                        break;
                    case 3:
                        displayAllTourists();
                        break;
                    case 4:
                        System.out.println("Exiting...");
                        break;
                    default:
                        System.out.println("Invalid choice, please try again.");
                }
            } while (choice != 4);

            scanner.close();
            connection.close();
        } catch (SQLException e) {
            System.err.println("Database connection error: " + e.getMessage());
            e.printStackTrace();
        }
    }

    // Method to add a Domestic Tourist
    private static void addDomesticTourist(Scanner scanner) {
        try {
            System.out.print("Enter Name: ");
            String name = scanner.nextLine();
            System.out.print("Enter Age: ");
            int age = scanner.nextInt();
            scanner.nextLine(); // consume newline
            System.out.print("Enter Destination: ");
            String destination = scanner.nextLine();
            System.out.print("Enter State: ");
            String state = scanner.nextLine();

            String query = "INSERT INTO tourists (name, age, destination, type, extra) VALUES (?, ?, ?, 'Domestic', ?)";
            PreparedStatement preparedStatement = connection.prepareStatement(query);
            preparedStatement.setString(1, name);
            preparedStatement.setInt(2, age);
            preparedStatement.setString(3, destination);
            preparedStatement.setString(4, state);
            preparedStatement.executeUpdate();

            System.out.println("Domestic Tourist added successfully!");
        } catch (SQLException e) {
            System.err.println("Error adding domestic tourist: " + e.getMessage());
            e.printStackTrace();
        }
    }

    // Method to add an International Tourist
    private static void addInternationalTourist(Scanner scanner) {
        try {
            System.out.print("Enter Name: ");
            String name = scanner.nextLine();
            System.out.print("Enter Age: ");
            int age = scanner.nextInt();
            scanner.nextLine(); // consume newline
            System.out.print("Enter Destination: ");
            String destination = scanner.nextLine();
            System.out.print("Enter Country: ");
            String country = scanner.nextLine();

            String query = "INSERT INTO tourists (name, age, destination, type, extra) VALUES (?, ?, ?, 'International', ?)";
            PreparedStatement preparedStatement = connection.prepareStatement(query);
            preparedStatement.setString(1, name);
            preparedStatement.setInt(2, age);
            preparedStatement.setString(3, destination);
            preparedStatement.setString(4, country);
            preparedStatement.executeUpdate();

            System.out.println("International Tourist added successfully!");
        } catch (SQLException e) {
            System.err.println("Error adding international tourist: " + e.getMessage());
            e.printStackTrace();
        }
    }

    // Method to display all tourists
    private static void displayAllTourists() {
        try {
            String query = "SELECT * FROM tourists";
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery(query);

            if (!resultSet.isBeforeFirst()) {
                System.out.println("No tourists available.");
                return;
            }

            while (resultSet.next()) {
                System.out.println("Name: " + resultSet.getString("name"));
                System.out.println("Age: " + resultSet.getInt("age"));
                System.out.println("Destination: " + resultSet.getString("destination"));
                String type = resultSet.getString("type");
                if (type.equals("Domestic")) {
                    System.out.println("State: " + resultSet.getString("extra"));
                } else if (type.equals("International")) {
                    System.out.println("Country: " + resultSet.getString("extra"));
                }
                System.out.println("------------------------");
            }
        } catch (SQLException e) {
            System.err.println("Error retrieving tourists: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
