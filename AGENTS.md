# AGENTS.md — El Rey del Chajá · Pack de arranque build

> Contrato firmado(v2.3M, 2 pagos 30/70) → este archivo es la única fuente de verdad para
> desarrollar. Si algo contradice la demo o la orden de trabajo, preguntar antes de asumir.
> Demo: https://gianni03.github.io/el-rey-del-chaja-demo/ · Repo: github.com/Gianni03/el-rey-del-chaja-demo

## 1. Qué es y para quién

Pastelería tradicional de Rosario ("El Rey del Chajá"). Dueño no técnico. 8 productos fijos
que NO cambian; solo cambian precios (~cada 3 meses). Lema: "Clásico. Artesanal. Inconfundible."
Público: clientes de mostrador + jóvenes que no los conocen. Contacto SOLO teléfono fijo
4856128. **Sin WhatsApp, sin carrito, sin reservas online, sin delivery** — por decisión del
negocio (las tortas se rompen en moto; se vende como valor de calidad, no como capricho).

## 2. Stack decidido (no reabrir sin motivo)

| Capa | Decisión |
|---|---|
| Front | Astro (estático) + TypeScript + CSS vanilla con tokens (el mock ya usa esta base) |
| Hosting | Vercel plan free (repo conectado, deploy por push a `master`) |
| Datos + Auth | Supabase plan free — org y proyecto a nombre del CLIENTE (su Gmail), dev como miembro |
| Tablas | `productos` (8 filas fijas), `precios` (historial por tamaño), `promos` (incl. fechas especiales), `config` (tel, dirección, horarios) |
| Fotos | Optimizadas locales en repo (la sesión de fotos las provee el cliente) |
| Offline TVs | Service worker: shell cache-first, precios network-first con fallback a último guardado |
| Dominio | El actual del cliente; DNS apunta a Vercel (ver §8) |

## 3. Arquitectura — una sola fuente de verdad

```
/ (web) ─┐
/admin (login dueño) ─→ Supabase ─→ deploy ≈1 min ─→ TVs hacen poll 60s + caché offline
/tv?pantalla=1/2/3 ─┘ (noindex, sin links públicos, solo kiosco del local)
```

- Admin escribe → Supabase → web y TVs leen. Web y TVs nunca se editan a mano.
- TVs: mini-PC con internet, Chrome `--kiosk` una instancia por pantalla (perfil
  `--user-data-dir` separado), autostart. Ver `TVS-INSTALACION.md`.
- Keepalive anti-pausa Supabase: las TVs ya generan actividad; + GitHub Action cron
  diario con SELECT liviano. Runbook pausa: dashboard → Resume project (2 min).

## 4. Rutas y pantallas

- `/` — Entrada cartel neón (skip, ≤2.2s, solo 1ª visita desktop) → grilla 8 tortas →
  promo → Cómo comprar → Historia 3 hitos → Dónde estamos → footer.
- `/torta/[slug]` o modal — ficha: fotos, ingredientes, tamaños/porciones/precio,
  botón "Cómo la compro". Sin botón comprar.
- `/admin` — login email+password; inputs gigantes de precio; fechas especiales
  on/off con inicio/fin; panel "Qué miran" (GA4 Data API en producción).
- `/tv?pantalla=1` — solo promo + logo (sin QR). `=2` Chajá/Zingarella/Mousse/Selva Negra.
  `=3` Lemon Pie/Bocado Griego/Mixta/Reina. Cuadrantes: foto+nombre+descripción+precio.

## 5. Sistema de diseño (de la marca)

- Paleta: `--marca:#E8491D` (rojo anaranjado), `--amarillo:#FFC107`, base crema/blanco,
  texto marrón oscuro. Amarillo nunca como texto sobre blanco.
- Motion: prohibidas animaciones fuertes. Permitidos: crossfade entrada, flicker neón
  breve, scroll sutil corona, shimmer líquido LENTO en TVs (gradiente 250% ida/vuelta
  7–12s, intensidad pulsante, delays desincronizados por elemento). Todo apagado con
  `prefers-reduced-motion`.
- Mobile web estilo app: header sticky con marca + bottom nav fija
  (Tortas·Comprar·Historia·Promo·Contacto). Tap targets ≥44px, inputs ≥16px.
- Copy sensible: "pastelería artesanal" (nunca "panadería"); packaging = "envuelta con
  faja de cartón" (NO cajas); Cómo comprar con la explicación de la moto.

## 6. Catálogo (datos reales, del cliente)

| Producto | Tamaños / precio | Porciones |
|---|---|---|
| Chajá durazno / frutilla | 1kg $28.000 · 1½kg $42.000 | 8 / 12 |
| Zingarella | 1kg $25.000 | 8 |
| Lemon Pie | 1kg $26.000 | 8 |
| Mousse / Selva Negra / Bocado Griego / Reina | 1kg $28.000 · 1½kg $42.000 | 8 / 12 |
| Mixta | 2kg $48.000 | 16 |
| Promo | 1kg Chajá + 1 Zingarella $48.000 | — |

Ingredientes completos por ficha en `PROPUESTA-El-Rey-del-Chaja.md` §1. Precios van a
Supabase como seed; el admin los gobierna desde el día uno.

## 7. Backlog (en orden, con aceptación)

- [ ] B1 Contenido: fotos 8 tortas + frente + promo (mismo encuadre), dirección,
      horarios, textos finales. SIN esto no se maqueta. (Bloquea B2)
- [ ] B2 Web E1: las 7 secciones con datos reales + SEO local (schema Bakery/Product).
      Aceptación: navega en celu/compu; `tel:4856128` funciona; sin scroll horizontal 360px.
- [ ] B3 Supabase: proyecto en cuenta del cliente, tablas, seed, RLS (lectura pública
      precios; escritura solo auth), Auth owner. Aceptación: leer sin login, escribir solo con login.
- [ ] B4 Admin E1: login, CRUD precios, promo, fechas on/off con auto-apagado, panel básico.
      Aceptación: cambio se refleja en web ≤2 min.
- [ ] B5 Deploy + dominio + DNS + GA4/Search Console (cuenta del cliente). Aceptación: URL final
      con precios reales + medición registrando.
- [ ] B6 TVs E2: `/tv` ×3 + SW offline + polling 60s + QR? NO (sin QR). Aceptación:
      reboot PC → 3 TVs solas; cambio en admin → TVs en ≤60s; sin internet → último precio + leyenda.
- [ ] B7 Endurecer: keepalive Action, leyenda "precios al HH:MM", manual 1 página + video 30 min,
      capacitación, garantía 30 días.
- Fuera (Fase 2): historia película, video frente, reporte mensual, pauta.

## 8. Convenciones de trabajo

- Commits convencionales (`feat:`, `fix:`, `docs:`), sin "Co-Authored-By" ni firma IA.
- Ramas: `master` = lo deployado; feature branches cortas, PR chico.
- **Secretos:** jamás en repo (es público). Solo placeholders + `.env` local. Service-role
  key de Supabase SOLO en server/functions, nunca en front.
- Verificación por tarea: qué se probó, en qué navegador/ancho, qué quedó pendiente.
- Tras cada tarea: actualizar checklist de este archivo + `odd/tasks/chaja-mock.md`.

## 9. Pendientes del cliente (bloquean inicio)

Anticipo + fotos + dirección + horarios + textos + acceso panel del dominio (o 10 min juntos)
+ Gmail del local (o crear `elreydelchaja.rosario@gmail.com` a su nombre).

## 10. Prompt de arranque para la IA build

> Estás desarrollando El Rey del Chajá según este AGENTS.md. Stack Astro+Vercel+Supabase.
> Trabajá el backlog en orden (B1→B7), una tarea por vez. Antes de codificar, releé la
> sección correspondiente y la demo. Nada de WhatsApp/carrito/delivery en ningún lado.
> Verificá cada tarea como dice §8 y actualizá los checklists. Si falta un dato del
> cliente (§9), no lo inventes: dejá placeholder visible y seguí.
