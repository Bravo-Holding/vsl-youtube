# CLAUDE.md — VSL YouTube (live clone)

## O QUE É
Landing page mobile, estática e single-file (`index.html`), que imita uma transmissão **ao vivo do YouTube** para reproduzir um **VSL hospedado no YouTube**. Objetivo: máxima conversão (player travado, prova social no chat, escassez via super chat, CTA sincronizado).

## MÉTODO DE TRABALHO — SCRUM
Trabalhar **em sprints**, não em mega-prompt. Cada sprint: escopo pontual, demo (screenshot/validação) e fechamento antes do próximo. Backlog vive na task list da sessão.

### Backlog (status em 2026-06-22)
- S1 Estrutura base + player VSL travado (mobile) — **feito**
- S2 Chat ao vivo contínuo (provas sociais) — **feito**
- S3 Super chat = card VERDE de gift memberships, sincronizado — **feito**
- S4 CTA sincronizado com o tempo do VSL — **feito**
- S5 Dados reais: VIDEO_ID `1sRjnSiME5Y`, CTA/super chat aos 621s (10:21), checkout Hotmart — **feito**
- S6 Repo público em Bravo-Holding — **feito** (`github.com/Bravo-Holding/vsl-youtube`, branch `master`)
- S7 Tracking (GTM/Pixel/CAPI) + Hotmart + modal de captura no CTA — **feito**
- S8 Conteúdo do chat: 50 mensagens (40 simuladas + 10 trechos adaptados de comentários de concorrente, sem o nome dele); 9 fotos reais de depoimento como avatares; footer "Modo somente para alunos" — **feito**

## TRACKING / CHECKOUT (espelho do metodo-remiyah — NÃO divergir)
Esta VSL é do **Método Remiyah** (mesmo produto, pixel e checkout da landing `metodo-remiyah`).
- **GTM:** `GTM-PCKSDBV4`. **Meta Pixel:** `25723610987317406` (PageView + ViewContent 397 BRL no load via requestIdleCallback). **fb-domain-verification:** `x3mdskvask209m6cuxpnd6964lbzp1`.
- **Hotmart launcher** account `be7aeb20-4303-3f6c-b0c3-3050352275ba`.
- **Checkout (CTA):** `https://pay.hotmart.com/P106391383D?off=2srrhbv4&checkoutMode=10`.
- **UTM/sck:** script reescreve hrefs no load; sck (máx 30 chars) só em links pay.hotmart/kiwify.
- **Modal de captura:** intercepta clique em `a[href*="pay.hotmart"]`, coleta nome/email/whatsapp, dispara Pixel `Lead` (advanced matching + external_id) + CAPI `fetch` keepalive pra Supabase Edge `capture-lead` (`iodseuinjheacuoptaau`), depois redireciona pro checkout (mesma aba). payload.product=`metodo-remiyah`, value 397.
- **CORS:** a Edge `capture-lead` só libera origem `ebravoholding.com`. Em localhost o `fetch` falha (capturado no catch, sem quebrar o fluxo). Hospedar sob `ebravoholding.com` pro CAPI gravar.

## DECISÕES TRAVADAS (não reabrir sem pedir)
- **Player travado:** autoplay mudo, sem controles/seek; overlay "toque para ativar o som" (gesto exigido por mobile pra som). Overlay transparente bloqueia interação com o iframe.
- **Só mobile** (tráfego de ADS é mobile). Não gastar tempo com desktop salvo pedido.
- **Config-driven:** tudo editável no bloco `CONFIG` no topo do `<script>`. Não espalhar valores pelo código.
- **Super chat = gift memberships VERDE** (print 2), não card laranja de doação.

## ARQUITETURA
- `index.html` — tudo (CSS inline + JS inline + CONFIG). YouTube IFrame API para autoplay/mute/unmute e leitura de `getCurrentTime` (sincroniza super chat e CTA).
- Chat: array `CONFIG.MESSAGES` (50 itens), append com delay aleatório, loop, DOM podado a ~60 nós. Cada item: `name`, `color` (gray|blue|green|purple|orange), `member` (selo opcional), `text`, `avatar` (opcional, caminho `assets/depo-*.avif`).
- Avatar: se `m.avatar` existe renderiza `<img>`, senão iniciais coloridas. 9 fotos em `assets/` mapeadas a mensagens de gênero compatível (matches diretos: camila→@camila.nunes, lucas→@lucas_dev, pedro→@pedrohenrique, chef-bruno→@bruno.martins).
- Sincronia: `setInterval` lê `getCurrentTime()`; dispara `showSuperchat()` em `SUPERCHAT.time` e `showCTA()` em `CTA.time` (uma vez cada).

## CONTEÚDO DO CHAT
- 40 mensagens simuladas + 10 trechos adaptados de comentários de um concorrente (origem: prints `rafaelmedeirosfilho.com`). **Nunca usar o nome do concorrente** ("Rafael") nas mensagens — referenciar só como "ele"/"esse vídeo".
- `assets/depo-*.avif`: 9 fotos reais de depoimento copiadas de `metodo-remiyah/assets`. São pessoas reais; ao reusar manter coerência de gênero com o handle.

## REGRAS DE EDIÇÃO
1. Mexeu em conteúdo de chat/depoimento? **Verbatim** — não normalizar, corrigir nem inventar.
2. Preservar o player travado e o gate de som ao editar o vídeo.
3. Validar cada sprint no celular/viewport mobile antes de fechar.
4. Não publicar fontes/prints (ver `.gitignore`).

## PENDÊNCIAS DO EWERTTON
- Hospedar sob `ebravoholding.com` pro CAPI gravar (CORS).
- QA em celular real (autoplay/som/sincronia) antes de escalar tráfego.
- (Opcional) diferenciar a origem "VSL live" da landing nos relatórios (hoje `payload.product='metodo-remiyah'`; dá pra separar via campo `product` ou `sck`/utm).

## GITHUB
- Org: `Bravo-Holding`. Repo: `vsl-youtube` (público). Branch única: `master` (commits direto, sem feature branches).
- `.gitignore` exclui prints, `.DS_Store`, `.claude/` e este `CLAUDE.md`.
