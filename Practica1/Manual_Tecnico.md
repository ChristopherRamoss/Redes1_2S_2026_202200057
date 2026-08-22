# Manual Técnico - Infraestructura Física de Red
**Empresa:** QuetzalDev S.A.  
**Autor:** Christopher Miguel Angel Ramos Ascencio - 202200057
**Curso:** Redes de Computadoras  

---

### 1. Ubicación y Justificación del MDF

* **Ubicación:** Cuarto cerrado en la esquina de Dirección General
* **Justificación:** Centraliza la distancia hacia los departamentos de mayor tráfico sin superar el límite de 90 m de cableado horizontal Aporta seguridad física por estar en un área restringida

---

### 2. Inventario de Equipos

| Cantidad | Dispositivo / Elemento | Especificación | Función |
| :---: | :--- | :--- | :--- |
| **1** | Switch Principal (Core) | Cisco 24P Gigabit | Concentrador central de la red (MDF) |
| **8** | Switches Departamentales | Cisco 16P/24P Gigabit | Conexión de hosts por área |
| **1** | Patch Panel | 24 Puertos Cat6 | Remate del cableado troncal en MDF |
| **1** | Rack de Comunicaciones | Rack de Piso 42U | Alojamiento de equipo activo, pasivo y UPS |
| **42** | Tomas de Red | Jacks Keystone Cat6 | Puntos de red de pared (RJ-45) |
| **1** | UPS | APC Smart-UPS 3000VA | Respaldo eléctrico centralizado |

---

### 3. Justificación de Topologías Físicas

* **Jerarquía Global:** **Estrella Jerárquica** (Switches departamentales conectados punto a punto hacia el MDF)
* **Red Interna:** **Estrella Simple** (Hosts conectados directamente a su switch de área)

| Departamento | Hosts | Topología | Justificación |
| :--- | :---: | :---: | :--- |
| **Recepción** | 3 PCs, 1 Laptop, 1 Servidor | Estrella | Conexión directa y aislamiento de fallas |
| **Recursos Humanos** | 8 PCs | Estrella | Mantiene independencia de puertos |
| **Legal** | 4 PCs | Estrella | Cableado simple de bajo costo |
| **Capacitación** | 10 PCs | Estrella | Previene caídas masivas por falla de un cable |
| **Diseño e Innovación**| 7 PCs, 1 Servidor | Estrella | Ancho de banda dedicado para archivos pesados |
| **Dirección General** | 4 Laptops, 1 PC | Estrella | Alta disponibilidad mediante switch propio (`SW-DIRECCION`) |
| **Backend** | 6 Laptops, 1 Servidor | Estrella | Flujo constante directo al backbone |
| **Data Center** | 3 Servidores | Estrella | Switch local (`SW-DC`) para respuesta rápida |

---

### 4. Medios de Transmisión y Estimación de Materiales

* **Cableado Horizontal (Switch a PC):** UTP Cat6 (Hasta 1 Gbps / 10 Gbps < 55m)
* **Cableado Troncal (Switch a MDF):** UTP Cat6a / Opciones en Fibra OM3
* **Cálculo de Bobinas:**
  * Metraje total estimado (Horizontal + Troncal + 15% desperdicio): **1,437 metros**
  * Total de bobinas (305m c/u): $\frac{1437}{305} = 4.71 \longrightarrow$ **5 Bobinas UTP Cat6**

---

### 5. Canalización, Rack y Respaldo Eléctrico

* **Canalización:** Escalerilla metálica abierta de rejilla por pasillos Otorga ventilación, inspección rápida y fácil mantenimiento
* **Gabinete:** Rack de piso cerrado de 42U en MDF para albergar Switch Core, Patch Panel, organizadores y UPS con margen de crecimiento
* **Respaldo UPS:** Consumo total de equipos activos estimado en ~440W Se selecciona una **UPS APC Smart-UPS 3000VA (2700W)** que provee 45-60 min de autonomía

---

### 6. Estándares de Ponchado y Disposición de Pines

#### A. Cable Directo (Straight-Through: T568B - T568B)
* **Uso:** Cableado horizontal (Hosts a Switch) y troncal (Switches a Patch Panel)

| Pin | Extremo 1 (T568B) | Extremo 2 (T568B) | Función |
| :---: | :--- | :--- | :---: |
| 1 | Blanco / Naranja | Blanco / Naranja | TX+ |
| 2 | Naranja | Naranja | TX- |
| 3 | Blanco / Verde | Blanco / Verde | RX+ |
| 4 | Azul | Azul | PoE / No usado |
| 5 | Blanco / Azul | Blanco / Azul | PoE / No usado |
| 6 | Verde | Verde | RX- |
| 7 | Blanco / Marrón | Blanco / Marrón | No usado |
| 8 | Marrón | Marrón | No usado |

#### B. Cable Cruzado (Crossover: T568A - T568B)
* **Uso:** Demostración teórica de enlace directo Switch a Switch (MDI-X a MDI-X)

| Pin | Extremo 1 (T568A) | Extremo 2 (T568B) | Cruce de Señas |
| :---: | :--- | :--- | :---: |
| 1 | Blanco / Verde | Blanco / Naranja | Cruzado con Pin 3 (RX+) |
| 2 | Verde | Naranja | Cruzado con Pin 6 (RX-) |
| 3 | Blanco / Naranja | Blanco / Verde | Cruzado con Pin 1 (TX+) |
| 4 | Azul | Azul | Directo |
| 5 | Blanco / Azul | Blanco / Azul | Directo |
| 6 | Naranja | Verde | Cruzado con Pin 2 (TX-) |
| 7 | Blanco / Marrón | Blanco / Marrón | Directo |
| 8 | Marrón | Marrón | Directo |

---

### 7. Tabla de Etiquetado de Cables

| Identificador | Origen | Destino | Tipo de Cable | Estándar |
| :--- | :--- | :--- | :--- | :---: |
| **MDF-RECEPCION** | Patch Panel (MDF) | SW-RECEPCION | Troncal Cat6 | T568B |
| **MDF-LEGAL** | Patch Panel (MDF) | SW-LEGAL | Troncal Cat6 | T568B |
| **MDF-CAPACITACION** | Patch Panel (MDF) | SW-CAPACITACION | Troncal Cat6 | T568B |
| **MDF-RRHH** | Patch Panel (MDF) | SW-RRHH | Troncal Cat6 | T568B |
| **MDF-DISENO** | Patch Panel (MDF) | SW-DISENO | Troncal Cat6 | T568B |
| **MDF-BACKEND** | Patch Panel (MDF) | SW-BACKEND | Troncal Cat6 | T568B |
| **MDF-DATACENTER** | Patch Panel (MDF) | SW-DATACENTER | Troncal Cat6a | T568A / T568B |
| **Recepcion-PR01** | SW-RECEPCION | PC-01 Recepción | Horizontal Cat6 | T568B |
| **Capacitacion-PR05**| SW-CAPACITACION | PC-05 Capacitación | Horizontal Cat6 | T568B |
| **Backend-PR02** | SW-BACKEND | Laptop-02 Backend | Horizontal Cat6 | T568B |

---

### 8. Comparación con el Estándar ANSI/TIA/EIA-606

| Criterio | Esquema Simplificado (Práctica) | Estándar TIA/EIA-606 |
| :--- | :--- | :--- |
| **Identificador** | Basado en departamento y correlativo (`Legal-PR01`) | Coordenada tridimensional única: `[Edificio]-[Piso].[Cuarto]-[Rack].[Panel]-[Puerto]` (`1A-C01-R01.P02-12`) |
| **Codificación de Color** | Diferenciación visual de mapas | Colores normados en etiquetas físicas (Azul=Horizontal, Blanco=Backbone, Púrpura=Equipos Activos). |
| **Documentación** | Registro estático simple | Mapeo obligatorio de rutas, tuberías y paneles en software DCIM/bases de datos. |

* **Diferencias concretas:** 
  1. El esquema simple identifica el departamento, mientras que TIA/EIA-606 indica la posición física exacta del puerto en el rack
  2. TIA/EIA-606 impone una codificación por color estándar obligatoria para las terminaciones, no solo para líneas del plano.
* **Justificación en Data Centers reales:** Permite a cualquier técnico diagnosticar y reemplazar líneas en segundos sin necesidad de rastrear cables manualmente ni realizar pruebas de continuidad, reduciendo costos de mantenimiento y tiempos de parada.

---

### 9. Flujo de Conexión End-to-End

**Trayecto:** 

[Laptop o PC escritorio - Backend]
(UTP Cat6 / T568B)

[Toma RJ-45: Backend-PR02]
(Cableado Horizontal por pared)

[Switch Departamental: SW-BACKEND]
(Enlace Troncal por pasillo: MDF-Backend)

[Patch Panel MDF: PP-MDF-01]
(Patch Cord UTP Cat6)

[Switch Principal / Core: SW-CORE-MDF]
(Enlace Troncal de Alta Velocidad: MDF-DataCenter)

[Switch Data Center: SW-DATACENTER]
(Patch Cord Cat6a)
[Servidor N1 - Data Center]

---

### 10. Presupuesto Estimado

| Cantidad | Equipo / Material | P. Unitario (USD) | Total (USD) |
| :---: | :--- | :---: | :---: |
| 1 | Switch Core Cisco 24P Gigabit | $1,200.00 | $1,200.00 |
| 8 | Switches Departamentales 16P/24P | $250.00 | $2,000.00 |
| 5 | Bobinas de Cable UTP Cat6 (305m) | $140.00 | $700.00 |
| 1 | Patch Panel 24P Cat6 | $60.00 | $60.00 |
| 1 | Rack de Piso 42U | $800.00 | $800.00 |
| 1 | UPS APC 3000VA | $1,100.00 | $1,100.00 |
| 42 | Jacks Keystone Cat6 + Faceplates | $3.50 | $147.00 |
| 60m | Escalerilla Metálica + Accesorios | $12.00/m | $720.00 |
| **TOTAL**| | | **$6,727.00 USD** |