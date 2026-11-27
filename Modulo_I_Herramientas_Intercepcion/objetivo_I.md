## Endpoints Detectados
A través de la interceptación con Burp Suite se detectaron los siguientes endpoints activos:

1. **`POST /src/ctl/enviar_mercancia.ctl.php`**
   - **Host:** `https://chl-51d6c231-a2ed-4...`
   - **IP del Servidor:** `172.67.183.171`
   - **Método:** `POST`
   - **Descripción:** Endpoint utilizado por el aplicativo para procesar y registrar el envío de mercancías. Es la petición crítica del laboratorio y el principal objetivo para la interceptación.

2. **`POST /cdn-cgi/rum?`**
   - **Host:** `https://chl-51d6c231-a2ed-4...`
   - **IP del Servidor:** `172.67.183.171`
   - **Método:** `POST`
   - **Descripción:** Endpoint de telemetría de Cloudflare (*Real User Monitoring* - RUM). Captura métricas de rendimiento del navegador y de la red, ajeno a la lógica de negocio del laboratorio.

## Explicación de la Interceptación
Para interceptar y detener la petición, se configuró el navegador para enrutar el tráfico a través del proxy local `127.0.0.1:8080` de Burp Suite, instalando previamente el certificado CA de la herramienta para permitir la inspección de tráfico HTTPS (SSL/TLS). 

La captura en el *HTTP History* demuestra que las peticiones `POST` (especialmente el envío de mercancía) fueron capturadas en tránsito antes de llegar al servidor de destino.
