# Crop-Recommendation1
import tkinter as tk
from tkinter import messagebox
import pandas as pd

from sklearn.ensemble import RandomForestClassifier


# ==========================================================
# SAMPLE AGRICULTURE DATASET
# ==========================================================

data = {
    "N": [
        90, 85, 88, 95, 80,
        20, 25, 18, 30, 22,
        40, 45, 38, 42, 48,
        70, 75, 68, 72, 78,
        55, 60, 52, 58, 62
    ],

    "P": [
        42, 40, 45, 38, 44,
        55, 50, 60, 52, 58,
        40, 45, 38, 42, 35,
        35, 40, 32, 38, 42,
        48, 52, 45, 50, 55
    ],

    "K": [
        43, 40, 45, 42, 38,
        25, 30, 22, 28, 24,
        20, 25, 18, 22, 28,
        40, 45, 38, 42, 48,
        30, 35, 28, 32, 38
    ],

    "temperature": [
        25, 26, 24, 27, 25,
        22, 24, 21, 23, 25,
        28, 30, 27, 29, 31,
        20, 22, 19, 21, 23,
        26, 28, 25, 27, 29
    ],

    "humidity": [
        80, 82, 78, 85, 81,
        65, 68, 62, 70, 66,
        55, 58, 52, 60, 54,
        75, 78, 72, 80, 76,
        70, 73, 68, 75, 71
    ],

    "ph": [
        6.5, 6.7, 6.4, 6.6, 6.8,
        6.0, 6.2, 5.8, 6.1, 6.3,
        6.8, 7.0, 6.7, 6.9, 7.1,
        6.2, 6.4, 6.0, 6.3, 6.5,
        6.5, 6.7, 6.3, 6.6, 6.8
    ],

    "rainfall": [
        200, 220, 190, 210, 230,
        100, 120, 90, 110, 130,
        60, 70, 55, 65, 75,
        150, 170, 140, 160, 180,
        100, 120, 90, 110, 130
    ],

    "crop": [
        "Rice", "Rice", "Rice", "Rice", "Rice",
        "Wheat", "Wheat", "Wheat", "Wheat", "Wheat",
        "Millet", "Millet", "Millet", "Millet", "Millet",
        "Maize", "Maize", "Maize", "Maize", "Maize",
        "Cotton", "Cotton", "Cotton", "Cotton", "Cotton"
    ]
}


# Convert dictionary into DataFrame
df = pd.DataFrame(data)


# ==========================================================
# MACHINE LEARNING MODEL
# ==========================================================

features = [
    "N",
    "P",
    "K",
    "temperature",
    "humidity",
    "ph",
    "rainfall"
]

X = df[features]
y = df["crop"]


# Random Forest model
model = RandomForestClassifier(
    n_estimators=100,
    random_state=42
)

model.fit(X, y)


# ==========================================================
# RECOMMEND CROP
# ==========================================================

def recommend_crop():

    try:

        # Get values from GUI
        nitrogen = float(n_entry.get())
        phosphorus = float(p_entry.get())
        potassium = float(k_entry.get())
        temperature = float(temp_entry.get())
        humidity = float(humidity_entry.get())
        ph = float(ph_entry.get())
        rainfall = float(rainfall_entry.get())

        # Basic validation
        if nitrogen < 0 or phosphorus < 0 or potassium < 0:
            messagebox.showerror(
                "Invalid Input",
                "N, P and K values cannot be negative."
            )
            return

        if humidity < 0 or humidity > 100:
            messagebox.showerror(
                "Invalid Input",
                "Humidity should be between 0 and 100."
            )
            return

        if ph < 0 or ph > 14:
            messagebox.showerror(
                "Invalid Input",
                "pH should be between 0 and 14."
            )
            return

        # Create input data
        input_data = pd.DataFrame(
            [[
                nitrogen,
                phosphorus,
                potassium,
                temperature,
                humidity,
                ph,
                rainfall
            ]],
            columns=features
        )

        # Predict crop
        prediction = model.predict(input_data)

        crop = prediction[0]

        # Get prediction probabilities
        probabilities = model.predict_proba(input_data)

        confidence = max(probabilities[0]) * 100

        # Display result
        result_label.config(
            text=f"Recommended Crop: {crop}"
        )

        confidence_label.config(
            text=f"Model Confidence: {confidence:.2f}%"
        )

        # Crop information
        crop_info = {
            "Rice": "Rice requires high rainfall, humidity and warm temperature.",
            "Wheat": "Wheat grows well in moderate temperature and rainfall.",
            "Millet": "Millet is suitable for relatively dry conditions.",
            "Maize": "Maize grows well with moderate rainfall and nutrients.",
            "Cotton": "Cotton prefers warm conditions and moderate rainfall."
        }

        info_label.config(
            text=crop_info.get(
                crop,
                "Suitable crop based on the entered conditions."
            )
        )

    except ValueError:

        messagebox.showerror(
            "Invalid Input",
            "Please enter numbers in all fields."
        )


# ==========================================================
# CLEAR FUNCTION
# ==========================================================

def clear_fields():

    n_entry.delete(0, tk.END)
    p_entry.delete(0, tk.END)
    k_entry.delete(0, tk.END)

    temp_entry.delete(0, tk.END)
    humidity_entry.delete(0, tk.END)
    ph_entry.delete(0, tk.END)
    rainfall_entry.delete(0, tk.END)

    result_label.config(
        text="Recommended Crop: --"
    )

    confidence_label.config(
        text="Model Confidence: --"
    )

    info_label.config(
        text=""
    )


# ==========================================================
# GUI WINDOW
# ==========================================================

window = tk.Tk()

window.title("Smart Agriculture Crop Recommendation System")

window.geometry("750x750")

window.configure(bg="#e8f5e9")


# ==========================================================
# TITLE
# ==========================================================

title = tk.Label(
    window,
    text="Smart Agriculture Crop Recommendation System",
    font=("Arial", 22, "bold"),
    bg="#e8f5e9",
    fg="#1b5e20"
)

title.pack(pady=20)


subtitle = tk.Label(
    window,
    text="Enter soil and environmental conditions",
    font=("Arial", 13),
    bg="#e8f5e9"
)

subtitle.pack(pady=5)


# ==========================================================
# INPUT FRAME
# ==========================================================

frame = tk.Frame(
    window,
    bg="#e8f5e9"
)

frame.pack(pady=20)


# Function to create labels and entries
def create_input(row, text):

    label = tk.Label(
        frame,
        text=text,
        font=("Arial", 12),
        bg="#e8f5e9"
    )

    label.grid(
        row=row,
        column=0,
        padx=15,
        pady=8,
        sticky="w"
    )

    entry = tk.Entry(
        frame,
        font=("Arial", 12),
        width=25
    )

    entry.grid(
        row=row,
        column=1,
        padx=15,
        pady=8
    )

    return entry


# Create inputs

n_entry = create_input(
    0,
    "Nitrogen (N):"
)

p_entry = create_input(
    1,
    "Phosphorus (P):"
)

k_entry = create_input(
    2,
    "Potassium (K):"
)

temp_entry = create_input(
    3,
    "Temperature (°C):"
)

humidity_entry = create_input(
    4,
    "Humidity (%):"
)

ph_entry = create_input(
    5,
    "Soil pH:"
)

rainfall_entry = create_input(
    6,
    "Rainfall (mm):"
)


# ==========================================================
# BUTTONS
# ==========================================================

button_frame = tk.Frame(
    window,
    bg="#e8f5e9"
)

button_frame.pack(pady=15)


recommend_button = tk.Button(
    button_frame,
    text="RECOMMEND CROP",
    font=("Arial", 13, "bold"),
    bg="#2e7d32",
    fg="white",
    width=18,
    height=2,
    command=recommend_crop
)

recommend_button.grid(
    row=0,
    column=0,
    padx=10
)


clear_button = tk.Button(
    button_frame,
    text="CLEAR",
    font=("Arial", 13, "bold"),
    bg="#555555",
    fg="white",
    width=12,
    height=2,
    command=clear_fields
)

clear_button.grid(
    row=0,
    column=1,
    padx=10
)


# ==========================================================
# RESULT
# ==========================================================

result_label = tk.Label(
    window,
    text="Recommended Crop: --",
    font=("Arial", 20, "bold"),
    bg="#e8f5e9",
    fg="#1b5e20"
)

result_label.pack(pady=15)


confidence_label = tk.Label(
    window,
    text="Model Confidence: --",
    font=("Arial", 14),
    bg="#e8f5e9"
)

confidence_label.pack()


info_label = tk.Label(
    window,
    text="",
    font=("Arial", 12),
    bg="#e8f5e9",
    wraplength=600
)

info_label.pack(pady=15)


# ==========================================================
# FOOTER
# ==========================================================

footer = tk.Label(
    window,
    text="Machine Learning Model: Random Forest",
    font=("Arial", 10),
    bg="#e8f5e9"
)

footer.pack(
    side=tk.BOTTOM,
    pady=15
)


# ==========================================================
# START PROGRAM
# ==========================================================

print("Smart Agriculture Crop Recommendation System Started!")

window.mainloop()
