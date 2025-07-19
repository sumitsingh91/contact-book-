# contact-book-
import tkinter as tk
from tkinter import messagebox, simpledialog

class ContactBookApp:
    def _init_(self, root):
        self.root = root
        self.root.title("Contact Book")
        self.root.geometry("600x400")
        self.root.configure(bg='lightblue')

        self.contacts = []

        # --- UI Layout ---
        # Input fields
        frame_input = tk.Frame(root, bg='lightblue')
        frame_input.pack(pady=10)

        tk.Label(frame_input, text="Name:", bg='lightblue').grid(row=0, column=0, sticky='e')
        self.name_var = tk.StringVar()
        tk.Entry(frame_input, textvariable=self.name_var, width=20).grid(row=0, column=1, padx=5)

        tk.Label(frame_input, text="Phone:", bg='lightblue').grid(row=0, column=2, sticky='e')
        self.phone_var = tk.StringVar()
        tk.Entry(frame_input, textvariable=self.phone_var, width=20).grid(row=0, column=3, padx=5)

        tk.Label(frame_input, text="Email:", bg='lightblue').grid(row=1, column=0, sticky='e')
        self.email_var = tk.StringVar()
        tk.Entry(frame_input, textvariable=self.email_var, width=20).grid(row=1, column=1, padx=5)

        tk.Label(frame_input, text="Address:", bg='lightblue').grid(row=1, column=2, sticky='e')
        self.address_var = tk.StringVar()
        tk.Entry(frame_input, textvariable=self.address_var, width=20).grid(row=1, column=3, padx=5)

        # Buttons
        frame_btns = tk.Frame(root, bg='lightblue')
        frame_btns.pack(pady=5)
        tk.Button(frame_btns, text="Add Contact", command=self.add_contact, width=15).grid(row=0, column=0, padx=5)
        tk.Button(frame_btns, text="Update Contact", command=self.update_contact, width=15).grid(row=0, column=1, padx=5)
        tk.Button(frame_btns, text="Delete Contact", command=self.delete_contact, width=15).grid(row=0, column=2, padx=5)

        # Search
        frame_search = tk.Frame(root, bg='lightblue')
        frame_search.pack(pady=5)
        tk.Label(frame_search, text="Search:", bg='lightblue').pack(side=tk.LEFT)
        self.search_var = tk.StringVar()
        tk.Entry(frame_search, textvariable=self.search_var, width=30).pack(side=tk.LEFT, padx=5)
        tk.Button(frame_search, text="Go", command=self.search_contact).pack(side=tk.LEFT, padx=5)
        tk.Button(frame_search, text="Show All", command=self.show_all_contacts).pack(side=tk.LEFT, padx=5)

        # Contact List
        frame_list = tk.Frame(root)
        frame_list.pack(pady=10, fill=tk.BOTH, expand=True)

        self.listbox = tk.Listbox(frame_list, width=60, height=10)
        self.listbox.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)
        self.listbox.bind('<<ListboxSelect>>', self.on_select)

        scrollbar = tk.Scrollbar(frame_list)
        scrollbar.pack(side=tk.RIGHT, fill=tk.BOTH)
        self.listbox.config(yscrollcommand=scrollbar.set)
        scrollbar.config(command=self.listbox.yview)

    # --- Contact Operations ---

    def add_contact(self):
        name = self.name_var.get().strip()
        phone = self.phone_var.get().strip()
        email = self.email_var.get().strip()
        address = self.address_var.get().strip()

        if not name or not phone:
            messagebox.showwarning("Input Error", "Name and Phone are required.")
            return

        # Check for duplicate phone
        for contact in self.contacts:
            if contact['phone'] == phone:
                messagebox.showwarning("Duplicate", "A contact with this phone already exists.")
                return

        self.contacts.append({
            'name': name,
            'phone': phone,
            'email': email,
            'address': address
        })
        self.clear_inputs()
        self.show_all_contacts()
        messagebox.showinfo("Success", "Contact added successfully.")

    def show_all_contacts(self):
        self.listbox.delete(0, tk.END)
        for contact in self.contacts:
            display = f"{contact['name']} - {contact['phone']}"
            self.listbox.insert(tk.END, display)

    def search_contact(self):
        query = self.search_var.get().strip().lower()
        self.listbox.delete(0, tk.END)
        for contact in self.contacts:
            if query in contact['name'].lower() or query in contact['phone']:
                display = f"{contact['name']} - {contact['phone']}"
                self.listbox.insert(tk.END, display)

    def on_select(self, event):
        if not self.listbox.curselection():
            return
        index = self.listbox.curselection()[0]
        display = self.listbox.get(index)
        name, phone = display.split(' - ')
        for contact in self.contacts:
            if contact['name'] == name and contact['phone'] == phone:
                self.name_var.set(contact['name'])
                self.phone_var.set(contact['phone'])
                self.email_var.set(contact['email'])
                self.address_var.set(contact['address'])
                break

    def update_contact(self):
        if not self.listbox.curselection():
            messagebox.showwarning("Select Contact", "Select a contact to update.")
            return
        index = self.listbox.curselection()[0]
        display = self.listbox.get(index)
        old_name, old_phone = display.split(' - ')

        for contact in self.contacts:
            if contact['name'] == old_name and contact['phone'] == old_phone:
                contact['name'] = self.name_var.get().strip()
                contact['phone'] = self.phone_var.get().strip()
                contact['email'] = self.email_var.get().strip()
                contact['address'] = self.address_var.get().strip()
                self.show_all_contacts()
                messagebox.showinfo("Updated", "Contact updated.")
                return

    def delete_contact(self):
        if not self.listbox.curselection():
            messagebox.showwarning("Select Contact", "Select a contact to delete.")
            return
        index = self.listbox.curselection()[0]
        display = self.listbox.get(index)
        name, phone = display.split(' - ')
        for i, contact in enumerate(self.contacts):
            if contact['name'] == name and contact['phone'] == phone:
                del self.contacts[i]
                self.clear_inputs()
                self.show_all_contacts()
                messagebox.showinfo("Deleted", "Contact deleted.")
                return

    def clear_inputs(self):
        self.name_var.set("")
        self.phone_var.set("")
        self.email_var.set("")
        self.address_var.set("")

if _name_ == "_main_":
    root = tk.Tk()
    app = ContactBookApp(root)
    root.mainloop()
