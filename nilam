import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import os
import requests

st.set_page_config(page_title="SmartMood", layout="centered")
st.title("🌈 SmartMood - Pelacak Mood & Aktivitas Harian")

DATA_FILE = "mood_data.csv"

activity_dict = {
    "Olahraga": 8,
    "Tidur cukup": 7,
    "Makan sehat": 6,
    "Main HP lama": 2,
    "Belajar": 9,
    "Dengar musik": 6,
    "Rebahan": 3,
    "Jalan-jalan": 7,
    "Bertemu teman": 8
}

def classify_mood(score):
    if score < 10:
        return "😢 Sedih", "Coba istirahat dan lakukan hal yang menyenangkan."
    elif 10 <= score < 20:
        return "😐 Biasa", "Mood kamu sedang stabil, pertahankan."
    else:
        return "😊 Bahagia", "Luar biasa! Pertahankan energi positif ini."

def get_daily_quote():
    try:
        response = requests.get('https://zenquotes.io/api/random')
        data = response.json()
        return f'"{data[0]["q"]}" — {data[0]["a"]}'
    except:
        return "Tetap semangat!"

def simpan_data(tanggal, skor, mood, aktivitas):
    new_data = pd.DataFrame([[tanggal, skor, mood, ", ".join(aktivitas)]],
                             columns=["Tanggal", "Skor", "Mood", "Aktivitas"])
    if os.path.exists(DATA_FILE):
        df = pd.read_csv(DATA_FILE)
        df = pd.concat([df, new_data], ignore_index=True)
    else:
        df = new_data
    df.to_csv(DATA_FILE, index=False)

menu = st.sidebar.radio("📋 Menu", ["Input Aktivitas", "Grafik Mingguan", "Lihat Data", "Tentang App"])

if menu == "Input Aktivitas":
    st.subheader("📝 Input Aktivitas Harian")
    st.markdown("Pilih 1 sampai 4 aktivitas yang kamu lakukan hari ini.")
    selected = st.multiselect("Aktivitas hari ini:", list(activity_dict.keys()))

    if st.button("💾 Simpan dan Analisis", disabled=(len(selected) == 0)):
        if len(selected) > 4:
            st.error("Maksimal pilih 4 aktivitas saja.")
        else:
            skor = sum(activity_dict[act] for act in selected)
            mood, saran = classify_mood(skor)
            tanggal = datetime.now().strftime("%Y-%m-%d")
            simpan_data(tanggal, skor, mood, selected)

            st.success(f"Data untuk {tanggal} berhasil disimpan!")
            st.markdown(f"**Skor Mood:** {skor} — {mood}")
            st.info(f"💡 Saran: {saran}")
            st.markdown(f"> 💬 {get_daily_quote()}")

elif menu == "Grafik Mingguan":
    st.subheader("📊 Grafik Mood Selama Seminggu")

    if not os.path.exists(DATA_FILE):
        st.warning("Belum ada data tersimpan.")
    else:
        df = pd.read_csv(DATA_FILE)
        if len(df) < 3:
            st.warning("Data belum cukup untuk menampilkan grafik (minimal 3 hari).")
        else:
            df = df.tail(7)
            color_map = df["Mood"].map(lambda m: "green" if "Bahagia" in m else ("gold" if "Biasa" in m else "blue"))

            fig, ax = plt.subplots(figsize=(10, 4))
            ax.bar(df["Tanggal"], df["Skor"], color=color_map)
            ax.set_title("Mood Kamu Selama Seminggu")
            ax.set_ylabel("Skor Mood")
            ax.set_xlabel("Tanggal")
            ax.set_ylim(0, 30)
            ax.grid(True)
            st.pyplot(fig)

elif menu == "Lihat Data":
    st.subheader("🗃️ Data Tersimpan")
    if os.path.exists(DATA_FILE):
        df = pd.read_csv(DATA_FILE)
        st.dataframe(df)
    else:
        st.info("Belum ada data yang tersimpan.")

elif menu == "Tentang App":
    st.subheader("ℹ️ Tentang SmartMood")
    st.markdown("""
    **SmartMood** adalah aplikasi pelacakan mood harian berbasis aktivitas.

    ✅ Input hingga 4 aktivitas harian  
    ✅ Hitung skor dan klasifikasi mood  
    ✅ Tampilkan kutipan motivasi  
    ✅ Simpan dan visualisasikan mood mingguan

    Dengan SmartMood, kamu bisa lebih sadar akan pola keseharianmu dan mulai menjaga kesehatan mentalmu 🌱
    """)
