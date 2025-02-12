import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import re

# Function to detect units in column names
def detect_units(column_name):
    unit_patterns = {"mm": r"\bmm\b", "cm": r"\bcm\b", "m": r"\bm\b",
                     "°C": r"°C", "°F": r"°F", "K": r"\bK\b"}
    for unit, pattern in unit_patterns.items():
        if re.search(pattern, column_name, re.IGNORECASE):
            return unit
    return None

# Function to suggest graph type
def suggest_graph(df):
    num_cols = len(df.columns)
    if num_cols == 1:
        return "Histogram"
    elif num_cols == 2:
        if df.dtypes[0] in ["int64", "float64"] and df.dtypes[1] in ["int64", "float64"]:
            return "Scatter Plot"
        else:
            return "Bar Chart"
    elif num_cols > 2:
        return "Pairplot"
    return "Line Graph"

# Streamlit App UI
st.title("Easy Data Plotter")

# File uploader
uploaded_file = st.file_uploader("Upload your Excel file", type=["xlsx"])
if uploaded_file:
    df = pd.read_excel(uploaded_file)
    st.write("### Preview of Data:", df.head())
    
    # Column selection
    selected_columns = st.multiselect("Select columns to plot", df.columns)
    if selected_columns:
        df_selected = df[selected_columns]
        
        # Unit detection and conversion
        converted_columns = {}
        for col in selected_columns:
            unit = detect_units(col)
            if unit:
                new_unit = st.selectbox(f"Convert {unit} in {col} to:", [unit, "mm", "cm", "m", "°C", "°F", "K"], index=0)
                if new_unit != unit:
                    if unit == "mm" and new_unit == "m":
                        df_selected[col] = df_selected[col] / 1000
                    elif unit == "cm" and new_unit == "m":
                        df_selected[col] = df_selected[col] / 100
                    col = col.replace(unit, new_unit)
                    converted_columns[col] = new_unit
        
        # Suggest graph type
        graph_type = suggest_graph(df_selected)
        st.write(f"### Suggested Graph Type: {graph_type}")
        
        # Plot graph
        fig, ax = plt.subplots(figsize=(8, 5))
        if graph_type == "Histogram":
            sns.histplot(df_selected, bins=20, ax=ax)
        elif graph_type == "Scatter Plot":
            sns.scatterplot(x=df_selected.iloc[:, 0], y=df_selected.iloc[:, 1], ax=ax)
        elif graph_type == "Bar Chart":
            sns.barplot(x=df_selected.iloc[:, 0], y=df_selected.iloc[:, 1], ax=ax)
        elif graph_type == "Pairplot":
            sns.pairplot(df_selected)
            st.pyplot()
            st.stop()
        
        st.pyplot(fig)
