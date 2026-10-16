# Password--Strength-Checker
A Python-based cybersecurity tool that evaluates password strength, detects common weaknesses, provides security recommendations, and generates secure random passwords.

import string
import secrets
import getpass

common_passwords = [
    "password", "password123", "12345678",
    "qwerty123", "admin123", "letmein"
]


def check_password(password):
    score = 0
    suggestions = []

    if len(password) >= 12:
        score += 30
    elif len(password) >= 8:
        score += 15
        suggestions.append("Use at least 12 characters")
    else:
        suggestions.append("Password is too short")

    if any(c.islower() for c in password):
        score += 15
    else:
        suggestions.append("Add lowercase letters")

    if any(c.isupper() for c in password):
        score += 15
    else:
        suggestions.append("Add uppercase letters")

    if any(c.isdigit() for c in password):
        score += 15
    else:
        suggestions.append("Add numbers")

    if any(c in string.punctuation for c in password):
        score += 15
    else:
        suggestions.append("Add special symbols")

    if len(password) >= 16:
        score += 10

    if password.lower() in common_passwords:
        score = 0
        suggestions.append("Avoid common passwords")

    for i in range(len(password) - 2):
        if password[i] == password[i+1] == password[i+2]:
            score -= 15
            suggestions.append("Avoid repeated characters")
            break

    sequences = ["1234", "abcd", "qwerty", "4321"]

    for seq in sequences:
        if seq in password.lower():
            score -= 20
            suggestions.append("Avoid predictable sequences")
            break

    score = max(0, min(score, 100))

    if score >= 80:
        level = "STRONG"
    elif score >= 50:
        level = "MODERATE"
    else:
        level = "WEAK"

    return score, level, suggestions


def generate_password(length=16):
    characters = (
        string.ascii_letters +
        string.digits +
        string.punctuation
    )

    while True:
        password = "".join(
            secrets.choice(characters)
            for i in range(length)
        )

        score, level, suggestions = check_password(password)

        if level == "STRONG":
            return password


def main():
    while True:
        print("\n==============================")
        print(" PASSWORD STRENGTH CHECKER 2.0")
        print("==============================")
        print("1. Check password strength")
        print("2. Generate secure password")
        print("3. Exit")

        choice = input("Enter your choice: ")

        if choice == "1":
            password = getpass.getpass(
                "Enter password: "
            )

            score, level, suggestions = check_password(password)

            print("\n----- SECURITY REPORT -----")
            print("Strength Score:", score, "/100")
            print("Strength Level:", level)

            if suggestions:
                print("\nSuggestions:")
                for item in suggestions:
                    print("-", item)
            else:
                print("No basic weaknesses detected")

            print("Scores are estimates, not guarantees.")

        elif choice == "2":
            password = generate_password()
            print("\nGenerated Password:", password)
            print("Keep your password private.")

        elif choice == "3":
            print("Thank you for using the tool!")
            break

        else:
            print("Invalid choice. Try again.")


if __name__ == "__main__":
    main()
