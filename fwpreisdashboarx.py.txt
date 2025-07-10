import streamlit as st

# 🔐 Einfacher Passwortschutz
def login():
    pw = st.text_input("🔐 Passwort eingeben:", type="password")
    if pw != "fernwaerme2025":
        st.stop()

login()

# 🌡️ Fernwärmekostenberechnung
ENERGIEPREIS_CENT_PRO_KWH = 8.0469
GRUNDPREIS_EURO_PRO_KW_JAHR = 42.9324
WAERMEZAEHLER_KOSTEN_MONATLICH = 2.8414

def berechne_messleistungskosten(anschlussleistung_kw):
    if anschlussleistung_kw <= 105:
        return 8.8986 * 12
    elif anschlussleistung_kw <= 245:
        return 17.1616 * 12
    elif anschlussleistung_kw <= 420:
        return 21.6109 * 12
    elif anschlussleistung_kw <= 1050:
        return 26.0602 * 12
    elif anschlussleistung_kw <= 1750:
        return 28.6027 * 12
    else:
        return 28.6027 * 12

st.title("🌡️ Fernwärme-Kostenrechner")

anschlussleistung_kw = st.number_input("Anschlussleistung (kW)", min_value=1.0, step=0.1)
verbrauch_kwh = st.number_input("Jahresverbrauch (kWh)", min_value=1.0, step=1.0)

if st.button("💰 Berechnen"):
    energiekosten = verbrauch_kwh * (ENERGIEPREIS_CENT_PRO_KWH / 100)
    grundkosten = anschlussleistung_kw * GRUNDPREIS_EURO_PRO_KW_JAHR
    messleistungskosten = berechne_messleistungskosten(anschlussleistung_kw)
    waermezaehler_kosten = WAERMEZAEHLER_KOSTEN_MONATLICH * 12

    zwischensumme = energiekosten + grundkosten + messleistungskosten + waermezaehler_kosten
    oekozuschlag = zwischensumme * 0.06
    netto_gesamt = zwischensumme + oekozuschlag
    mehrwertsteuer = netto_gesamt * 0.20
    brutto_gesamt = netto_gesamt + mehrwertsteuer
    monatskosten = brutto_gesamt / 12

    st.subheader("📊 Kostenübersicht:")
    st.write(f"Energiekosten: **{energiekosten:.2f} €**")
    st.write(f"Grundpreis: **{grundkosten:.2f} €**")
    st.write(f"Messleistungskosten: **{messleistungskosten:.2f} €**")
    st.write(f"Wärmezählerkosten: **{waermezaehler_kosten:.2f} €**")
    st.write(f"Ökoabgabe (6%): **{oekozuschlag:.2f} €**")
    st.write(f"Netto-Gesamt: **{netto_gesamt:.2f} €**")
    st.write(f"Mehrwertsteuer (20%): **{mehrwertsteuer:.2f} €**")
    st.write("___")
    st.success(f"💶 **Gesamtkosten (brutto): {brutto_gesamt:.2f} € / Jahr**")
    st.info(f"📆 **Monatliche Kosten: {monatskosten:.2f} € / Monat**")
