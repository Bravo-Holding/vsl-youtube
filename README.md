# VSL YouTube (live clone)

Landing page mobile que imita uma transmissão ao vivo do YouTube para reproduzir um VSL hospedado no YouTube. Página estática, single file (`index.html`), sem build.

## O que faz
- Player do VSL **travado** (autoplay mudo + toque para ativar som, sem controles/seek).
- **Chat ao vivo simulado** e contínuo com provas sociais (mensagens em loop).
- **Card verde de gift memberships** (super chat) que aparece em um segundo definido.
- **Botão de CTA** que aparece sincronizado com um segundo do vídeo.

## Como configurar
Tudo no bloco `CONFIG` no topo do `<script>` em `index.html`:
- `VIDEO_ID` — id do vídeo no YouTube.
- `CTA.time` / `CTA.text` / `CTA.url` — quando o botão aparece e para onde leva.
- `SUPERCHAT.time` / `name` / `text` — quando e o que o card verde mostra.
- `MESSAGES` — lista de mensagens do chat (provas sociais).

## Deploy
Site estático: subir `index.html` para qualquer host.
