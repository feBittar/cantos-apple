# Cantos Apple

Uma skill para o Claude que confere e corrige os cantos arredondados do seu app, do jeito que a Apple faz: quando um elemento fica dentro de outro (um botão dentro de um cartão), os dois cantos têm que fazer a curva em volta do mesmo centro. A Apple chama isso de cantos concêntricos.

A conta é uma só:

```text
raio de fora = raio de dentro + espaço entre os dois
```

Em SwiftUI e UIKit com iOS 26 ou mais novo, a skill usa a API que a Apple lançou para isso (`ConcentricRectangle`). Em site, Tailwind, React Native, Flutter e Compose, onde essa API não existe, ela faz a conta e escreve o número certo.

**Antes** (um cartão de produto que o Claude fez sem ninguém falar de canto):

> cartão com canto 16, botão com canto 12, espaço de 20 entre os dois

A distância entre o botão e a borda do cartão dava 20 nos lados e perto de 27 na curva do canto.

**Depois de rodar `/cantos-apple`:**

| onde | antes | depois | a conta |
|---|---|---|---|
| botão "Comprar" dentro do cartão de produto | cartão 16, botão 12, espaço 20 | cartão 32, botão 12 | 12 + 20 = 32 |

Ela mudou uma classe no arquivo (o canto do cartão), abriu a página e conferiu: 20 de distância em toda a volta, até na curva. Avisou também a outra saída, para quem prefere o cartão menos redondo: diminuir o espaço em volta do botão.

## Instalar

### No Claude Code

Cole esta frase na conversa:

```text
Claude, instala a skill que tá nesse repositório pra eu usar em qualquer projeto e confere antes se tá tudo seguro: https://github.com/feBittar/cantos-apple
```

O Claude lê o `SKILL.md`, diz o que ele faz e copia o arquivo para `~/.claude/skills/cantos-apple/` (no Windows, a pasta `.claude\skills\cantos-apple\` dentro da sua pasta de usuário). No modo padrão do Claude Code, ele pede a sua permissão antes de baixar e antes de gravar. Se o `/cantos-apple` não aparecer depois, feche e abra o Claude Code. Para instalar à mão, copie o `SKILL.md` para essa pasta. Para desinstalar, apague essa pasta.

A skill é um arquivo de texto só, sem programa junto.

### No claude.ai

Baixe o [cantos-apple.zip](https://github.com/feBittar/cantos-apple/releases/latest/download/cantos-apple.zip), vá em Customize > Skills, clique no "+" e em "Upload a skill", e envie o zip. Se a opção não aparecer, ligue a execução de código em Settings > Capabilities.

No navegador o Claude não abre a pasta do seu projeto. Cole o código da tela na conversa e ele devolve o trecho corrigido.

### No ChatGPT, no Gemini e em outras IAs

Copie o conteúdo do `SKILL.md` e cole como primeira mensagem de uma conversa nova, junto com o código da tela que você quer revisar.

## Usar

Abra o Claude Code na pasta do seu app e chame a skill:

```text
/cantos-apple
```

Ela revisa as telas e os componentes do projeto. Para olhar um arquivo ou uma pasta só:

```text
/cantos-apple src/components/ProductCard.tsx
```

Ou peça do seu jeito:

```text
revisa os cantos do meu app
```

Para só conferir, sem mudar nenhum arquivo:

```text
/cantos-apple só olha, não muda nada
```

## O que ela devolve

1. Uma frase com o resultado: quantos pares de cantos olhou, quantos corrigiu, quantos já estavam certos e quantos deixou de propósito.
2. Uma tabela com o antes, o depois e a conta de cada par.
3. O que ela viu e deixou, com o motivo.
4. O que precisa de você, quando há uma escolha a fazer.
5. Como ela conferiu: abrindo o app ou só pela conta.

## Como ela decide

- **O normal:** o elemento de fora fica e o de dentro muda. Raio de dentro = raio de fora − espaço.
- **Quando essa conta dá menos que 4** (o espaço é maior que o raio de fora): o de dentro ficaria quadrado, então ela mantém o de dentro e sobe o de fora. É o caso do exemplo acima.
- **Quando o de fora não pode mudar** (é o canto da tela do celular, ou um raio que o app inteiro usa): o de dentro fica com um raio mínimo e ela avisa.

Ela não mexe em pílula nem em círculo, em elemento que está longe do canto do pai, nem em imagem que já encosta na borda e é cortada pelo cartão. E muda só raio de canto: cor, tamanho e texto ficam. O espaço só muda se você escolher a outra saída que ela oferece.

## Onde entra a API da Apple

Em projeto SwiftUI com versão mínima iOS 26 (ou macOS 26), o elemento de fora declara a forma dele com `containerShape` e o de dentro usa `ConcentricRectangle`, que calcula o raio sozinho e se ajusta quando o tamanho muda. Em UIKit, ela usa `cornerConfiguration` com `.containerConcentric`. Se o projeto precisa rodar em versão mais antiga, ela protege com `if #available` ou faz só a conta.

## Limites

- A skill foi testada em três casos pequenos: um cartão em HTML com Tailwind, um em React Native e um em SwiftUI. O código Swift não foi compilado nos testes, porque foram feitos sem Xcode.
- A curva contínua da Apple (o "squircle") é outro assunto. A skill mantém o que o projeto já usa.
- Ela aplica a correção sem perguntar quando a regra é clara. Para ver só o relatório antes, use `/cantos-apple só olha, não muda nada`. E guarde o seu trabalho no git antes de rodar num app inteiro, para poder voltar atrás.

## Fontes

- [ConcentricRectangle](https://developer.apple.com/documentation/swiftui/concentricrectangle), na documentação da Apple.
- [Get to know the new design system](https://developer.apple.com/videos/play/wwdc2025/356/), sessão 356 da WWDC25, onde a Apple explica a concentricidade.
- [The math behind nesting rounded corners](https://cloudfour.com/thinks/the-math-behind-nesting-rounded-corners/), da Cloud Four, sobre a conta em CSS.

## Licença

MIT. Veja o arquivo [LICENSE](LICENSE). A skill é do Felipe, [@ofelipemart](https://www.instagram.com/ofelipemart/) no Instagram.
