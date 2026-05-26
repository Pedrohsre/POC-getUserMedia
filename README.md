# Teste (getUserMedia, nonce overlay, input capture)

Conjunto de provas de conceito para validar abordagens de captura fotográfica obrigatória via câmera, com geração de evidências

## Estrutura

```
spike-v3/
├── index.html           # Hub de navegação e mapa de riscos
├── poc1.html            # POC 1 — Captura básica via getUserMedia
├── poc3.html            # POC 3 — Overlay de nonce + verificação SHA-256
└── input-capture.html   # Teste isolado do atributo capture do <input type="file">
```

## POCs

### POC 1 — Captura via `getUserMedia`
Valida o fluxo básico de acesso à câmera traseira com `facingMode: "environment"`. Exibe preview ao vivo, captura o frame no canvas e exporta como JPEG. Cobre tratamento de erros para câmera negada, não encontrada ou em uso por outro processo.

### POC 3 — Overlay de Nonce com Integridade SHA-256
Grava metadados de auditoria diretamente nos pixels da imagem (não em metadados EXIF, que podem ser removidos):

- Gera um nonce criptográfico de 4 bytes por sessão com `crypto.getRandomValues()`
- Desenha no canvas uma barra semitransparente com `AUDITORIA [timestamp] #[nonce]`
- Calcula SHA-256 do JPEG final via `crypto.subtle.digest()` para detecção de adulteração

O overlay sobrevive a recompressão JPEG, upload e redimensionamento — por estar gravado nos pixels.

### Input Capture — Teste do Atributo `capture`
Testa se `<input type="file" capture="environment">` abre diretamente a câmera ou ainda oferece a galeria como opção.

## Tecnologias

- HTML5 + JavaScript (ES6+), sem frameworks ou dependências externas
- `navigator.mediaDevices.getUserMedia()` — acesso à câmera
- `HTMLCanvasElement` 2D — captura e manipulação de frames
- `crypto.getRandomValues()` — geração de nonce criptográfico
- `crypto.subtle.digest('SHA-256')` — verificação de integridade
- `Blob` API — serialização de imagens

Documentação utilizada: https://developer.mozilla.org/en-US/docs/Web/API/Media_Capture_and_Streams_API/Taking_still_photos e https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia
