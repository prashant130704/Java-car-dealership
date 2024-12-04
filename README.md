# Java-car-dealership

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.ArrayList;
import java.util.List;

class Car {
    private String make;
    private String model;
    private double price;
    private boolean sold;

    public Car(String make, String model, double price) {
        this.make = make;
        this.model = model;
        this.price = price;
        this.sold = false;
    }

    public String getDetails() {
        return make + " " + model + " - $" + price + (sold ? " (Sold)" : " (Available)");
    }

    public boolean isSold() {
        return sold;
    }

    public void sell() {
        this.sold = true;
    }

    public double getPrice() {
        return price;
    }
}

class Dealership {
    public List<Car> inventory;

    public Dealership() {
        this.inventory = new ArrayList<>();
    }

    public void addCar(Car car) {
        inventory.add(car);
    }

    public List<String> getCarDetails() {
        List<String> details = new ArrayList<>();
        for (Car car : inventory) {
            details.add(car.getDetails());
        }
        return details;
    }

    public boolean sellCar(int carIndex, double customerBudget) {
        if (carIndex < 0 || carIndex >= inventory.size()) {
            return false;
        }
        Car selectedCar = inventory.get(carIndex);
        if (selectedCar.isSold() || customerBudget < selectedCar.getPrice()) {
            return false;
        }
        selectedCar.sell();
        return true;
    }
}

public class CarDealershipGUI {
    private Dealership dealership;

    public CarDealershipGUI() {
        dealership = new Dealership();
        dealership.addCar(new Car("Toyota", "Camry", 24000));
        dealership.addCar(new Car("Honda", "Civic", 22000));
        dealership.addCar(new Car("Ford", "Mustang", 26000));

        createAndShowGUI();
    }

    private void createAndShowGUI() {
        JFrame frame = new JFrame("Car Dealership");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(600, 400);

        JPanel mainPanel = new JPanel();
        mainPanel.setLayout(new BorderLayout());

        // Customer Info Panel
        JPanel customerPanel = new JPanel(new GridLayout(2, 2));
        customerPanel.add(new JLabel("Customer Name:"));
        JTextField nameField = new JTextField();
        customerPanel.add(nameField);
        customerPanel.add(new JLabel("Customer Budget:"));
        JTextField budgetField = new JTextField();
        customerPanel.add(budgetField);

        mainPanel.add(customerPanel, BorderLayout.NORTH);

        // Car List Panel
        DefaultListModel<String> carListModel = new DefaultListModel<>();
        for (String details : dealership.getCarDetails()) {
            carListModel.addElement(details);
        }
        JList<String> carList = new JList<>(carListModel);
        mainPanel.add(new JScrollPane(carList), BorderLayout.CENTER);

        // Action Panel
        JPanel actionPanel = new JPanel();
        JButton buyButton = new JButton("Buy Selected Car");
        JLabel statusLabel = new JLabel(" ");
        actionPanel.add(buyButton);
        actionPanel.add(statusLabel);
        mainPanel.add(actionPanel, BorderLayout.SOUTH);

        frame.add(mainPanel);

        // Button Action Listener
        buyButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                try {
                    String name = nameField.getText();
                    double budget = Double.parseDouble(budgetField.getText());
                    int selectedIndex = carList.getSelectedIndex();

                    if (selectedIndex == -1) {
                        statusLabel.setText("Please select a car.");
                    } else if (dealership.sellCar(selectedIndex, budget)) {
                        carListModel.set(selectedIndex, dealership.inventory.get(selectedIndex).getDetails());
                        statusLabel.setText(name + " purchased the car!");
                    } else {
                        statusLabel.setText("Purchase failed. Check budget or car availability.");
                    }
                } catch (NumberFormatException ex) {
                    statusLabel.setText("Invalid budget input.");
                }
            }
        });

        frame.setVisible(true);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(CarDealershipGUI::new);
    }
}
