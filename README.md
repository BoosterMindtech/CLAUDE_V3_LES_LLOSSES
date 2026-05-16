# Aigues Les Lloses — v3.0

App de gestión de consumos de agua, facturación y envío de emails.
Diseñada para ejecutarse en un VPS propio.

---

## Archivos necesarios

```
tu-servidor/
├── lloses_app_v3.py       <- la app
├── requirements.txt       <- dependencias (renombrar)
└── data/facturas/         <- se crea automaticamente
```

---

## Instalación en VPS

```bash
pip install -r requirements.txt
streamlit run lloses_app_v3.py --server.port 8501 --server.address 0.0.0.0 --server.headless true
```

Accede en: `http://tu-ip-vps:8501`

---

## Arranque automático con systemd

`/etc/systemd/system/aigues.service`:

```ini
[Unit]
Description=Aigues Les Lloses
After=network.target

[Service]
User=aigues
WorkingDirectory=/home/aigues
ExecStart=/usr/local/bin/streamlit run lloses_app_v3.py --server.port 8501 --server.address 0.0.0.0 --server.headless true
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
systemctl enable aigues
systemctl start aigues
```

---

## Credenciales por defecto

| Usuario   | Contraseña | Rol           |
|-----------|------------|---------------|
| admin     | admin123   | Administrador |
| joan.puig | vecino123  | Vecino (demo) |

Cambia las contraseñas desde Vecinos -> Usuarios en cuanto arranques.

---

## Importar vecinos desde CSV

Ve a Vecinos -> Importar CSV. Columnas aceptadas (solo nombre es obligatorio):

```
nombre,direccion,email,telefono,iban
Joan Puig,Carrer Major 1,joan@exemple.com,600111001,ES00 0000
Maria Sala,Carrer Major 3,maria@exemple.com,,
```

- Descarga la plantilla desde la misma pestaña.
- Si el vecino ya existe (mismo nombre) se actualizan sus datos.
- Se crea automaticamente usuario con contraseña vecino123.

---

## Configuracion de email

Ve a Facturacion -> Configuracion de email.

Pon solo el email remitente y su contraseña. La app detecta el servidor SMTP
automaticamente para los proveedores mas comunes:

| Proveedor       | Automatico                   |
|-----------------|------------------------------|
| Gmail           | smtp.gmail.com:587           |
| Outlook/Hotmail | smtp.office365.com:587       |
| Yahoo           | smtp.mail.yahoo.com:587      |
| OVH             | ssl0.ovh.net:465 SSL         |

Gmail: necesitas Contraseña de aplicacion (no tu contraseña normal).
Cuenta Google -> Seguridad -> Verificacion en 2 pasos -> Contraseñas de aplicacion.

### Error CERTIFICATE_VERIFY_FAILED (Hostalia, hosting propio)

Si ves este error, despliega "Configuracion SMTP avanzada" y rellena:

| Campo                           | Valor                    |
|---------------------------------|--------------------------|
| Servidor SMTP                   | mail.tudominio.com       |
| Puerto                          | 465                      |
| SSL                             | activado                 |
| No verificar certificado SSL    | activado (soluciona el error) |

Este error es habitual en hostings compartidos con certificado wildcard.
Pulsa Guardar configuracion email despues de rellenar.

---

## Facturas guardadas en el servidor

Cada envio guarda el PDF en:
```
data/facturas/YYYY/MM/NombreMes-YYYY_Nombre_Vecino.pdf
```

Desde Facturacion -> Ver facturas guardadas puedes consultar el historico.

---

## Backup

Configuracion -> Backup base de datos descarga el fichero lloses.db completo.

---

## Dependencias

```
streamlit>=1.32.0
pandas>=2.0.0
plotly>=5.18.0
reportlab>=4.0.0
```

SQLite viene incluido en Python.
