import csv
import os
from typing import List, Dict

# Constants
CONTACTS_FILE = "contacts.csv"
FIELDNAMES = ["Name", "Phone", "Email"]

def load_contacts() -> List[Dict[str, str]]:
    """Load contacts from CSV file. Returns empty list if file doesn't exist."""
    contacts = []
    if os.path.exists(CONTACTS_FILE):
        with open(CONTACTS_FILE, mode="r", newline="") as file:
            reader = csv.DictReader(file)
            contacts = list(reader)
    return contacts

def save_contacts(contacts: List[Dict[str, str]]) -> None:
    """Save contacts to CSV file."""
    with open(CONTACTS_FILE, mode="w", newline="") as file:
        writer = csv.DictWriter(file, fieldnames=FIELDNAMES)
        writer.writeheader()
        writer.writerows(contacts)

def display_contact(contact: Dict[str, str]) -> None:
    """Display a single contact in formatted way."""
    print(f"\nName: {contact['Name']}")
    print(f"Phone: {contact['Phone']}")
    print(f"Email: {contact['Email']}")
    print("-" * 30)

def add_contact() -> None:
    """Add a new contact to the address book."""
    print("\n--- Add New Contact ---")
    name = input("Enter name: ").strip()
    phone = input("Enter phone number: ").strip()
    email = input("Enter email: ").strip()
    
    contacts = load_contacts()
    contacts.append({
        "Name": name,
        "Phone": phone,
        "Email": email
    })
    save_contacts(contacts)
    print(f"\n✅ Contact '{name}' added successfully!")

def view_contacts() -> None:
    """Display all contacts in the address book."""
    contacts = load_contacts()
    print("\n--- All Contacts ---")
    
    if not contacts:
        print("No contacts found.")
        return
    
    for idx, contact in enumerate(contacts, 1):
        print(f"{idx}. {contact['Name']} | {contact['Phone']} | {contact['Email']}")

def search_contact() -> None:
    """Search for contacts by name."""
    contacts = load_contacts()
    print("\n--- Search Contacts ---")
    search_term = input("Enter name to search: ").strip().lower()
    
    found_contacts = [
        contact for contact in contacts 
        if search_term in contact["Name"].lower()
    ]
    
    if found_contacts:
        print(f"\nFound {len(found_contacts)} matching contacts:")
        for contact in found_contacts:
            display_contact(contact)
    else:
        print("\n❌ No matching contacts found.")

def update_contact() -> None:
    """Update an existing contact."""
    contacts = load_contacts()
    view_contacts()
    
    if not contacts:
        return
    
    try:
        choice = int(input("\nEnter contact number to update: ")) - 1
        if 0 <= choice < len(contacts):
            contact = contacts[choice]
            print(f"\nEditing: {contact['Name']}")
            
            # Get updated values (keep current if blank)
            new_name = input(f"New name [{contact['Name']}]: ").strip()
            new_phone = input(f"New phone [{contact['Phone']}]: ").strip()
            new_email = input(f"New email [{contact['Email']}]: ").strip()
            
            # Update only fields that were changed
            if new_name:
                contact["Name"] = new_name
            if new_phone:
                contact["Phone"] = new_phone
            if new_email:
                contact["Email"] = new_email
            
            save_contacts(contacts)
            print("\n✅ Contact updated successfully!")
        else:
            print("\n❌ Invalid contact number.")
    except ValueError:
        print("\n❌ Please enter a valid number.")

def delete_contact() -> None:
    """Delete a contact from the address book."""
    contacts = load_contacts()
    view_contacts()
    
    if not contacts:
        return
    
    try:
        choice = int(input("\nEnter contact number to delete: ")) - 1
        if 0 <= choice < len(contacts):
            deleted_name = contacts[choice]["Name"]
            del contacts[choice]
            save_contacts(contacts)
            print(f"\n✅ Contact '{deleted_name}' deleted successfully!")
        else:
            print("\n❌ Invalid contact number.")
    except ValueError:
        print("\n❌ Please enter a valid number.")

def export_contacts() -> None:
    """Export contacts to a CSV file."""
    contacts = load_contacts()
    if not contacts:
        print("\n❌ No contacts to export.")
        return
    
    export_file = input("Enter export filename (e.g., backup.csv): ").strip()
    if not export_file.endswith(".csv"):
        export_file += ".csv"
    
    try:
        with open(export_file, mode="w", newline="") as file:
            writer = csv.DictWriter(file, fieldnames=FIELDNAMES)
            writer.writeheader()
            writer.writerows(contacts)
        print(f"\n✅ Contacts exported to '{export_file}' successfully!")
    except Exception as e:
        print(f"\n❌ Error exporting contacts: {e}")

def main_menu() -> None:
    """Display the main menu and handle user choices."""
    # Create contacts file if it doesn't exist
    if not os.path.exists(CONTACTS_FILE):
        with open(CONTACTS_FILE, mode="w", newline="") as file:
            writer = csv.DictWriter(file, fieldnames=FIELDNAMES)
            writer.writeheader()
    
    while True:
        print("\n===== Contact Manager =====")
        print("1. Add Contact")
        print("2. View All Contacts")
        print("3. Search Contacts")
        print("4. Update Contact")
        print("5. Delete Contact")
        print("6. Export Contacts")
        print("7. Exit")
        
        choice = input("\nEnter your choice (1-7): ").strip()
        
        if choice == "1":
            add_contact()
        elif choice == "2":
            view_contacts()
        elif choice == "3":
            search_contact()
        elif choice == "4":
            update_contact()
        elif choice == "5":
            delete_contact()
        elif choice == "6":
            export_contacts()
        elif choice == "7":
            print("\n👋 Goodbye!")
            break
        else:
            print("\n❌ Invalid choice. Please enter a number between 1-7.")

if __name__ == "__main__":
    main_menu()
