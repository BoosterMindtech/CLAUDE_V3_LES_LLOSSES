# Aigues Les Lloses — v3.0

App de gestión de consumos de agua, facturación y envío de emails para comunidades rurales.
Diseñada para ejecutarse en un VPS propio.

---

## Archivos necesarios en el servidor

```
/home/aigues/
├── lloses_app_v3.py    <- la app
└── requirements.txt    <- dependencias
```

La carpeta `data/facturas/` se crea automáticamente al enviar la primera factura.

---

## Instalación en VPS

```bash
# Instalar dependencias
pip install -r requirements.txt

# Arrancar la app
streamlit run lloses_app_v3.py \
  --server.port 8501 \
  --server.address 0.0.0.0 \
  --server.headless true
```

Accede desde el navegador en: `http://tu-ip-vps:8501`

---

## Arranque automático con systemd

Crea el archivo `/etc/systemd/system/aigues.service`:

```ini
[Unit]
Description=Aigues Les Lloses
After=network.target

[Service]
User=aigues
WorkingDirectory=/home/aigues
ExecStart=/usr/local/bin/streamlit run lloses_app_v3.py \
  --server.port 8501 \
  --server.address 0.0.0.0 \
  --server.headless true
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
systemctl enable aigues
systemctl start aigues
systemctl status aigues
```

---

## Credenciales por defecto

| Usuario   | Contraseña | Rol           |
|-----------|------------|---------------|
| admin     | admin123   | Administrador |
| joan.puig | vecino123  | Vecino (demo) |

**Cambia las contraseñas desde Vecinos → Usuarios en cuanto arranques.**

---

## Importar vecinos desde Excel

Ve a **Vecinos → Importar Excel**.

Columnas aceptadas (`nombre` es la única obligatoria):

| nombre | direccion | email | telefono | iban |
|--------|-----------|-------|----------|------|
| Joan Puig | Carrer Major 1 | joan@exemple.com | 600111001 | ES00 0000 |
| Maria Sala | Carrer Major 3 | maria@exemple.com | | |
| Pere Font | Carrer del Pi 2 | | | |

- Descarga la plantilla `.xlsx` desde la misma pestaña.
- Se aceptan archivos `.xlsx` (Excel moderno) y `.xls` (Excel antiguo).
- Si el vecino ya existe (mismo nombre) se actualizan sus datos.
- Se crea automáticamente un usuario con contraseña `vecino123`.

---

## Exportar listado de vecinos

Desde **Vecinos → Listado** puedes descargar el listado completo en formato Excel (`.xlsx`).

---

## Configuración de email

Ve a **Facturación → Configuración de email**.

Introduce el email remitente y su contraseña. La app detecta el servidor
SMTP automáticamente para los proveedores más comunes:

| Proveedor       | Servidor automático          |
|-----------------|------------------------------|
| Gmail           | smtp.gmail.com:587           |
| Outlook/Hotmail | smtp.office365.com:587       |
| Yahoo           | smtp.mail.yahoo.com:587      |
| iCloud          | smtp.mail.me.com:587         |
| OVH             | ssl0.ovh.net:465 SSL         |

**Gmail:** Necesitas una Contraseña de aplicación (no tu contraseña normal).
Cuenta Google → Seguridad → Verificación en 2 pasos → Contraseñas de aplicación.

### Error CERTIFICATE_VERIFY_FAILED (Hostalia, hosting propio)

Si ves este error al enviar, despliega **⚙️ Configuración SMTP avanzada** y rellena:

| Campo                        | Valor para Hostalia/hosting propio |
|------------------------------|-------------------------------------|
| Servidor SMTP                | mail.tudominio.com                  |
| Puerto                       | 465                                 |
| SSL                          | activado                            |
| No verificar certificado SSL | activado ← esto soluciona el error  |

Este error es habitual en hostings compartidos donde el certificado SSL
es wildcard y no coincide con smtp.tudominio.com.

Pulsa **Guardar configuración email** después de rellenar los campos.

---

## Facturas guardadas en el servidor

Cada envío guarda el PDF automáticamente en:

```
data/facturas/YYYY/MM/NombreMes-YYYY_Nombre_Vecino.pdf
```

Desde **Facturación → Ver facturas guardadas** puedes consultar el histórico
y descargar cualquier factura anterior.

---

## Backup de la base de datos

**Configuración → Backup base de datos** descarga el fichero `lloses.db` completo.
Guárdalo periódicamente. Contiene todos los vecinos, consumos y configuración.

---

## Dependencias

```
streamlit>=1.32.0
pandas>=2.0.0
plotly>=5.18.0
reportlab>=4.0.0
openpyxl>=3.1.0
xlrd>=2.0.1
```

SQLite viene incluido en Python, no necesita instalación separada.
