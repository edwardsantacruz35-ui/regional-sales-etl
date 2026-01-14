📊 Sistema Maestro de Inteligencia de Ventas (ETL Regional V120)

📋 Descripción del Proyecto

Motor de procesamiento de datos (ETL) desarrollado en Google Apps Script para consolidar la operación comercial de una corporación multinacional con presencia en Venezuela, Colombia y Guatemala.

El sistema resuelve el problema de la fragmentación de datos ingesta reportes crudos de facturación de múltiples fuentes (ERPs locales), normaliza monedas a una divisa común (USD), clasifica automáticamente la tipología de cliente (B2B/B2C) mediante algoritmos heurísticos y distribuye un Reporte Ejecutivo HTML con KPIs visuales directamente al correo electrónico de la gerencia.

Impacto: Reducción del tiempo de consolidación de reportes de horas a minutos, garantizando visibilidad diaria del 100% de la facturación regional.

⚙️ Arquitectura del Pipeline

El script ejecutarReconstruccion() orquesta el siguiente flujo de datos automatizado:

Extracción (Inputs):

Lee hojas de carga cruda (Input_VE, Input_CO, Input_GU) provenientes de exportaciones de ERPs diversos (Siigo, Odoo).

Valida la integridad de las hojas requeridas antes de procesar.

Transformación (Lógica de Negocio):

Algoritmo de Clasificación B2B/B2C: Función determinarTipoCliente que utiliza Regex para buscar patrones en los nombres de los clientes. Detecta sufijos legales (C.A, S.A, LLC) y palabras clave (Agencia, Alcaldía, Hospital) para segmentar la cartera automáticamente sin intervención manual.

Normalización Monetaria: Conversión dinámica a USD consultando una matriz histórica de tasas mensual por país (obtenerTasasPorPais).

Mapeo de Productos: Detección de línea de negocio (Open Network, Closed Network, Rental) basada en el análisis de cuentas contables.

Gestión de Facturas Mixtas: Lógica avanzada para distribuir proporcionalmente el monto de una factura "padre" entre múltiples productos "hijos" si es necesario.

Carga (Outputs):

Genera hojas maestras limpias (Data VE, Data CO, Data GU) estandarizadas y formateadas para consumo de herramientas de BI o análisis directo.

Visualización (Reporting):

Dashboard en Hoja: Generación de tablas "Top 5 Clientes" usando inyección de SQL (QUERY) de Google Sheets y gráficas de tendencia mensual (SPARKLINE) incrustadas en celdas.

Email Dashboard: Envío de un Email HTML responsivo con diseño "Slim" (tablas compactas), semáforo de estado (Header Rojo/Verde según errores) y resumen ejecutivo por unidad de negocio.

🛠 Stack Tecnológico

Lenguaje: JavaScript (Google Apps Script V8).

Frontend: HTML5/CSS Inline para correos y Pop-ups (HtmlService).

Datos: Google Sheets API, Regex para minería de texto.

Visualización: Sparklines, Formato Condicional programático y Google Query Language.

🚀 Snippet Destacado: Clasificación Heurística

Este fragmento muestra cómo el sistema decide si un cliente es empresa o persona basándose en su nombre fiscal:

function determinarTipoCliente(cliente, rif) {
  let nombreUpper = String(cliente).trim().toUpperCase();
  
  // 1. Búsqueda por palabras clave B2B (Agencia, Grupo, Sociedad...)
  if (REGLAS.B2B_KEYWORDS.some(k => nombreUpper.includes(k))) return "B2B";
  
  // 2. Búsqueda por sufijos legales (C.A., S.A., LLC...)
  let esLegal = REGLAS.LEGAL_SUFFIXES.some(s => {
      let pattern = s.replace(/\./g, "\\.?"); 
      let regex = new RegExp("\\b" + pattern + "\\b", "i");
      return regex.test(nombreUpper);
  });
  
  if (esLegal) return "B2B";
  return "B2C"; // Por defecto
}


📦 Instalación y Uso

Crear una nueva Google Sheet.

Abrir Extensiones > Apps Script.

Copiar el contenido de Code.gs en el editor.

Ejecutar la función onOpen() para generar el menú personalizado "🚌 SISTEMA VENTAS" en la barra de herramientas.

Crear las hojas de Input (Input_VE, Input_CO, Input_GU) y cargar la data cruda.

Ejecutar 🚀 EJECUTAR REPORTE desde el menú personalizado.

Desarrollado por Edward Gabriel Santacruz - Especialista en Automatización Financiera & RevOps
