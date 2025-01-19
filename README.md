import json
import os

# File to store results
DATA_FILE = "student_grades.json"

def load_data():
    """Load data from the JSON file."""
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, 'r') as file:
            return json.load(file)
    return {}

def save_data(data):
    """Save data to the JSON file."""
    with open(DATA_FILE, 'w') as file:
        json.dump(data, file, indent=4)

def input_validation(prompt, data_type=float, condition=None):
    """Validate input based on type and condition."""
    while True:
        try:
            value = data_type(input(prompt))
            if condition and not condition(value):
                raise ValueError
            return value
        except ValueError:
            print("Invalid input. Please try again.")

def calculate_weighted_average(marks, weights):
    """Calculate the weighted average."""
    total_weighted_score = sum(m * w for m, w in zip(marks, weights))
    total_weights = sum(weights)
    return total_weighted_score / total_weights

def display_statistics(results):
    """Display statistics for all students."""
    print("\nOverall Statistics:")
    if not results:
        print("No data available.")
        return
    
    total_students = len(results)
    total_avg = sum(student['weighted_average'] for student in results.values()) / total_students
    print(f"Total Students: {total_students}")
    print(f"Average Grade: {total_avg:.2f}")

def main():
    print("=== Student Grade Calculator ===")
    data = load_data()
    
    student_name = input("Enter student name: ")
    subjects = int(input_validation("Enter the number of subjects: ", int, lambda x: x > 0))
    
    marks = []
    weights = []
    for i in range(subjects):
        print(f"\nSubject {i + 1}:")
        mark = input_validation("Enter mark (0-100): ", float, lambda x: 0 <= x <= 100)
        weight = input_validation("Enter weight (0-1): ", float, lambda x: 0 <= x <= 1)
        marks.append(mark)
        weights.append(weight)
    
    weighted_average = calculate_weighted_average(marks, weights)
    
    grade = None
    if weighted_average >= 90:
        grade = "A"
    elif weighted_average >= 80:
        grade = "B"
    elif weighted_average >= 70:
        grade = "C"
    elif weighted_average >= 60:
        grade = "D"
    else:
        grade = "F"
    
    print("\nFinal Results:")
    print(f"Student Name: {student_name}")
    print(f"Weighted Average: {weighted_average:.2f}")
    print(f"Grade: {grade}")
    
    # Store the result
    data[student_name] = {
        "marks": marks,
        "weights": weights,
        "weighted_average": weighted_average,
        "grade": grade,
    }
    save_data(data)
    
    # Display statistics
    display_statistics(data)

if __name__ == "__main__":
    main()
