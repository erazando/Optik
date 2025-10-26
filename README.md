import streamlit as st
import numpy as np
import matplotlib.pyplot as plt

# Data indeks bias
materials = {
    "Udara": 1.00,
    "Air": 1.33,
    "Kaca": 1.50,
    "Akrilik": 1.49,
    "Minyak": 1.46
}

st.title("Simulasi Pembiasan Cahaya (Hukum Snellius)")
st.write("Aplikasi ini menghitung sudut bias serta menampilkan visualisasi pembiasan cahaya")

# Input
medium1 = st.selectbox("Medium asal (n1)", list(materials.keys()))
medium2 = st.selectbox("Medium tujuan (n2)", list(materials.keys()))
theta1_deg = st.slider("Sudut datang (°)", 0.0, 80.0, 30.0)

n1 = materials[medium1]
n2 = materials[medium2]
theta1 = np.radians(theta1_deg)

# Perhitungan sudut bias
try:
    theta2 = np.arcsin((n1 / n2) * np.sin(theta1))
    theta2_deg = np.degrees(theta2)
    total_internal_reflection = False
except ValueError:
    total_internal_reflection = True
    theta2_deg = None

st.subheader("Hasil Perhitungan")

if total_internal_reflection:
    st.error("Terjadi **Pembiasan Total Internal**. Tidak ada sinar bias keluar.")
else:
    st.success(f"Sudut bias: **{theta2_deg:.2f}°**")

    # Jenis pembiasan
    if theta2_deg < theta1_deg:
        st.info("Sinar **mendekati garis normal** karena masuk medium lebih rapat (n2 > n1)")
    else:
        st.info("Sinar **menjauhi garis normal** karena masuk medium lebih renggang (n2 < n1)")

    # Plot visualisasi
    fig, ax = plt.subplots()
    ax.axvline(0, color='black', linestyle='--')

    # Sinar datang
    ax.plot([0, -np.sin(theta1)], [0, np.cos(theta1)], label="Sinar Datang")

    # Sinar bias
    ax.plot([0, np.sin(theta2)], [0, -np.cos(theta2)], label="Sinar Bias")

    ax.set_xlim(-1, 1)
    ax.set_ylim(-1, 1)
    ax.set_aspect('equal')
    ax.set_title('Visualisasi Pembiasan')
    ax.legend()
    ax.axis('off')
    st.pyplot(fig)
