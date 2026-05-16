# Aigues Les Lloses — v3.0

## Instalación en VPS

```bash
# 1. Clonar o subir los archivos al VPS
scp lloses_app_v3.py requirements_lloses.txt usuario@tu-vps:/home/aigues/

# 2. En el VPS, instalar dependencias
pip install -r requirements_lloses.txt

# 3. Arrancar la app
streamlit run lloses_app_v3.py --server.port 8501 --server.address 0.0.0.0

# 4. (Opcional) Arrancar como servicio con systemd
```

## Servicio systemd (arranque automático)

Crear `/etc/systemd/system/aigues.service`:

```ini
[Unit]
Description=Aigues Les Lloses
After=network.target

[Service]
User=aigues
WorkingDirectory=/home/aigues
ExecStart=/usr/local/bin/streamlit run lloses_app_v3.py --server.port 8501 --server.address 0.0.0.0 --server.headless true
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
systemctl enable aigues
systemctl start aigues
```

## Importar vecinos desde CSV

El CSV debe tener estas columnas (solo `nombre` es obligatoria):

```
nombre,direccion,email,telefono,iban
Joan Puig,Carrer Major 1,joan@exemple.com,600111001,ES00 0000
Maria Sala,Carrer Major 3,maria@exemple.com,,
```

- Si el vecino ya existe (mismo nombre), se actualizan sus datos.
- Se crea automáticamente un usuario con contraseña `vecino123` (cámbiala).

## Configuración de email

Solo necesitas el email desde el que envías y su contraseña.
La app detecta el servidor SMTP automáticamente según el dominio:

| Proveedor | Configuración automática |
|---|---|
| Gmail | smtp.gmail.com:587 STARTTLS |
| Outlook/Hotmail | smtp.office365.com:587 |
| Yahoo | smtp.mail.yahoo.com:587 |
| iCloud | smtp.mail.me.com:587 |
| OVH | ssl0.ovh.net:465 SSL |
| Otros | smtp.{dominio}:587 |

**Gmail:** En tu cuenta Google → Seguridad → Contraseñas de aplicación → genera una para "Correo".

## Facturas guardadas en el servidor

Cada vez que se envía una factura por email, el PDF se guarda automáticamente en:
```
data/facturas/YYYY/MM/nombre-mes_Nombre_Vecino.pdf
```

Desde Facturación → Ver facturas guardadas puedes ver y descargar cualquier factura histórica.

## Credenciales por defecto

| Usuario | Contraseña | Rol |
|---|---|---|
| admin | admin123 | Administrador |
| joan.puig | vecino123 | Vecino demo |

**Cambia las contraseñas desde Vecinos → Usuarios.**
