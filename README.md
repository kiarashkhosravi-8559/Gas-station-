
# Gas-station-
import streamlit as st
import numpy as np
import plotly.graph_objects as go

# تنظیمات صفحه
st.set_page_config(page_title="Gas Station Designer AI", layout="wide")

st.title("🏗️ سیستم هوشمند طراحی ایستگاه کنترل فشار گاز (GPRS)")
st.sidebar.header("📥 پارامترهای ورودی طراحی")

# --- بخش ورودی‌ها در Sidebar ---
p_inlet = st.sidebar.number_input("فشار ورودی (Inlet Pressure - Bar)", value=50.0)
p_target = st.sidebar.number_input("فشار خروجی هدف (Target Outlet - Bar)", value=4.0)
flow_rate = st.sidebar.number_input("دبی گاز (Flow Rate - m3/h)", value=1000.0)
max_p_limit = st.sidebar.number_input("حد فشار مجاز خروجی (Max Limit - Bar)", value=5.0)

# --- منطق محاسباتی (Engineering Logic) ---
def analyze_safety(p_out, p_max):
    if p_out > p_max:
        return "🔴 خطرناک: فشار خروجی از حد مجاز فراتر رفته است! (SSV باید عمل کند)"
    elif p_out < (p_target * 0.8):
        return "⚠️ هشدار: افت فشار بیش از حد (Low Pressure Alert)"
    else:
        return "✅ وضعیت عملیاتی: سیستم در محدوده ایمن است."

status_msg = analyze_safety(p_target, max_p_limit)

# --- نمایش نتایج در صفحه اصلی ---
col1, col2, col3 = st.columns(3)

with col1:
    st.metric("فشار ورودی", f"{p_inlet} Bar")
with col2:
    st.metric("فشار خروجی هدف", f"{p_target} Bar")
with col3:
    st.metric("دبی جریان", f"{flow_rate} m3/h")

st.divider()

st.subheader("📊 تحلیل وضعیت ایمنی و عملکرد")
if "🔴" in status_msg:
    st.error(status_msg)
elif "⚠️" in status_msg:
    st.warning(status_msg)
else:
    st.success(status_msg)

# --- بخش نمودار (Visualizing Pressure Drop) ---
st.subheader("📈 نمودار افت فشار در ایستگاه")

# شبیه‌سازی داده‌های نمودار
x_points = np.linspace(0, 100, 100)
# فرمول فرضی برای افت فشار در طول ایستگاه
y_pressure = np.linspace(p_inlet, p_target, 100) 

fig = go.Figure()
fig.add_trace(go.Scatter(x=x_points, y=y_pressure, mode='lines', name='Pressure Profile', line=dict(color='royalblue', width=4)))

fig.update_layout(
    xaxis_title="موقعیت در ایستگاه (متر)",
    yaxis_title="فشار (Bar)",
    template="plotly_dark"
)

st.plotly_chart(fig, use_container_width=True)

# --- بخش پیشنهادات هوشمند (AI Recommendation) ---
st.subheader("🤖 پیشنهادات هوشمند طراحی (AI Engine)")
if flow_rate > 2000:
    st.info("💡 پیشنهاد: با توجه به دبی بالا، از رگولاتورهای سایز بزرگ (Large Bore) و لوله‌های با قطر بیشتر استفاده کنید تا افت فشار کاهش یابد.")
if p_inlet > 70:
    st.info("💡 پیشنهاد: فشار ورودی بالا است؛ حتماً از شیرهای قطع‌کننده اضطراری با سرعت پاسخگویی بالا (High Speed SSV) استفاده کنید.")
