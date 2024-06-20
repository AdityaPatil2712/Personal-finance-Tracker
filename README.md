import pandas as pd
import streamlit as st

# Helper function to normalize column names
def normalize_column_names(columns):
    return [col.strip().lower() for col in columns]

# Helper function to clean amount column
def clean_amount(amount):
    try:
        return float(str(amount).replace('$', '').replace(',', '').strip())
    except ValueError:
        return None  # Return None for non-numeric values

# Streamlit app
def app():
    st.title("Personal Finance Tracker")

    # File uploader
    uploaded_file = st.file_uploader("Upload your bank statement (CSV or Excel)", type=["csv", "xlsx"])

    if uploaded_file is not None:
        try:
            if uploaded_file.name.endswith('.csv'):
                df = pd.read_csv(uploaded_file)
            else:
                df = pd.read_excel(uploaded_file)

            st.write("Uploaded Data:")
            st.write(df.head())

            # Normalize column names
            df.columns = normalize_column_names(df.columns)

            # Check if necessary columns exist
            if 'date' in df.columns and 'amount' in df.columns:
                # Process and clean the data
                df['date'] = pd.to_datetime(df['date'], errors='coerce')  # Convert to datetime, set errors to NaT
                df['amount'] = df['amount'].apply(clean_amount)  # Clean and convert amounts

                # Drop rows with invalid data
                df.dropna(subset=['date', 'amount'], inplace=True)

                # Display cleaned data
                st.write("Cleaned Data:")
                st.write(df.head())
            else:
                st.error("Uploaded file must contain 'Date' and 'Amount' columns (case insensitive).")
        except Exception as e:
            st.error(f"Error processing file: {e}")

if __name__ == '__main__':
    app()
# Personal-finance-Tracker
