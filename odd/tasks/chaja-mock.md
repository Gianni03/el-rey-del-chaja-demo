# Feature: chaja-mock — Mock navegable vendible

Objetivo: mock HTML estático del recorrido completo para mostrar al cliente y vender Fase 1.
Problema: la propuesta en texto no vende; con algo clickeable sí.
Alcance autorizado: solo mock en `mock/` con fotos hotlinkeadas de la web actual. Sin backend, sin romper nada existente.

## Route

- Ruta: delegated direct (writer trigger: 5 archivos no triviales).
- TDD: no aplica (mock visual sin lógica). Checks: abrir index.html y navegar sin errores 404 locales.

## Tasks

- [x] T1: `mock/index.html` — 7 bloques (entregado R1).
- [x] T2: `mock/admin.html` — inputs + fechas (entregado R1).
- [x] T3: `mock/tv1.html` — promo + QR (entregado R1, QR se elimina en R2).
- [x] T4: `mock/tv2.html` — 4 cuadrantes (entregado R1).
- [x] T5: `mock/tv3.html` — 4 cuadrantes (entregado R1).
- [x] T6: verificación R1 — 6 archivos existen, cero WhatsApp, refs OK. Sin git → sin commit.
- [x] T7 (R2): entrada sin imagen — cartel tipográfico gigante "EL REY DEL CHAJÁ" con encendido neón CSS; botones Entrar/Saltar chicos abajo a la derecha.
- [x] T8 (R2): grilla tortas a 2 columnas, fotos más grandes; copy packaging: envueltas con fajas de cartón (NO cajas — corregir donde diga caja).
- [x] T9 (R2): promo protagonista con foto de las dos tortas, banda que llame la atención.
- [x] T10 (R2): paleta marca rojo anaranjado + amarillo en todo el mock (web + admin + TVs).
- [x] T11 (R2): admin suma pestaña Analytics "Qué miran" con datos de ejemplo (ranking tortas, clics llamar, horario pico) + nota de que en producción viene de GA4 Data API.
- [x] T12 (R2): tv1 sin QR + logo marca; shimmer en título + precio + borde.
- [x] T13 (R2): tv2/tv3 postres más grandes + shimmer en nombre, precio y borde de cada cuadrante.
- [x] T14 (R2): verificación R2 padre OK — sin WhatsApp, sin "caja", sin QR en tv1, paleta marca presente. Check visual en browser pendiente (no hay motor de render acá).
- [x] T15 (R3): entrada cartel lo más grande posible (clamp agresivo, ocupa viewport).
- [x] T16 (R3): shimmer TVs como líquido lento que va y viene, sube/baja intensidad, tiempos desincronizados por elemento.
- [x] T17 (R3): verificación R3 padre OK — cartel clamp(4rem,18vw,16rem) presente, keyframes líquido presentes, cero WhatsApp. Sensación visual pendiente de ojo en browser.
- [x] T18: `index.html` hub demo en raíz ("Nueva web") con links a web, admin y 3 TVs.
- [x] T19: repo git local + commit inicial del mock (`be807d4`). Fotos WhatsApp fuente quedan fuera del repo (untracked, material de referencia).
- [x] T20 (R4): polish mobile web + admin (tap targets, modal full-screen, inputs sin zoom iOS, mapa responsive, cartel 360px sin overflow).
- [x] T21 (R4): TVs en celu con experiencia landscape — portrait muestra aviso "girá el celu" + preview, landscape muestra layout real escalado.
- [x] T22 (R4): verificación R4 padre OK — solo mock/ tocado, sin WhatsApp, orientation queries presentes. Push a Pages (`ad2684d`). Check visual en celu real pendiente del usuario.
- [x] T23 (R5): revertir aviso "girá el celu" + preview en tv1/2/3 — forzar 2 filas × 2 columnas full-width en mobile, vertical u horizontal.
- [x] T24 (R5): web mobile estilo app — logo arriba (header sticky), navegación en barra fija abajo.
- [x] T25 (R5): verificación R5 padre OK — revert completo (sin rastros aviso/orientation), sin WhatsApp. Push a Pages. Visual en celu pendiente del usuario.

## Datos reales a usar

Precios: Chajá 28k/42k, Zingarella 25k, Lemon 26k, Mousse/Selva/Bocado/Reina 28k/42k, Mixta 2kg 48k, promo 48k. Ingredientes e historia en PROPUESTA. Fotos: hotlink framerusercontent (ver lista en hilo), solo para mock.

## Progreso

- 2026-10-02: documento creado, delegado a writer único.
- 2026-10-02: writer entregó 6 archivos en mock/ (index, admin, tv1-3, style.css). Verificación padre: existen los 6, cero menciones WhatsApp, refs locales OK. Sin repo git → sin work-unit commit (no hay rama). Dirección/horarios de ejemplo pendientes de confirmar con cliente. Fotos hotlinkeadas sin mapear 1:1 (Framer JS).
