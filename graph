import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime
import numpy as np

st.set_page_config(page_title="Production Line Dashboard", layout="wide")

st.title("📊 Production Line Performance Dashboard")

uploaded_file = st.file_uploader("Upload your Excel file", type=["xlsx"])

if uploaded_file:
    df = pd.read_excel(uploaded_file)

    # Basic validation
    expected_columns = ["Date", "Production Line", "Planned Downtime", "Micro stops", 
                        "Unplanned Downtime", "PR", "Target PR"]
    if not all(col in df.columns for col in expected_columns):
        st.error(f"Missing one or more expected columns: {expected_columns}")
    else:
        # Preprocessing
        df["Date"] = pd.to_datetime(df["Date"])
        df.sort_values("Date", inplace=True)

        production_lines = df["Production Line"].unique()

        def plot_pr_chart(line_df, line_name):
            fig, ax = plt.subplots(figsize=(10, 5))

            colors = ["green" if pr >= target else "red" 
                      for pr, target in zip(line_df["PR"], line_df["Target PR"])]
            
            ax.bar(line_df["Date"].dt.strftime("%Y-%m-%d"), line_df["PR"], color=colors)

            # Add trendline
            x = np.arange(len(line_df))
            z = np.polyfit(x, line_df["PR"], 1)
            p = np.poly1d(z)
            ax.plot(line_df["Date"].dt.strftime("%Y-%m-%d"), p(x), "k--", label="Trendline")

            ax.set_title(f"PR vs Target PR for {line_name}")
            ax.set_ylabel("PR")
            ax.set_xlabel("Date")
            ax.tick_params(axis='x', rotation=45)
            ax.legend()
            st.pyplot(fig)

        def plot_downtime_chart(line_df, line_name, column):
            fig, ax = plt.subplots(figsize=(10, 5))

            ax.bar(line_df["Date"].dt.strftime("%Y-%m-%d"), line_df[column], color='steelblue')

            x = np.arange(len(line_df))
            z = np.polyfit(x, line_df[column], 1)
            p = np.poly1d(z)
            ax.plot(line_df["Date"].dt.strftime("%Y-%m-%d"), p(x), "k--", label="Trendline")

            ax.set_title(f"{column} Over Time for {line_name}")
            ax.set_ylabel(column)
            ax.set_xlabel("Date")
            ax.tick_params(axis='x', rotation=45)
            ax.legend()
            st.pyplot(fig)

        for line in sorted(production_lines):
            st.subheader(f"📈 Line {line} Charts")

            line_df = df[df["Production Line"] == line].copy()

            st.markdown("**🔹 PR vs Target PR**")
            plot_pr_chart(line_df, line)

            for downtime_type in ["Planned Downtime", "Micro stops", "Unplanned Downtime"]:
                st.markdown(f"**🔸 {downtime_type}**")
                plot_downtime_chart(line_df, line, downtime_type)
