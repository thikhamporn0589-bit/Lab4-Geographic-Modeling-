# GE338 Lab 4: Geographic Modeling — Urban Heat Risk

พื้นที่ใดในกรุงเทพมหานครมีความเสี่ยงต่อความร้อนในระดับสูง และปัจจัยใดเป็นตัวกำหนดความเสี่ยงดังกล่าว?

---

## 3. Methodology

### 3.1 Data Sources

- MODIS LST (MOD11A2) — อุณหภูมิพื้นผิว
- Sentinel-2 — ใช้คำนวณ NDVI และ NDBI
- SRTM DEM — ความสูงของพื้นที่

---

### 3.2 Factors Selection

แบบจำลองนี้ใช้ 4 ปัจจัยหลัก:

1. **NDVI (Normalized Difference Vegetation Index)**  
   แสดงปริมาณพืชพรรณ มีผลลดอุณหภูมิผ่านกระบวนการ evapotranspiration

2. **LST (Land Surface Temperature)**  
   เป็นตัวแทนอุณหภูมิพื้นผิวจริง

3. **Built-up Index (NDBI)**  
   แสดงความหนาแน่นของสิ่งปลูกสร้าง ซึ่งเป็นแหล่งสะสมความร้อน

4. **DEM (Elevation)**  
   ความสูงของพื้นที่มีผลต่ออุณหภูมิ (temperature lapse rate)

---

### 3.3 Normalization

ใช้วิธี Min-Max Normalization:

\[
X_{norm} = \frac{X - X_{min}}{X_{max} - X_{min}}
\]

โดยมีการปรับทิศทางของบางปัจจัย:
- NDVI → inverse (เพราะเพิ่ม vegetation = ลดความร้อน)
- DEM → inverse (พื้นที่สูง = อุณหภูมิต่ำ)

---

### 3.4 Weight Assignment

น้ำหนักของแต่ละปัจจัย:

| Factor | Weight |
|--------|--------|
| NDVI   | 0.30 |
| LST    | 0.25 |
| NDBI   | 0.25 |
| DEM    | 0.20 |

**เหตุผล:**
- NDVI มีผลเชิงลบกับ LST อย่างมีนัยสำคัญ (Weng et al., 2004; Zhou et al., 2014)
- LST เป็นตัววัดอุณหภูมิจริง
- Built-up เป็น driver สำคัญของ UHI (Oke, 1982)
- DEM มีผลรองในพื้นที่เมือง

---

### 3.5 Model Equation

\[
Heat\ Risk = (0.30 \cdot NDVI') + (0.25 \cdot LST) + (0.25 \cdot NDBI) + (0.20 \cdot DEM')
\]

---

### 3.6 Classification

แบ่งระดับความเสี่ยงเป็น 3 ระดับ:

- Low: < 0.3  
- Medium: 0.3 – 0.6  
- High: > 0.6  

ใช้วิธี Equal Interval เนื่องจากข้อมูลอยู่ในช่วง 0–1

---

## 4. Results

### 4.1 Model Statistics

- Mean: 0.444  
- Min: 0.249  
- Max: 0.709  
- StdDev: 0.091  

แสดงว่าพื้นที่ส่วนใหญ่มีความเสี่ยงระดับปานกลาง และมีความแปรปรวนไม่สูงมาก

---

### 4.2 Spatial Distribution

- Low Risk: 3.4%  
- Medium Risk: 92.1%  
- High Risk: 4.8%  

พื้นที่ความเสี่ยงสูงกระจุกตัวในเขตเมืองที่มีความหนาแน่นของสิ่งปลูกสร้างสูง

---

## 5. Sensitivity Analysis

มีการปรับน้ำหนัก NDVI ±20% และพบว่า:

- พื้นที่ stable ≈ 82%  
- พื้นที่เมืองมีความไวต่อการเปลี่ยนน้ำหนักสูง

แสดงว่าโมเดลมีความเสถียรในระดับดี แต่มีความไม่แน่นอนในบางพื้นที่ที่ซับซ้อน

---

## 6. Validation

ใช้ LST เป็น proxy validation โดยเปรียบเทียบค่าเฉลี่ย:

- Model mean: 0.444  
- LST mean: 0.288  

ผลลัพธ์มีแนวโน้มสอดคล้องกัน แม้โมเดลจะให้ค่าที่สูงกว่าเนื่องจากรวมหลายปัจจัย

ข้อจำกัด:
- ไม่มี ground truth data
- เป็น indirect validation

---

## 7. Discussion

โมเดลสามารถสะท้อนรูปแบบความร้อนในเมืองได้ดี โดยเฉพาะบทบาทของ vegetation และ built-up อย่างไรก็ตาม ความละเอียดของข้อมูล (MODIS) และการขาดข้อมูลเชิงลึก เช่น building height ส่งผลต่อความแม่นยำ

---

## 8. Limitations

- ไม่มีข้อมูลความสูงของอาคาร (3D urban structure)
- ไม่มีข้อมูล wind flow
- Resolution ของ MODIS ค่อนข้างหยาบ (1 km)
- Validation ไม่มี ground truth จริง

---

## 9. Conclusion

แบบจำลอง Urban Heat Risk ที่พัฒนาขึ้นสามารถใช้วิเคราะห์ความเสี่ยงความร้อนในระดับเมืองได้อย่างมีประสิทธิภาพ โดยมีความเสถียรสูงและสะท้อนรูปแบบเชิงพื้นที่ได้ดี เหมาะสำหรับการวางแผนเชิงนโยบายและการจัดการพื้นที่เมือง

---

## 10. Answers to Questions

### 1. Accuracy ของโมเดล

คาดว่าอยู่ในระดับปานกลาง (~70–80%) เนื่องจากใช้ proxy validation และไม่มี ground truth แต่มีความสอดคล้องกับหลักการทางวิทยาศาสตร์

---

### 2. ปัจจัยที่ไม่ได้ใส่

- Building height  
- Population density  
- Wind speed  
- Surface materials  

ไม่ได้ใส่เนื่องจากข้อจำกัดของข้อมูลใน GEE และความซับซ้อนของโมเดล

---

### 3. Scale ที่เหมาะสม

เหมาะกับระดับ **เมือง (city scale)**  
เนื่องจาก
- resolution ของข้อมูล (MODIS)
- ปัจจัยที่ใช้เป็น macro-scale

---

### 4. การอัปเดตโมเดล

ต้องทำซ้ำ
- ดึงข้อมูลใหม่ (LST, Sentinel-2)
- Normalize

ไม่ต้องเปลี่ยน
- สมการโมเดล
- Weight
