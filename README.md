# Huntington-Disease consecutive CAG repeats in a DNA sequence 
# Import the tkinter library for GUI development and os for file path handling
import tkinter as tk
import os

# Function to count the number of **consecutive** CAG repeats in a DNA sequence
def count_cag_repeats(sequence):
    count = 0  # Current consecutive CAG count
    max_count = 0  # Maximum consecutive CAG repeats found
    i = 0  # Index to iterate through the sequence
    while i < len(sequence) - 2:
        if sequence[i:i+3] == "CAG":  # Check for "CAG" codon
            count += 1
            i += 3  # Move forward by 3 to avoid overlapping
        else:
            max_count = max(max_count, count)  # Update max if needed
            count = 0
            i += 1  # Move one base ahead and recheck
    max_count = max(max_count, count)  # Final check at the end
    return max_count

# Function to determine the diagnostic status based on CAG repeat count
def determine_huntington_status(repeats):
    if repeats <= 26:
        return "Sain"  # Normal
    elif 27 <= repeats <= 35:
        return "Intermédiaire (risque faible)"  # Low risk
    elif 36 <= repeats <= 39:
        return "Pré-mutant (risque accru)"  # Increased risk
    else:
        return "Atteint"  # Affected (positive diagnosis)

# Function called when the "Calculer les répétitions CAG" button is clicked
def calculate_repeats():
    # Get input sequence from the text field and process it
    sequence = sequence_entry.get("1.0", tk.END).strip().upper()
    
    # Validate sequence: check if it's not empty
    if not sequence:
        result_label.config(text="Erreur : Veuillez entrer une séquence génétique.", fg="red")
        return

    # Validate sequence: ensure it only contains valid DNA bases
    if not all(base in "ACGT" for base in sequence):
        result_label.config(text="Erreur : La séquence contient des caractères invalides.", fg="red")
        return

    # Count CAG repeats and display the result
    global cag_repeats
    cag_repeats = count_cag_repeats(sequence)
    result_label.config(text=f"Nombre de répétitions CAG : {cag_repeats}", fg="black")

# Function called when the "Afficher le résultat" button is clicked
def show_result():
    # Check if CAG repeats have been calculated
    if cag_repeats is None:
        result_label.config(text="Erreur : Veuillez d'abord calculer les répétitions CAG.", fg="red")
        return

    # Determine diagnostic status and display it
    status = determine_huntington_status(cag_repeats)
    diagnosis_label.config(text=f"Résultat : {status}", fg="black")

# Initialize main application window
root = tk.Tk()
root.title("Analyse de la maladie de Huntington")  # Window title
root.geometry("600x500")  # Window size

# Path to logo image (update this path based on your file system)
image_path = r"C:\Users\user\Downloads\Huntington.logo.png"

# Load and display logo if the image file exists
if os.path.exists(image_path):
    logo = tk.PhotoImage(file=image_path)
    logo = logo.subsample(4, 4)  # Resize image
    logo_label = tk.Label(root, image=logo)
    logo_label.image = logo  # Keep a reference to avoid garbage collection
    logo_label.pack(pady=10)

# Initialize global variable for CAG repeat count
cag_repeats = None

# Label prompting the user to input the DNA sequence
sequence_label = tk.Label(root, text="Entrez la séquence du gène HTT :")
sequence_label.pack(pady=5)

# Text input area for the DNA sequence
sequence_entry = tk.Text(root, height=5, width=50)
sequence_entry.pack(pady=5)

# Button to trigger calculation of CAG repeats
calculate_button = tk.Button(root, text="Calculer les répétitions CAG", command=calculate_repeats)
calculate_button.pack(pady=5)

# Label to display the number of CAG repeats
result_label = tk.Label(root, text="Nombre de répétitions CAG : ", fg="black")
result_label.pack(pady=5)

# Button to show the diagnostic result
result_button = tk.Button(root, text="Afficher le résultat", command=show_result)
result_button.pack(pady=5)

# Label to display the diagnostic status
diagnosis_label = tk.Label(root, text="Résultat : ", fg="black")
diagnosis_label.pack(pady=5)

# Start the GUI event loop
root.mainloop()
