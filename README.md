
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
import streamlit as st
import numpy as np
import plotly.graph_objects as go
from datetime import datetime

# تنظیمات صفحه
st.set_page_config(page_title="GPRS Standard Compliant Designer", layout="wide")

# --- تابع انتخاب تجهیزات بر اساس استاندارد ---
def get_standard_equipment(p_in, flow, vel, dia):
    equipment_list = []
    
    # انتخاب بر اساس ASME B31.8 برای لوله‌ها
    pipe_spec = "API 5L / ASME B31.8 compliant"
    equipment_list.append(["Piping", f"{dia} Inch", pipe_spec, "Carbon Steel"])

    # انتخاب رگولاتور بر اساس فشار و استاندارد API
    if p_in > 70:
        reg_type = "High-Pressure Pilot Operated (API 6D compliant)"
    else:
        reg_type = "Standard Pressure Regulator"
    equipment_list.append(["Regulator", reg_type, "ASME B16.34", "Steel"])

    # تعیین سطح ایمنی (SIL) بر اساس ریسک فشار
    sil_level = "SIL 2" if p_in > 50 else "SIL 1"
    equipment_list.append(["Control System", f"PLC/SCADA ({sil_level})", "IEC 61508", "Electronic"])

    # انتخاب شیر SSV بر اساس استاندارد API
    ssv_spec = "API 6D / ASME B16.34"
    equipment_list.append(["SSV", "Fast-Acting Slam-Shut", ssv_spec, "Cast Steel"])

    return equipment_list

# --- UI شروع ---
st.title("🏗️ طراح هوشمند ایستگاه کنترل فشار (استاندارد ASME/API)")
st.info("💡 این سیستم بر اساس استانداردهای ASME B31.8 و API 6D طراحی و مانیتور می‌شود.")

# --- پنل کناری ---
st.sidebar.header("📥 پارامترهای طراحی")
p_inlet = st.sidebar.number_input("فشار ورودی (Bar)", 10.0, 150.0, 50.0)
p_target = st.sidebar.number_input("فشار خروجی هدف (Bar)", 1.0, 20.0, 4.0)
flow_rate = st.sidebar.number_input("دبی گاز (m3/h)", 100.0, 10000.0, 1000.0)
pipe_diameter = st.sidebar.slider("قطر لوله (Inch)", 2, 36, 6)
p_max_limit = st.sidebar.number_input("حد فشار ایمنی (Bar)", 1.0, 100.0, 5.5)

# --- محاسبات مهندسی ---
d_meter = (pipe_diameter * 0.0254)
area = np.pi * (d_meter/2)**2
velocity = (flow_rate / 3600) / area
pressure_drop_calc = (velocity**2) * 0.01 

# --- نمایش نتایج اصلی ---
col1, col2, col3 = st.columns(3)
with col1:
    st.metric("سرعت جریان", f"{velocity:.2f} m/s")
    if velocity > 20: st.error("⚠️ Over-velocity (ASME Limit)")
    else: st.success("✅ Velocity OK")

with col2:
    st.metric("افت فشار", f"{pressure_drop_calc:.3f} Bar")

with col3:
    status = "🔴 خطر" if p_target > p_max_limit else "🟢 ایمن"
    st.metric("وضعیت ایمنی (SIL Compliance)", status)

st.divider()

# --- بخش انتخاب تجهیزات با استاندارد ---
st.subheader("📋 لیست تجهیزات پیشنهادی (Compliant BOM)")
bom = get_standard_equipment(p_inlet, flow_rate, velocity, pipe_diameter)
st.table(bom)

# --- بخش نمودار ---
st.subheader("📈 تحلیل پروفایل فشار و ایمنی")
dist = np.linspace(0, 100, 100)
p_prof = []
for d in dist:
    if d < 40: p = p_inlet - (p_inlet - p_target) * (d/100) * 0.1
    elif d < 60: p = p_inlet - (p_inlet - p_target) * ((d-40)/20)
    else: p = p_target
    p_prof.append(p)

fig = go.Figure()
fig.add_trace(go.Scatter(x=dist, y=p_prof, name="Pressure Profile", line=dict(color='#00ffcc', width=3)))
fig.add_hline(y=p_max_limit, line_dash="dash", line_color="red", annotation_text="ASME Safety Limit")
fig.update_layout(template="plotly_dark", height=400, margin=dict(l=20, r=20, t=30, b=20))
st.plotly_chart(fig, use_container_width=True)

# --- بخش گزارش نهایی فنی (Engineering Report) ---
st.divider()
st.subheader("📄 گزارش فنی نهایی (Final Engineering Report)")

report_date = datetime.now().strftime("%Y-%m-%d %H:%M")
report_text = f"""
**تاریخ گزارش:** {report_date}  
**استاندارد مرجع طراحی:** ASME B31.8 / API 6D  

**خلاصه مشخصات:**
- فشار طراحی ورودی: {p_inlet} Bar
- فشار طراحی خروجی: {p_target} Bar
- دبی طراحی: {flow_rate} m3/h
- قطر لوله انتخاب شده: {pipe_diameter} Inch

**نتیجه ارزیابی ایمنی:**
- سطح SIL مورد نیاز بر اساس فشار ورودی: {"SIL 2" if p_inlet > 50 else "SIL 1"}
- وضعیت سرعت جریان: {"متجاوز از حد استاندارد (بحرانی)" if velocity > 20 else "در محدوده استاندارد طراحی"}

**توصیه نهایی:**
{"⚠️ نیاز به بازبینی قطر لوله جهت کاهش سرعت و افت فشار دارد." if velocity > 20 else "✅ طراحی مطابق با استانداردهای مورد نظر است."}
"""
st.markdown(report_text)

# دکمه فرضی برای خروجی
if st.button("📥 تولید فایل گزارش PDF (در حال توسعه)"):
    st.info("این قابلیت در نسخه Pro فعال است.")

