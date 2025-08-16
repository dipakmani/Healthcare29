# -------------------------------
# Complete Healthcare Fact Tables Generator
# -------------------------------

import pandas as pd
import random
from datetime import datetime, timedelta

# -------------------------------
# Helper Functions
# -------------------------------
def random_date(start, end):
    """Generate a random date between start and end"""
    delta = end - start
    random_days = random.randint(0, delta.days)
    return start + timedelta(days=random_days)

def random_choice(choices):
    return random.choice(choices)

# -------------------------------
# Configuration
# -------------------------------
num_records = 500000  # 5 lakh records per table
batch_size = 50000    # write in batches to save memory
start_date = datetime(2020, 1, 1)
end_date = datetime(2025, 12, 31)

# Sample dimension IDs
patients = list(range(1, 5001))       # 5k patients
doctors = list(range(1, 201))         # 200 doctors
procedures = list(range(1, 51))       # 50 procedures
hospitals = list(range(1, 21))        # 20 hospitals
departments = list(range(1, 31))      # 30 departments
equipments = list(range(1, 101))      # 100 equipments
medications = list(range(1, 101))     # 100 medications
labs = list(range(1, 51))             # 50 labs
lab_tests = list(range(1, 51))        # 50 lab tests
pharmacies = list(range(1, 21))       # 20 pharmacies
rooms = list(range(1, 101))           # 100 rooms
insurance_ids = list(range(1, 51))    # 50 insurance plans

# -------------------------------
# Generic CSV generator
# -------------------------------
def generate_fact_csv(filename, row_func):
    chunks = num_records // batch_size
    for i in range(chunks):
        data = [row_func(j + i*batch_size) for j in range(batch_size)]
        df = pd.DataFrame(data)
        if i == 0:
            df.to_csv(filename, index=False, mode='w')
        else:
            df.to_csv(filename, index=False, mode='a', header=False)
        print(f"Written batch {i+1}/{chunks} to {filename}")

# -------------------------------
# Fact Table Row Functions
# -------------------------------
def fact_visits_row(idx):
    return {
        "Visit_ID": idx,
        "Patient_ID": random_choice(patients),
        "Doctor_ID": random_choice(doctors),
        "Department_ID": random_choice(departments),
        "Visit_Date": random_date(start_date, end_date).strftime("%Y-%m-%d"),
        "Insurance_ID": random_choice(insurance_ids),
        "Visit_Type": random_choice(["Emergency", "Routine", "Follow-up"]),
        "Visit_Cost": random.randint(500, 5000),
        "Duration_Minutes": random.randint(15, 120),
        "Outcome": random_choice(["Recovered", "Referred", "Admitted"])
    }

def fact_operations_row(idx):
    return {
        "Operation_ID": idx,
        "Patient_ID": random_choice(patients),
        "Doctor_ID": random_choice(doctors),
        "Procedure_ID": random_choice(procedures),
        "Hospital_ID": random_choice(hospitals),
        "Date": random_date(start_date, end_date).strftime("%Y-%m-%d"),
        "Equipment_ID": random_choice(equipments),
        "Department_ID": random_choice(departments),
        "Operation_Duration_Minutes": random.randint(60, 200),
        "Operation_Cost": random.randint(10000, 30000),
        "Anesthesia_Type": random_choice(["General", "Local", "Regional"]),
        "Blood_Transfusion_Required": random_choice(["Yes", "No"]),
        "Outcome_Status": random_choice(["Successful", "Complications", "Failed", "Referred"]),
        "Recovery_Time_Days": random.randint(3, 14),
        "Number_of_Assisting_Staff": random.randint(2, 5)
    }

def fact_medications_row(idx):
    return {
        "Prescription_ID": idx,
        "Patient_ID": random_choice(patients),
        "Doctor_ID": random_choice(doctors),
        "Medication_ID": random_choice(medications),
        "Date": random_date(start_date, end_date).strftime("%Y-%m-%d"),
        "Pharmacy_ID": random_choice(pharmacies),
        "Dosage": f"{random.randint(1,3)} tablet(s) per day",
        "Duration_Days": random.randint(3, 14),
        "Prescription_Cost": random.randint(100, 2000)
    }

def fact_labtests_row(idx):
    return {
        "Test_ID": idx,
        "Patient_ID": random_choice(patients),
        "Doctor_ID": random_choice(doctors),
        "LabTest_ID": random_choice(lab_tests),
        "Lab_ID": random_choice(labs),
        "Date": random_date(start_date, end_date).strftime("%Y-%m-%d"),
        "Test_Result_Value": round(random.uniform(0.1, 15.0), 2),
        "Test_Result_Unit": random_choice(["mg/dL", "mmHg", "IU/L"]),
        "Test_Cost": random.randint(500, 5000)
    }

def fact_hospitalizations_row(idx):
    admission_date = random_date(start_date, end_date)
    discharge_date = admission_date + timedelta(days=random.randint(1, 20))
    return {
        "Admission_ID": idx,
        "Patient_ID": random_choice(patients),
        "Doctor_ID": random_choice(doctors),
        "Department_ID": random_choice(departments),
        "Hospital_ID": random_choice(hospitals),
        "Room_ID": random_choice(rooms),
        "Admission_Date": admission_date.strftime("%Y-%m-%d"),
        "Discharge_Date": discharge_date.strftime("%Y-%m-%d"),
        "Length_of_Stay_Days": (discharge_date - admission_date).days,
        "Admission_Cost": random.randint(5000, 50000),
        "Outcome": random_choice(["Recovered", "Referred", "Deceased"])
    }

# -------------------------------
# Generate All Fact Tables
# -------------------------------
print("Generating Fact_Visits...")
generate_fact_csv("Fact_Visits.csv", fact_visits_row)

print("Generating Fact_Operations...")
generate_fact_csv("Fact_Operations.csv", fact_operations_row)

print("Generating Fact_Medications...")
generate_fact_csv("Fact_Medications.csv", fact_medications_row)

print("Generating Fact_LabTests...")
generate_fact_csv("Fact_LabTests.csv", fact_labtests_row)

print("Generating Fact_Hospitalizations...")
generate_fact_csv("Fact_Hospitalizations.csv", fact_hospitalizations_row)

print("All 5 healthcare fact tables with 5 lakh records each have been generated successfully!")
