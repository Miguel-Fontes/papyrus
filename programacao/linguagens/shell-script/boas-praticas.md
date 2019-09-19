---
description: Meus padrões e práticas sugeridos para escrever scripts.
---

# Boas práticas

## Shell selecionado \(shebang\)

Prefiro usar `#!/bin/bash` sempre. É uma decisão sensível, uma vez que deixo de lado uma grande quantidade de funcionalidades específicas de outros shells, em favor de portabilidade.

## Atribuição de valores padrão

É simples validar o conteúdo de uma variável e atribuir um valor de diversas formas, mas prefiro usar uma forma verbosa em scripts para garantir uma legibilidade maior. Em pequenos hacks, no entanto, uso a forma 2 com frequência.

```bash
# 1. Preferida: mais verboso, porém possui maior legibilidade
if [[ -z "$VAR" ]]; then
  VAR="default"
fi

# 2. Em hacks rápidos uso essa forma
[[ "$VAR" ]] || VAR="default"
```



