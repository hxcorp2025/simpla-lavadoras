# Simpla Lavadoras — LP Funil Pós-Obra

Landing page de captação para aluguel de lavadora de piso (limpeza bruta durante e pós-obra) na Grande Porto Alegre e Serra Gaúcha.

Página única, estática, sem build e sem dependência de runtime. Abre direto no navegador ou em qualquer host estático.

- **Origem:** projeto `SIMPLA | LP FUNIL PÓS OBRA` no Claude Design (`cada8289-15ec-41ff-8c83-14807fa97ce9`)
- **Conversão:** o `.dc.html` original (formato do Claude Design) foi compilado para HTML/CSS/JS puro

---

## 🔴 Pendências antes de subir tráfego

| # | Item | Onde |
|---|---|---|
| 1 | **Pixel da Meta não está instalado.** O código dispara `fbq('track','Lead')` só se o pixel existir; hoje não existe, então nenhum Lead é registrado. | `index.html`, colar o snippet do pixel no `<head>` |
| 2 | **Lead não é persistido em lugar nenhum.** O form só monta a mensagem e abre o `wa.me`. Se a pessoa não apertar enviar no WhatsApp, o lead evapora — e não há como medir isso. | ver "Captura de lead" abaixo |
| 3 | **Rótulo do CTA quebra em 2 linhas dentro do form no mobile.** | ver "CTA no mobile" abaixo |

✅ Resolvido: número de WhatsApp real (`5551985175287`) em `index.html`, const `WHATSAPP`.

### Captura de lead

Hoje o funil é: preenche → abre WhatsApp → **torce** pra pessoa apertar enviar.
Não existe registro do lead nem medição da perda entre "preencheu" e "mandou a mensagem".

⚠️ **Desde que a mensagem virou fixa** ("Olá, quero saber mais sobre as lavadoras de piso!"),
nome/e-mail/telefone **não saem mais da página**: são validados e descartados. Antes ao menos
viajavam dentro do texto do WhatsApp. Ou seja, os 3 campos hoje são atrito puro — custam
conversão e não entregam nada. Só faz sentido mantê-los junto com a persistência abaixo;
sem ela, o form poderia ser só um botão pro WhatsApp.

Se quiser fechar esse furo, o caminho é um POST pro n8n/Supabase antes do `window.open`
(gravar nome/email/telefone + UTMs) e disparar o CAPI server-side em paralelo ao pixel.
Não foi implementado porque não foi pedido.

### CTA no mobile

O rótulo "Falar com um especialista agora" tem 30 caracteres. Os CTAs de seção cabem em
uma linha de 360px pra cima (full-width, 15px). O botão **dentro do form** tem só ~264px
úteis (paddings aninhados: seção 16 + card 16 + box 16) e quebra em 2 linhas equilibradas
— visualmente aceitável, mas é limite de copy, não de CSS.

Um rótulo de ~22 caracteres ("Falar com especialista", "Quero minha máquina") cabe em
uma linha em qualquer largura. Decisão de copy, não mexi.

### Enquadramento do form no mobile (restrição viva)

~95% do tráfego é mobile e todos os CTAs abrem em `#contato` (a **seção**, não o `#form`,
senão o scroll pula a headline). A seção precisa caber numa tela só, da headline ao botão.
Os paddings do bloco `@media (max-width:768px)` são apertados justamente pra isso e foram
medidos, não estimados. Estado atual (base do botão vs. viewport útil):

| Aparelho | Viewport | Botão | |
|---|---|---|---|
| Android 360×640 | 640 | ~560 | ✅ com aviso |
| iPhone 14 390×713 | 713 | ~560 | ✅ com aviso |
| iPhone Pro Max 430×800 | 800 | ~540 | ✅ com aviso |
| iPhone SE 375×553 | 553 | ~560 | ⚠️ ~8px abaixo da dobra (some quando a barra do Safari recolhe no scroll) |
| 320×568 | 568 | ~630 | ❌ não cabe (aparelho pré-2016) |

Pra fechar o SE e o 320 sem apertar mais o layout, o caminho é encurtar a headline ou a
subheadline — decisão de copy.

> ⚠️ **Ao remedir:** `--window-size` no Chrome headless do macOS **não** aplica a largura —
> ele trava o `innerWidth` em **500px** (mínimo de janela do SO) e só a altura responde.
> Medir assim dá falso positivo. Use um `<iframe>` com a largura do aparelho
> (+ `--allow-file-access-from-files`), que é o que as media queries realmente enxergam.

---

## O que mudou em relação ao design

Convertido 1:1 em copy, cores, tipografia e espaçamentos. As diferenças são deliberadas:

- **Uma página responsiva no lugar de dois arquivos.** O `Simpla Lavadoras.dc.html` e o
  `Simpla Lavadoras Mobile.dc.html` são a mesma página em dois viewports — copy e lógica
  idênticas, mudando só paddings e grids. Viraram um `index.html` com o desktop de base e
  uma media query em `max-width:768px` aplicando os valores do mobile.
- **Contadores animam ao entrar na tela** (IntersectionObserver), não no load. No original
  a contagem rodava no `componentDidMount` e terminava antes do usuário chegar na seção —
  ninguém via. Respeita `prefers-reduced-motion`.
- **FAQ usa `scrollHeight`** em vez de `max-height` fixo de 440px. No mobile uma resposta
  longa passava de 440px e era cortada.
- **Assets locais.** O original apontava pro WordPress do cliente (`simplalavadoras.com.br`)
  e pra `image-slot` do Design. Tudo baixado pra `assets/`.
- **Sem JS a página continua legível.** Os `sc-for` foram expandidos em HTML estático;
  o acordeão só fecha os painéis quando o JS carrega (classe `.js` no `<html>`).
- **Removido `<meta name="robots" content="noindex,nofollow">`** — era artefato do ambiente
  do Claude Design. Se a intenção for não competir com o site principal no orgânico,
  é só recolocar.
- **`object-fit:contain` no logo do hero.** O original forçava 280x62 num logo de proporção
  4.06:1, esticando de leve.
- **Acessibilidade:** `aria-expanded`/`aria-controls` na FAQ, `lang="pt-BR"`, foco visível
  nos inputs, `alt` nas imagens.
- **SEO/social:** `<title>`, meta description, Open Graph, favicon.

## Estrutura

```
index.html          página completa (HTML + CSS + JS inline)
assets/
  hero-obra.webp        fundo do hero
  simpla-logo.webp      logo (hero + rodapé)
  favicon.webp
  cliente-19..26.svg    8 logos de clientes (marquee)
  prod-compacta.webp    ┐
  prod-media.webp       ├ fotos das 3 lavadoras
  prod-industrial.webp  ┘
```

Origem dos assets: os logos, o logo Simpla e o favicon vieram do site oficial
(`simplalavadoras.com.br/wp-content/uploads/`); o hero e as fotos de produto vieram do
projeto do Claude Design. O hero estava salvo como `.png` mas o conteúdo é WebP —
renomeado pra extensão correta.

## Rodar local

```bash
python -m http.server 8000    # ou: npx serve .
```

Abrir `http://localhost:8000`. Abrir o `index.html` por `file://` também funciona.

## Deploy (GitHub Pages)

Settings → Pages → Source: `main` / root. O repositório precisa ser **público**
(ou conta Pro) pro Pages funcionar.

## Validado

Renderizado em Chrome headless a 320 / 360 / 390 / 430 / 768 / 1440px:
sem overflow horizontal, sem erro de JS, nenhuma imagem quebrada, contadores animando
(`7+ → 18+ → 25+ → 30+`) e FAQ abrindo/fechando com altura correta.
