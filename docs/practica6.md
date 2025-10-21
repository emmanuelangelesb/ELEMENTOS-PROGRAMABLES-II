<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>📚 Práctica 7 – Control Web de LEDs con ESP32</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root{
      --bg:#0f1115; --card:#151822; --muted:#99a1b3; --text:#e8eaf0; --acc:#7aa2f7; --ok:#34d399; --warn:#f59e0b; --danger:#ef4444;
      --br:16px;
    }
    *{box-sizing:border-box}
    body{
      margin:0; padding:32px; font-family:system-ui,-apple-system,Segoe UI,Roboto,Inter,Arial,sans-serif;
      background: radial-gradient(1200px 600px at 100% -10%, #182035 0%, rgba(24,32,53,0) 60%), var(--bg); color:var(--text);
    }
    .wrap{max-width:980px; margin:0 auto}
    .card{
      background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border:1px solid rgba(255,255,255,0.08);
      border-radius:var(--br);
      padding:24px;
      box-shadow: 0 20px 60px rgba(0,0,0,0.35), inset 0 1px 0 rgba(255,255,255,0.04);
      backdrop-filter: blur(8px);
    }
    h1,h2,h3{margin:0 0 12px}
    h1{font-size:clamp(24px,3vw,36px)}
    h2{font-size:clamp(18px,2.2vw,24px); margin-top:28px}
    p,li{color:var(--text); line-height:1.65}
    .muted{color:var(--muted)}
    .hr{height:1px; background:linear-gradient(90deg, transparent, rgba(255,255,255,0.15), transparent); margin:20px 0}
    .kvs{display:grid; grid-template-columns: 220px 1fr; gap:10px 16px}
    .kvs div{padding:8px 12px; border:1px solid rgba(255,255,255,0.08); border-radius:12px; background:rgba(255,255,255,0.03)}
    code.inline{background:rgba(255,255,255,0.07); padding:2px 6px; border-radius:8px}
    .codebox{position:relative; margin-top:10px}
    textarea.code{
      width:100%; min-height:420px; resize:vertical; padding:14px 14px 44px 14px;
      background:#0b0d13; color:#e6edf3; border:1px solid rgba(255,255,255,0.1); border-radius:14px; font-family:ui-monospace, SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;
      font-size:13.5px; line-height:1.45; tab-size:2; white-space:pre; overflow:auto;
    }
    .code-actions{
      position:absolute; right:10px; bottom:10px; display:flex; gap:8px;
    }
    .btn{
      border:1px solid rgba(255,255,255,0.15); background:rgba(255,255,255,0.06); color:var(--text);
      padding:8px 12px; border-radius:10px; cursor:pointer; font-weight:600; transition: .15s ease;
    }
    .btn:hover{transform: translateY(-1px); background:rgba(255,255,255,0.1)}
    .btn.copy{border-color:rgba(122,162,247,0.5)}
    .grid{
      display:grid; gap:12px; grid-template-columns: repeat(auto-fit, minmax(240px,1fr));
    }
    .badge{display:inline-block; padding:4px 8px; border-radius:999px; font-size:12px; border:1px solid rgba(255,255,255,0.12); background:rgba(255,255,255,0.05)}
    .routes table{width:100%; border-collapse:collapse; overflow:hidden; border-radius:12px; border:1px solid rgba(255,255,255,0.08)}
    .routes th,.routes td{padding:10px 12px; text-align:left; border-top:1px solid rgba(255,255,255,0.06)}
    .routes thead th{background:rgba(255,255,255,0.06); border-top:none}
    .callout{
      border:1px solid rgba(255,255,255,0.12); border-left:6px solid var(--acc);
      background:rgba(122,162,247,0.08); padding:14px 16px; border-radius:12px;
    }
    .gallery{
      display:grid; grid-template-columns: repeat(auto-fit,minmax(220px,1fr)); gap:12px; margin-top:10px;
    }
    .ph{
      height:160px; border-radius:12px; border:1px dashed rgba(255,255,255,0.18); display:flex; align-items:center; justify-content:center; color:var(--muted);
      background: repeating-linear-gradient(45deg, rgba(255,255,255,0.04), rgba(255,255,255,0.04) 10px, rgba(255,255,255,0.02) 10px, rgba(255,255,255,0.02) 20px);
    }
    .small{font-size:13px}
    .list-tight li{margin:4px 0}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <h1>📚 Práctica 7 – Control Web de LEDs con ESP32</h1>
      <p class="muted">En esta práctica se construye una página web embebida en el ESP32 para encender y apagar tres LEDs mediante rutas HTTP.</p>
      <div class="hr"></div>

      <!-- 1) Resumen -->
      <h2>1) Resumen 📌</h2>
      <div class="kvs">
        <div><b>Nombre del proyecto</b></div><div>Control Web de LEDs con ESP32</div>
        <div><b>Autor(es)</b></div><div>Jesús Cerezo</div>
        <div><b>Curso / Asignatura</b></div><div>Elementos Programables II</div>
        <div><b>Fecha</b></div><div>21/10/25</div>
        <div><b>Descripción breve</b></div>
        <div>El ESP32 monta un servidor HTTP con rutas <code class="inline">/on</code>, <code class="inline">/off</code> (y variantes) para controlar digitalmente 3 salidas. La página HTML refleja el estado y ofrece botones de acción.</div>
      </div>

      <div class="hr"></div>

      <!-- 2) Objetivos -->
      <h2>2) Objetivos 🎯</h2>
      <ul class="list-tight">
        <li><b>General:</b> Implementar una interfaz web ligera que permita controlar múltiples GPIO del ESP32 y visualizar su estado.</li>
        <li><b>Específicos:</b>
          <ul>
            <li>Configurar el ESP32 como cliente Wi-Fi y publicar su IP local.</li>
            <li>Montar un <i>WebServer</i> con rutas REST simples para encendido/apagado.</li>
            <li>Actualizar dinámicamente el HTML para reflejar el estado de cada LED.</li>
          </ul>
        </li>
      </ul>

      <div class="hr"></div>

      <!-- 3) Código -->
      <h2>3) Código ⌨️</h2>
      <p class="small muted">Botón para copiar completo. El recuadro es editable si quieres corregir o anotar.</p>
      <div class="codebox">
        <textarea id="code" class="code" spellcheck="false">
// ====== Código: Servidor Web ESP32 (3 LEDs) ======
#include &lt;WiFi.h&gt;
#include &lt;WebServer.h&gt;
 
// Replace with your network credentials
const char* ssid = "iPhone";
const char* password = "karennajera";
 
// Create a web server on port 80
WebServer server(80);
 
const int ledPin = 20;  // Onboard LED for many ESP32 boards
const int ledPin1 = 19;
const int ledPin2 = 21;
String ledState = "OFF";
String ledState1 = "OFF";
String ledState2 = "OFF";
 
// Funcion Base
void handleRoot() {
  /*
            &lt;!DOCTYPE html&gt;
            &lt;html&gt;
              &lt;head&gt;
                &lt;meta charset="UTF-8"&gt;
                &lt;title&gt;ESP32 LED Control&lt;/title&gt;
              &lt;/head&gt;
              &lt;body&gt;
                &lt;h1&gt;ESP32 LED Control&lt;/h1&gt;
                &lt;p&gt;LED is OFF&lt;/p&gt;
                &lt;a href="/on"&gt;&lt;button&gt;Turn On&lt;/button&gt;&lt;/a&gt;
              &lt;/body&gt;
            &lt;/html&gt;
  */
  String html = "&lt;!DOCTYPE html&gt;&lt;html&gt;&lt;head&gt;&lt;meta charset='UTF-8'&gt;&lt;title&gt;ESP32 LED Control&lt;/title&gt;&lt;meta name='viewport' content='width=device-width, initial-scale=1'&gt;&lt;style&gt;body{font-family:system-ui;max-width:680px;margin:0 auto;padding:24px;background:#0b0d13;color:#e6edf3}a button{padding:8px 12px;border-radius:10px;border:1px solid rgba(255,255,255,.2);background:rgba(255,255,255,.06);color:#e6edf3;cursor:pointer}&lt;/style&gt;&lt;/head&gt;&lt;body&gt;";
  html += "&lt;h1&gt;ESP32 LED Control&lt;/h1&gt;";
  html += "&lt;p&gt;LED0 está " + ledState + " | LED1 está " + ledState1 + " | LED2 está " + ledState2 + "&lt;/p&gt;";

  // Controles LED 0
  if (ledState == "OFF")  html += "&lt;a href=&quot;/on&quot;&gt;&lt;button&gt;Encender LED0&lt;/button&gt;&lt;/a&gt; ";
  else                    html += "&lt;a href=&quot;/off&quot;&gt;&lt;button&gt;Apagar LED0&lt;/button&gt;&lt;/a&gt; ";

  // Controles LED 1
  if (ledState1 == "OFF") html += "&lt;a href=&quot;/on1&quot;&gt;&lt;button&gt;Encender LED1&lt;/button&gt;&lt;/a&gt; ";
  else                    html += "&lt;a href=&quot;/off1&quot;&gt;&lt;button&gt;Apagar LED1&lt;/button&gt;&lt;/a&gt; ";

  // Controles LED 2
  if (ledState2 == "OFF") html += "&lt;a href=&quot;/on2&quot;&gt;&lt;button&gt;Encender LED2&lt;/button&gt;&lt;/a&gt; ";
  else                    html += "&lt;a href=&quot;/off2&quot;&gt;&lt;button&gt;Apagar LED2&lt;/button&gt;&lt;/a&gt; ";

  html += "&lt;/body&gt;&lt;/html&gt;";
  server.send(200, "text/html", html);
}
 
// Funcion encender/apagar LED0
void handleOn()  { digitalWrite(ledPin,  HIGH); ledState  = "ON";  handleRoot(); }
void handleOff() { digitalWrite(ledPin,  LOW);  ledState  = "OFF"; handleRoot(); }
// LED1
void handleOn1()  { digitalWrite(ledPin1, HIGH); ledState1 = "ON";  handleRoot(); }
void handleOff1() { digitalWrite(ledPin1, LOW);  ledState1 = "OFF"; handleRoot(); }
// LED2
void handleOn2()  { digitalWrite(ledPin2, HIGH); ledState2 = "ON";  handleRoot(); }
void handleOff2() { digitalWrite(ledPin2, LOW);  ledState2 = "OFF"; handleRoot(); }
 
void setup() {
  Serial.begin(115200);
  pinMode(ledPin,  OUTPUT); digitalWrite(ledPin,  LOW);
  pinMode(ledPin1, OUTPUT); digitalWrite(ledPin1, LOW);
  pinMode(ledPin2, OUTPUT); digitalWrite(ledPin2, LOW);
 
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) { delay(500); Serial.print("."); }
  Serial.println("\nWiFi connected");
  Serial.println(WiFi.localIP()); // Anota esta IP para entrar desde el navegador
 
  // Rutas
  server.on("/",   handleRoot);
  server.on("/on", handleOn);
  server.on("/off",handleOff);
  server.on("/on1",handleOn1);
  server.on("/off1",handleOff1);
  server.on("/on2",handleOn2);
  server.on("/off2",handleOff2);
 
  server.begin();
  Serial.println("Server started");
}
 
void loop() {
  server.handleClient();
}
// ====== Fin del código ======
        </textarea>
        <div class="code-actions">
          <button class="btn copy" onclick="copyCode()">📋 Copiar</button>
          <button class="btn" onclick="toggleWrap()">↔ Ajuste de línea</button>
        </div>
      </div>

      <div class="hr"></div>

      <!-- Resumen del código -->
      <h2>Resumen del código 🔎</h2>
      <ul class="list-tight">
        <li>Se crean tres salidas: <code class="inline">GPIO 20, 19, 21</code> (ajústalas a tu placa si es necesario).</li>
        <li>El servidor HTTP expone rutas: <code class="inline">/on</code>, <code class="inline">/off</code>, <code class="inline">/on1</code>, <code class="inline">/off1</code>, <code class="inline">/on2</code>, <code class="inline">/off2</code>.</li>
        <li><code class="inline">handleRoot()</code> genera el HTML dinámico que muestra el estado y los botones.</li>
        <li>Tras cada acción, se llama de nuevo a <code class="inline">handleRoot()</code> para refrescar la vista con el estado actualizado.</li>
      </ul>

      <div class="routes">
        <h3>Rutas útiles 🌐</h3>
        <table>
          <thead><tr><th>Ruta</th><th>Acción</th></tr></thead>
          <tbody>
            <tr><td><code>/</code></td><td>Página principal con botones y estado</td></tr>
            <tr><td><code>/on</code> / <code>/off</code></td><td>Enciende / apaga LED0 (GPIO 20)</td></tr>
            <tr><td><code>/on1</code> / <code>/off1</code></td><td>Enciende / apaga LED1 (GPIO 19)</td></tr>
            <tr><td><code>/on2</code> / <code>/off2</code></td><td>Enciende / apaga LED2 (GPIO 21)</td></tr>
          </tbody>
        </table>
      </div>

      <div class="hr"></div>

      <!-- Evidencias -->
      <h2>Evidencias del resultado final 📸</h2>
      <p>Inserta capturas: la IP en el monitor serie, la página abierta en el navegador y los LEDs encendiendo/apagando.</p>
      <div class="gallery">
        <div class="ph">Agregar captura 1 (IP en Serial)</div>
        <div class="ph">Agregar captura 2 (Página /)</div>
        <div class="ph">Agregar captura 3 (LEDs ON/OFF)</div>
      </div>

      <div class="hr"></div>

      <!-- Extra -->
      <h2>Notas y curiosidades 🧠</h2>
      <ul class="list-tight">
        <li><span class="badge">Tip</span> Si tu placa no tiene los GPIO 19/20/21 disponibles, mapea a otros pines y actualiza las constantes.</li>
        <li><span class="badge">Mejora</span> Puedes añadir sliders con AJAX para “dimmer” PWM y actualizar sin recargar.</li>
        <li><span class="badge">Depuración</span> Si no abre la página, verifica la IP en el monitor serie y que tu PC/móvil esté en la misma red.</li>
      </ul>

      <div class="hr"></div>

      <div class="callout">
        <b>Cómo probar:</b> Compila y sube el sketch. Abre el Monitor Serie y copia la IP que imprime (p. ej. <code class="inline">192.168.1.75</code>). En un navegador, entra a <code class="inline">http://&lt;IP&gt;/</code> y usa los botones.
      </div>
    </div>
  </div>

  <script>
    function copyCode(){
      const ta = document.getElementById('code');
      ta.select(); ta.setSelectionRange(0, 999999);
      const ok = document.execCommand('copy');
      const old = event.target.textContent;
      event.target.textContent = ok ? '✅ Copiado' : '⚠ No se pudo copiar';
      setTimeout(() => event.target.textContent = old, 1200);
    }
    function toggleWrap(){
      const ta = document.getElementById('code');
      const s = getComputedStyle(ta).whiteSpace;
      ta.style.whiteSpace = (s === 'pre') ? 'pre-wrap' : 'pre';
    }
  </script>
</body>
</html>
