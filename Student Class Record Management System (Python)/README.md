# Class average program with counter-controlled iteration.

def lettergrade(grade):
    if (90 <= grade <= 100):
        return 'A'
    if (80 <= grade <= 90):
        return 'B'
    if (70 <= grade <= 80):
        return 'C'
    if (60 <= grade <= 70):
        return 'D'
    if (0 <= grade < 60):
        return 'F'

def main():
    total = 0
    passing = 0
    fail = 0
    highest = 0.0
    lowest = 100.0
    counter = 1

    while counter <= 10:
        grade = float(input("Enter grade: "))
        total += grade

        if grade < 60:
            fail += 1
        
        if grade >= 60:
            passing += 1
        
        counter += 1

        if grade > highest:
                highest = grade

        if grade < lowest:
                lowest = grade

        print(f"Numerical Grade: {grade:.2f}\t Letter Grade: {lettergrade(grade)}")

    average = total / 10
    print('')
    print('The Class Average is', average)
    print('Letter Grade is', lettergrade(average))
    print('')
    
    print('Passed Grades:', passing)
    print('Failed Grades:', fail)
    print('Highest Grade:', highest)
    print('Lowest Grade:', lowest)

main()
