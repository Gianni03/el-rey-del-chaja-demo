# El Rey del Chajá — Propuesta de modernización integral

Web producto-céntrica + un solo admin de precios + 3 TVs del local alimentadas por la misma fuente. Sin animaciones fuertes, sin banners descartables, sin depender de Framer para cada cambio.

## Quick path — qué se entrega

1. **Fase 1 (2-3 semanas):** web one-page con las 8 tortas, ingredientes, precios y porciones + admin simple de precios + 3 pantallas TV HDMI con layout ya definido por el cliente.
2. **Fase 2 (2 semanas):** intro cinematográfica cartel apagado → prendido (liviana, salteable), scroll sutil logo/corona, popups de fechas especiales prearmados, SEO local Rosario.
3. **Fase 3 (opcional):** video hero real, analytics + datos para decidir, y mantenimiento.
4. **Verificación:** cambiar un precio en el admin actualiza web + 3 TVs. Y el admin muestra qué torta se mira más. Esas son las dos pruebas de aceptación.

## 1. Punto de partida (lo relevado)

### Catálogo real — no va a cambiar

| Producto | Tamaños | Precio actual | Porciones |
|----------|---------|---------------|-----------|
| Chajá durazno / frutilla | 1 kg / 1 ½ kg | $28.000 / $42.000 | 8 / 12 |
| Zingarella | 1 kg aprox | $25.000 | 8 |
| Lemon Pie | 1 kg | $26.000 | 8 |
| Mousse | 1 kg / 1 ½ kg | $28.000 / $42.000 | 8 / 12 |
| Selva Negra | 1 kg / 1 ½ kg | $28.000 / $42.000 | 8 / 12 |
| Bocado Griego | 1 kg / 1 ½ kg | $28.000 / $42.000 | 8 / 12 |
| Reina | 1 kg / 1 ½ kg | $28.000 / $42.000 | 8 / 12 |
| Mixta | 2 kg | $48.000 | 16 |
| Promo | 1 kg Chajá + 1 Zingarella | $48.000 | — |

### Ingredientes (de las hojas del cliente — van a cada ficha)

- **Chajá:** bizcochuelo vainilla, crema chantilly, merengues, duraznos, frutillas (solo en temporada).
- **Zingarella:** bizcochuelo vainilla, flan casero, crema chantilly o dulce de leche, decorado con chocolate y cerezas.
- **Lemon Pie:** masa frola, crema de limón, merengue italiano.
- **Mousse:** bizcochuelo chocolate, dulce de leche, mousse chocolate, baño chocolate.
- **Selva Negra:** bizcochuelo chocolate, mermelada frutillas, crema chantilly, cerezas al maraschino, baño chocolate.
- **Bocado Griego:** bizcochuelo, dulce de leche, crema de café, granizada con chocolate, merengues.
- **Reina:** bizcochuelo vainilla, dulce de leche, dulce a la crema, chocolate blanco.
- **Mixta:** bizcochuelo vainilla y chocolate, dulce de leche y crema chantilly, duraznos, merengues.

### Web actual

- Hecha en **Framer** (generator `Framer f550547`, template `Detox Cafe — Restaurant & Cafe`), publicada 01/09/2026.
- Se ve bien pero es template de café, no pastelería tradicional rosarina.
- Sin admin real: cada aumento de precio es edición manual.
- Sin ingredientes, FAQ incompleto (encargos, envíos, conservación sin responder), sin horarios/mapa/WhatsApp directo, dice "panadería" cuando no lo son, SEO local débil.
- Se conserva el lema "Clásico. Artesanal. Inconfundible." y la estética aprobada.

### TVs del local (hoja del cliente)

- 3 pantallas 55" (1,20 m x 70 cm) en fila sobre el mostrador.
- TV1: solo PROMO (1 kg Chajá + 1 Zingarella $48.000).
- TV2 y TV3: divididas en 4 cuadrantes. Cada cuadrante: foto + nombre + descripción arriba, precio + porciones abajo.
- Orden pedido — TV2: Chajá / Zingarella / Mousse / Selva Negra. TV3: Lemon Pie / Bocado Griego / Mixta / Reina.
- Requisito textual: mismos productos siempre, solo cambian precios. No rehacer diseño cada 3 meses.

### Instagram pendiente (hoja para Germán)

Solo 3 fotos subidas, faltan 5 (total 8). Corregir "la única panadería que...". Publicar "ya comenzó temporada de frutillas".

## 2. Propuesta técnica

### Arquitectura — una sola fuente de verdad

```
products.json  (único archivo editable)
 ├── web pública (lee el JSON)
 ├── TV1 promo (lee el JSON)
 ├── TV2 cuadrantes (lee el JSON)
 └── TV3 cuadrantes (lee el JSON)
```

Stack sugerido: **Astro (estático) + `products.json` + admin protegido con contraseña.** Hosting gratuito (Vercel/Netlify) con el dominio actual. Sin base de datos, sin mensualidad, funciona offline en TVs.

Por qué no `contenteditable` directo en la TV: se borra con F5, no valida formato, no sincroniza con la web, y un toque de más rompe el layout de 4 cuadrantes. El admin con 8 inputs gigantes es igual de fácil y actualiza todo a la vez.

### Web — estructura en grande (7 bloques, para elegir)

Orden propuesto: Entrada → Las 8 tortas → Cómo comprar → Historia → Promo → Dónde estamos → Footer. El hambre primero, el enamoramiento después. Cada bloque se puede aprobar o recortar por separado: la decisión es del negocio, la sugerencia de orden es UX.

**1. Entrada.** Crossfade foto frente día → noche con flicker de neón en CSS (~300 kb, parece video). Solo desktop, solo primera visita, máximo 2,2 s, con botón saltar y respeto a `prefers-reduced-motion`. El video real del frente va como loop muteado en hero, nunca como bloqueo. Logo/corona con scale sutil al scrollear, fijo chiquito en header.
- Variante mínima: sin intro, solo hero con foto noche + cartel prendido. Mismo efecto, 0 riesgo.

**2. Las 8 tortas (cada una clickeable a su ficha).** Grilla de 8 + promo. Card: foto real, nombre, precio grande, porciones. Ficha completa por torta: foto entera + corte, ingredientes (los relevados), tamaños y porciones, "ideal para..." (ej: Mixta 2 kg = 16 porciones = cumpleaños familiar), botón "Cómo la compro" que salta a la sección 3. Sin carrito ni botón comprar: no venden online y un botón muerto destruye confianza.

**3. Cómo comprar (sección propia, no un FAQ escondido).** Copy honesto propuesto, a aprobar por ellos:
> **"Vení al local. Te lo explicamos."**
> No tomamos reservas ni hacemos envíos. No es capricho: la mousse y el chajá no sobreviven al viaje en moto, llegan rotas y no queremos entregarte eso. Las tortas del día están en el mostrador, elegís la que ves, te la llevás en caja firme. Si tenés dudas, llamanos al 4856128 en horario de atención.
Tres pasos con iconos: 1) Vení, 2) Mirá el mostrador, 3) Llevátela. Conservación y horarios acá, no dispersos.

**4. Historia (condicionada al material).** Pedirles TODO lo viejo: fotos del frente antiguo, cajas, recortes, primer cartel. Aunque estén rotas, se escanean y restauran.
- Variante A — Película (si aparecen 10+ fotos): scrollytelling con línea de tiempo 1980 → hoy, fotos que se agrandan, texto corto, fondo oscuro. En mobile cae a carrusel simple (el scroll pinnned en celular se rompe, no forzarlo). +8h opcionales.
- Variante B — 3 hitos (si hay 3-4 fotos): foto grande + frase por hito. Digno y corto antes que largo y pobre. Incluida en base.
Título a mantener: "El clásico inconfundible que nos une", con fechas reales.

**5. Promo vigente.** Siempre visible, alimentada por el admin. En web y TV1 con el mismo diseño.

**6. Dónde estamos / contacto.** Solo teléfono fijo 4856128, gigante y clickeable `tel:`. Dirección, horarios, mapa embebido + "cómo llego". Aclaración honesta: "Atendemos por teléfono fijo en horario de local."

**Contacto — decisión UX cerrada: sin WhatsApp.** Sin botón de WhatsApp y sin bot. Un botón que saca de la web para volver a la web es un loop que rompe confianza, y un bot que nadie del local mira muere en dos semanas. Si algún día lo piden, será proyecto aparte con celular dedicado en el local. La web convierte con fijo + mapa + horarios, nada más.

- SEO local: títulos "El Rey del Chajá Rosario", schema `Bakery + Product + Price`, Open Graph.

### Admin

- Ruta `/admin` con contraseña. 8 productos con inputs de precio por tamaño + campo promo.
- Guardar → escribe `products.json` → se actualiza web + TVs.
- Módulo **Fechas especiales**: lista fija (Navidad, Año Nuevo, Pascuas, Día del Padre, Día de la Madre, Día del Amigo, Temporada Frutilla). Cada una con título, texto, estilo y foto prearmados. El dueño solo tilda `[x] Activo`, ajusta precio si quiere, define inicio/fin y guarda. Se apaga solo. Popup con el mismo estilo prearmado en web y TV1.
- Capacitación: video de 30 min + manual de 1 página.

### TVs

- Mini-PC con 3 salidas HDMI + Chrome en modo kiosco fullscreen. Tres URLs: `tv.html?pantalla=1/2/3`.
- Tipografía enorme legible a 3 m, fondo claro, alto contraste.
- Animación única permitida: brillo (`shimmer`) en títulos cada 8 s, 1,2 s de duración, sin mover layout. Cero carruseles, cero video con sonido.
- Funciona sin internet una vez cargado. Para actualizar: guardar en admin + F5 en la PC.

### Analytics y datos — para que puedan revisar sin saber nada técnico

Negocio tradicional: no les sirve un panel lleno de gráficos. Les sirve responder 4 preguntas una vez por mes: ¿nos buscan más?, ¿qué torta miran más?, ¿la promo funciona?, ¿nos llaman o nos escriben?

- **GA4 + Search Console**, con banner de cookies simple e IP anonimizada. Sin trackers raros.
- **Eventos medidos (5, ni uno más):** `ver_torta` (cada ficha), `ver_promo`, `clic_llamar_4856128`, `clic_como_llegar`, `ver_historia`. Cada popup de fecha especial mide `ver_popup` y `clic_popup`.
- **Panel "Qué miran" dentro del mismo `/admin`:** ranking de las 8 tortas por vistas, clics a llamar/cómo llegar por semana, horario pico, y de dónde vienen (Rosario / resto). Todo en lenguaje de mostrador, no de marketing.
- **QR en TV1** apuntando a la ficha de la promo en la web (`utm_source=tv-local`): mide cuánta gente en el local escanea. Las TVs no se trackean (funcionan offline), el QR es el puente. Nada de WhatsApp en el medio.
- **Reporte mensual de 1 página (PDF automático):** visitas, top 3 tortas, promo del mes, clics a contacto, y una recomendación ("la Selva Negra es la 2ª más vista pero 6ª en consultas: súbanla a TV2 arriba"). Lo leen en 2 minutos.
- **Decisiones que habilita:** qué torta poner en TV1 el mes que viene, si la promo Chajá+Zingarella rinde, si Temporada Frutilla justifica stock, y si conviene pautar en Instagram.

## 3. Plan por fases y horas

| Fase | Alcance | Horas |
|------|---------|-------|
| 0. Contenido | Briefing sesión fotos (8 tortas + local + promo mismo encuadre), retoque, corrección copies "pastelería artesanal", post frutillas | 10 |
| 1. Web base + admin + TVs | One-page 8 fichas + sección Cómo comprar + promo + local/contacto solo fijo, `products.json`, admin precios con login, 3 layouts TV, deploy dominio, capacitación | 46 |
| 2. Experiencia + fechas + historia | Intro cartel, scroll corona, 7 popups fechas con programación, historia Variante B (3 hitos), SEO local + schemas, feed Instagram 8 fotos | 36 |
| 2b. Historia Película (opcional) | Variante A scrollytelling con línea de tiempo (solo si hay 10+ fotos viejas) | +8 |
| 3. Cierre (opcional) | Video hero optimizado, analytics + panel "Qué miran" + reporte mensual 1 página, manual impreso, garantía 30 días | 18 |
| **Total Grande (0+1+2+3)** | | **~110** |
| **Grande + Película (0+1+2+2b+3)** | | **~118** |
| **Fase 1 sola (0 parcial + 1)** | Entrega que ya factura | **~46** |
| **Fase 2 sola** | Cuando vean que vende | **~36** |

Desglose Fase 1 (las 46h): diseño UI 8h, maquetación web 14h, sección Cómo comprar 4h, fichas 8 tortas 5h, admin + JSON 8h, TVs 7h. Deploy + capacitación aparte en Fase 3.
Desglose Fase 2 (las 36h): intro + scroll 10h, popups + programación 10h, historia 3 hitos 4h, SEO + schemas 6h, integración fotos + QA 6h.

Plazos: Fase 1 en 2-3 semanas (1 semana fotos en paralelo). Fase 2 en 2 semanas.

## 4. Impacto esperado

| Problema hoy | Impacto del cambio |
|--------------|-------------------|
| Banners impresos mueren con cada aumento | Precio se cambia en 1 minuto, en web + TVs a la vez. Ahorro de reimpresión permanente |
| Cliente no sabe qué lleva cada torta | Fichas con ingredientes → menos preguntas en mostrador, más ticket promedio |
| Solo 3 fotos, template genérico | 8 fotos reales + identidad tradicional → confianza y diferenciación en Rosario |
| "Panadería" + FAQ vacío + sin mapa | Copies correctos + horarios/mapa/fijo → menos llamados para preguntar lo básico |
| Joven que no los conoce no entiende por qué no hay delivery | Sección Cómo comprar con la explicación de la moto → el "no" se vuelve valor de calidad |
| Promo y fechas especiales dependen de vos | Popups con auto-apagado → venden en Navidad/Pascuas sin llamarte |
| Deciden a ciegas qué se vende | Panel "Qué miran" + reporte 1 página → saben qué torta empujar y si la promo rinde |

## 5. Checklist de aceptación

- [ ] Las 8 fotos reales están publicadas (web + Instagram + TVs usan las mismas)
- [ ] Cambiar un precio en `/admin` actualiza web + TV1/2/3 sin tocar código
- [ ] TV1 muestra solo la promo, TV2/TV3 los 4 cuadrantes en el orden pedido
- [ ] Intro se puede saltar, dura menos de 3 s y no aparece en cada visita
- [ ] Popup de fecha especial se activa/desactiva y se apaga solo por fecha fin
- [ ] Textos dicen "pastelería artesanal", nunca "panadería"
- [ ] Tel 4856128, dirección, horarios y mapa visibles sin hacer scroll lateral en mobile. Sin botón ni bot de WhatsApp en ningún lado
- [ ] El panel "Qué miran" muestra el ranking de tortas y los clics a llamar sin salir del admin
- [ ] El reporte mensual llega en 1 página con 1 recomendación concreta

## 6. Siguiente paso

Aprobar Fase 1 + coordinar sesión de fotos. Sin fotos no se maqueta. Todo lo demás ya está relevado.

---

## ANEXO — Valores (información interna, no presentar así)

> Tarifa base: $25.000/hora. No mostrar horas al cliente, mostrar precio cerrado por fase.

| Paquete | Horas | Valor |
|---------|-------|-------|
| Fase 1 — lo que duele hoy | ~46h | **$1.150.000** |
| Fase 2 — experiencia + fechas + historia 3 hitos | ~36h | **$900.000** |
| Historia Película scrollytelling (opcional, solo con 10+ fotos) | +8h | **+$200.000** |
| Fase 3 — cierre + analytics y datos | ~18h | **$450.000** |
| Contenido/fotos dirección (incluido en F0) | ~10h | **$250.000** (fotógrafo aparte ~$200-300 USD) |
| **Grande completo** | ~110h | **~$2.750.000** |
| **Grande + Película** | ~118h | **~$2.950.000** |
| Achicado mínimo (solo si piden recorte extremo) | ~35h | $875.000 |

Forma de cobro sugerida: 30% anticipo, 40% entrega Fase 1, 30% entrega Fase 2. Mantenimiento sin abono fijo: $25.000 por intervención o bolsa de 4h/mes. Garantía 30 días por bugs.
