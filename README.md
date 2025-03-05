import json
import os

DATA_FILE = "fitness_data.json"

class FitnessTracker:
    def __init__(self):
        self.data = self.load_data()

    def load_data(self):
        """Load data file, create default data if it does not exist."""
        if os.path.exists(DATA_FILE):
            try:
                with open(DATA_FILE, "r") as f:
                    data = json.load(f)
            except json.JSONDecodeError:
                print("⚠️ Detected a corrupted JSON file, resetting data...")
                data = self.default_data()
                self.save_data(data)
        else:
            data = self.default_data()
            self.save_data(data)
        
        return data

    def save_data(self, data=None):
        """Save data to JSON file."""
        if data is None:
            data = self.data  # Default to saving current data

        try:
            with open(DATA_FILE, "w") as f:
                json.dump(data, f, indent=2)
        except PermissionError:
            print("❌ Unable to write to file, please check permissions!")

    def default_data(self):
        """Return default data."""
        return {
            "goal": {"total_minutes": 0, "total_calories": 0},  # Goal
            "records": {"total_minutes": 0, "total_calories": 0}  # Actual accumulated data
        }

    def set_goal(self, total_minutes, total_calories):
        """Set total goal."""
        self.data["goal"]["total_minutes"] = total_minutes
        self.data["goal"]["total_calories"] = total_calories
        self.save_data()
        print("✅ Exercise goal updated!")

    def add_record(self, minutes, calories):
        """Accumulate exercise data."""
        if minutes < 0 or calories < 0:
            print("❌ Invalid input, exercise time and calories must be positive numbers!")
            return

        self.data["records"]["total_minutes"] += minutes
        self.data["records"]["total_calories"] += calories
        self.save_data()
        print("✅ Exercise data recorded!")

    def get_statistics(self):
        """Retrieve current exercise data."""
        return {
            "total_minutes": self.data["records"]["total_minutes"],
            "total_calories": self.data["records"]["total_calories"],
            "goal_minutes": self.data["goal"]["total_minutes"],
            "goal_calories": self.data["goal"]["total_calories"],
            "remaining_minutes": max(self.data["goal"]["total_minutes"] - self.data["records"]["total_minutes"], 0),
            "remaining_calories": max(self.data["goal"]["total_calories"] - self.data["records"]["total_calories"], 0),
        }

    def clear_data(self):
        """Clear all data."""
        self.data = self.default_data()
        self.save_data()
        print("🗑️ All data has been reset!")
//ai made
def main_menu():
    tracker = FitnessTracker()
    
    while True:
        print("\n🏃 Exercise Tracker Menu")
        print("1️⃣ Set Exercise Goal")
        print("2️⃣ Record Exercise Data")
        print("3️⃣ View Exercise Data")
        print("4️⃣ Clear All Data")
        print("5️⃣ Exit")
        
        choice = input("🔹 Enter your choice: ")

        if choice == "1":
            try:
                total_minutes = float(input("💪 Enter total exercise goal (minutes): "))
                total_calories = float(input("🔥 Enter total calorie burn goal (kcal): "))
                tracker.set_goal(total_minutes, total_calories)
            except ValueError:
                print("❌ Please enter a valid number!")

        elif choice == "2":
            try:
                minutes = float(input("📌 Enter exercise duration (minutes): "))
                calories = float(input("📌 Enter calories burned (kcal): "))
                tracker.add_record(minutes, calories)
            except ValueError:
                print("❌ Please enter a valid number!")

        elif choice == "3":
            stats = tracker.get_statistics()
            print("\n📊 Exercise Data Summary")
            print("="*30)
            print(f"🏅 Total Exercise Time: {stats['total_minutes']} minutes | Goal: {stats['goal_minutes']} minutes")
            print(f"🔥 Total Calories Burned: {stats['total_calories']} kcal | Goal: {stats['goal_calories']} kcal")
//ai made
            print("\n🎯 Goal vs. Actual")
            print(f"⚠️ Remaining {stats['remaining_minutes']} minutes of exercise")
            print(f"⚠️ Remaining {stats['remaining_calories']} kcal to burn")

            if stats["remaining_minutes"] == 0 and stats["remaining_calories"] == 0:
                print("🎉 🎉 🎉 Congratulations! You have achieved your exercise goal! 🎉 🎉 🎉")

        elif choice == "4":
            tracker.clear_data()

        elif choice == "5":
            print("👋 Goodbye!")
            break

        else:
            print("❌ Invalid choice, please try again!")

if __name__ == "__main__":
    main_menu()
