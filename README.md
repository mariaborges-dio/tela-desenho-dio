# Código de referência: implantes (selecionar modelo, inserir, girar, redimensionar, excluir)

**Protótipo de referência, não é código de produção.** Ele mostra o comportamento e o visual esperados. Adaptem à stack e ao canvas reais do editor.

## Como abrir
Abra `index.html` em qualquer navegador (desktop, tablet ou celular). Não precisa de servidor.
Para testar no tablet, sirva a pasta na rede local (ex.: `npx serve .`) ou use o link público do protótipo.

## Estrutura
```
index.html                  → tela + CSS + JS (tudo comentado)
assets/
  radiografia-exemplo.jpg   → imagem de exemplo, recortada do print
  modelo-1..5.png           → imagens dos 5 modelos (fundo transparente; 2–5 recortados de ícones, baixa resolução)
  implante.png              → mesmo que modelo-1 (alta resolução)
  cursor-rotate.svg         → cursor customizado de girar (desktop)
  tool-*.png                → ícones da barra lateral, recortados do print
referencia/Main.dc.html     → fonte original do protótipo no canvas do Claude (só referência)
```

## Onde está cada coisa no `index.html`
- **CSS:** blocos marcados com `NOVO` (botão de girar, rótulo de graus, eixo 0°, pontos com seta). Os tokens ficam em `:root`.
- **JS:**
  - `CFG`: parâmetros (encaixe, tolerância, limites, limite de arraste, `KEEP_MODE_AFTER_INSERT`)
  - `MODELS`: lista de modelos (nome, imagem, tamanho na lista e tamanho inserido)
  - **Lista de modelos**: clicar seleciona e **nunca desseleciona**. Arrastar mais de 8 px mostra a prévia + "Toque na imagem para posicionar o item", e soltar não insere
  - **Inserir**: `pointerdown` na imagem com um modelo ativo cria o implante no ponto, já selecionado, e fecha a lista
  - **Balão** "Toque na imagem para inserir": visível enquanto a lista está aberta, some no toque na imagem
  - **Excluir**: remove o selecionado; o botão fica desabilitado sem seleção
  - `render()`: aplica o estado ao DOM
  - **Mover**: `pointerdown/move/up` no `.obj`
  - **Girar**: `pointerdown/move/up` no `.rot`, ângulo pelo centro do objeto (`pointerAngle`) + encaixe + Shift
  - **Redimensionar**: `pointerdown/move/up` em cada `.hnd`, com cálculo no referencial do objeto (o ponto oposto fica fixo)
  - `cursorFor()`: escolhe o cursor do sistema pela direção do ponto + rotação

## Notas de implementação
- Usar **Pointer Events + `setPointerCapture`**. Um único caminho atende mouse, caneta e toque.
- `touch-action: none` na área de edição, para o navegador não rolar nem dar zoom no gesto.
- O rótulo de graus fica **fora** do elemento rotacionado, para continuar na horizontal. A posição é calculada a partir do centro e do ângulo, e limitada à área da imagem.
- A haste é desenhada **antes** dos pontos, para não cobrir a seta do ponto superior.
- Os elementos dos implantes são reaproveitados no `render()` (Map por id) para não perder a captura do ponteiro durante o arraste.
- `window.__proto` só existe para gerar os prints. Removam.
