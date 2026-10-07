1. ¿Guardan todas las lecturas para siempre? Cálculo de crecimiento anual

• Respuesta: No se guardan para siempre. Las lecturas en crudo (cada 15 segundos) pierden valor con el tiempo; solo se conservan las tendencias y promedios.

• Cálculo de volumen:
	• Cada sensor genera 5,760 lecturas al día.
	• Los 20 sensores generan 115,200 lecturas al día (más de 42 millones al año).
	• Con un tamaño optimizado de 40 bytes por registro, la base de datos crecerá aproximadamente 1.57 GB al año en almacenamiento crudo.

2. ¿Cómo agregan los datos históricos sin perder capacidad de respuesta?

• Estrategia: Implementando una Política de Retención y Compactación (Roll-up / Downsampling) en una base de datos de series de tiempo (como TimescaleDB o InfluxDB).

• Estructura de capas:
	• Últimos 7 días: Datos en crudo (cada 15 segundos) para alertas y tableros en vivo.
	• De 1 a 6 meses: Datos agregados por hora (promedio, mínimos y máximos).
	• Más de 6 meses: Datos agregados por día. Las lecturas en crudo se eliminan automáticamente para mantener el sistema rápido.

3. Si el sistema se cae 10 minutos, ¿qué pasa con las lecturas?

• Problema: Se perderían exactamente 800 lecturas globales si los sensores transmiten directo por HTTP y el servidor no responde.

• Solución Arquitectónica: Se implementará un mecanismo de desacoplamiento mediante un bróker de mensajería (como MQTT o RabbitMQ). Los sensores publican en una cola; si el servidor se cae 10 minutos, los mensajes se acumulan de forma segura en la cola y se procesan en ráfaga inmediatamente cuando el sistema vuelve a estar en línea, garantizando cero pérdida de datos.