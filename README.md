# Day-04-Student-Management-System
"""
🎒 STUDENT HQ — A Gen Z Student Management System 🎒
------------------------------------------------------
Features:
1. ➕ Add student (name, class, age, roll number)
2. 📋 View all students in a clean table
3. 🔍 Search student by name or roll number
4. ✏️  Edit student details
5. 🏷️  Student Titles — everyone gets a fun Gen Z badge

Data is saved locally to `students_data.json` so nothing is lost
when you close the program.
"""

import json
import os
import random

DATA_FILE = "students_data.json"

# Fun Gen-Z style titles handed out randomly when a student is added.
# Feel free to add your own to this list.
GENZ_TITLES = [
    "Certified Vibe Curator ✨",
    "Chief Rizz Officer 🎤",
    "Professional Overthinker 🧠",
    "Group Project Carrier 💪",
    "Low-Key Genius 🤓",
    "Main Character Energy 🎬",
    "Snack Table Guardian 🍕",
    "Silent Assassin (Grades Edition) 🥷",
    "Meme Lord in Training 🐸",
    "No Cap Just Facts 📊",
    "The Group Chat MVP 💬",
    "Certified Nap Enthusiast 😴",
    "Front Row Energy 📚",
    "Back Row Legend 😎",
]


def load_data():
    """Load saved student list from the JSON file, or start fresh."""
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, "r") as f:
            return json.load(f)
    return {"students": []}


def save_data(data):
    """Write the current student list back to the JSON file."""
    with open(DATA_FILE, "w") as f:
        json.dump(data, f, indent=2)


def roll_number_exists(data, roll_number):
    """Check if a roll number is already taken."""
    return any(s["roll_number"] == roll_number for s in data["students"])


def add_student(data):
    """Ask for student details and save a new student record."""
    print("\n--- ➕ Add new student ---")
    name = input("Name: ").strip().title()

    while True:
        roll_number = input("Roll number: ").strip()
        if roll_number_exists(data, roll_number):
            print("⚠️  That roll number is already taken. Try another.")
        else:
            break

    student_class = input("Class (e.g. 10-A): ").strip().upper()

    while True:
        try:
            age = int(input("Age: "))
            break
        except ValueError:
            print("That's not a valid number, try again.")

    # Every student gets a random Gen Z title when they join
    title = random.choice(GENZ_TITLES)

    student = {
        "roll_number": roll_number,
        "name": name,
        "class": student_class,
        "age": age,
        "title": title,
    }
    data["students"].append(student)
    save_data(data)
    print(f"✅ {name} added to the roster! Their title: {title}")


def view_all_students(data):
    """Print every student in a clean, aligned table."""
    print("\n--- 📋 All Students ---")
    students = data["students"]

    if not students:
        print("No students yet. The roster is empty 👀")
        return

    # Column widths — fixed so everything lines up neatly
    print(f"{'Roll No':<10}{'Name':<18}{'Class':<10}{'Age':<6}{'Title':<30}")
    print("-" * 74)
    for s in students:
        print(
            f"{s['roll_number']:<10}{s['name']:<18}{s['class']:<10}"
            f"{s['age']:<6}{s['title']:<30}"
        )


def search_student(data):
    """Search students by name (partial match) or roll number."""
    print("\n--- 🔍 Search student ---")
    query = input("Enter name or roll number: ").strip().lower()

    results = [
        s for s in data["students"]
        if query in s["name"].lower() or query == s["roll_number"].lower()
    ]

    if not results:
        print("😶 No matches found.")
        return

    print(f"{'Roll No':<10}{'Name':<18}{'Class':<10}{'Age':<6}{'Title':<30}")
    print("-" * 74)
    for s in results:
        print(
            f"{s['roll_number']:<10}{s['name']:<18}{s['class']:<10}"
            f"{s['age']:<6}{s['title']:<30}"
        )


def find_student_by_roll(data, roll_number):
    """Helper: return the student dict matching a roll number, or None."""
    for s in data["students"]:
        if s["roll_number"] == roll_number:
            return s
    return None


def edit_student(data):
    """Find a student by roll number and update one of their fields."""
    print("\n--- ✏️  Edit student ---")
    roll_number = input("Enter roll number of the student to edit: ").strip()
    student = find_student_by_roll(data, roll_number)

    if student is None:
        print("😶 No student found with that roll number.")
        return

    print(f"Editing {student['name']} ({student['roll_number']})")
    print("What do you want to edit?")
    print("1. Name")
    print("2. Class")
    print("3. Age")
    print("4. Roll number")
    choice = input("Pick a number: ").strip()

    if choice == "1":
        student["name"] = input("New name: ").strip().title()
    elif choice == "2":
        student["class"] = input("New class: ").strip().upper()
    elif choice == "3":
        while True:
            try:
                student["age"] = int(input("New age: "))
                break
            except ValueError:
                print("That's not a valid number, try again.")
    elif choice == "4":
        new_roll = input("New roll number: ").strip()
        if roll_number_exists(data, new_roll):
            print("⚠️  That roll number is already taken.")
            return
        student["roll_number"] = new_roll
    else:
        print("Not a valid option.")
        return

    save_data(data)
    print("✅ Student updated!")


def student_titles(data):
    """View everyone's Gen Z title, or re-roll a specific student's title."""
    print("\n--- 🏷️  Student Titles ---")
    students = data["students"]

    if not students:
        print("No students yet. The roster is empty 👀")
        return

    for s in students:
        print(f"{s['name']:<18} → {s['title']}")

    reroll = input("\nRe-roll someone's title? Enter their roll number, or press Enter to skip: ").strip()
    if reroll == "":
        return

    student = find_student_by_roll(data, reroll)
    if student is None:
        print("😶 No student found with that roll number.")
        return

    student["title"] = random.choice(GENZ_TITLES)
    save_data(data)
    print(f"🎲 New title for {student['name']}: {student['title']}")


def main_menu():
    data = load_data()
    print("🎒 Welcome to STUDENT HQ 🎒")

    while True:
        print("\n============================")
        print("1. ➕ Add student")
        print("2. 📋 View all students")
        print("3. 🔍 Search student")
        print("4. ✏️  Edit student")
        print("5. 🏷️  Student titles")
        print("6. 🚪 Exit")
        choice = input("Choose an option (1-6): ").strip()

        if choice == "1":
            add_student(data)
        elif choice == "2":
            view_all_students(data)
        elif choice == "3":
            search_student(data)
        elif choice == "4":
            edit_student(data)
        elif choice == "5":
            student_titles(data)
        elif choice == "6":
            print("👋 Catch you later!")
            break
        else:
            print("Not a valid option, try 1-6.")


if __name__ == "__main__":
    main_menu()
