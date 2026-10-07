# Registro — as outras frentes (Guia de Locais e Eventos)

Escopo: tipografia, mídia, usuário e auditoria. As decisões de layout da etapa anterior estão em `DOCUMENTACAO.md` e não mudaram.

## 1. Tipografia fluida — `clamp(mínimo, preferido, máximo)`

Fórmula nos dois títulos: `clamp(1.5rem, 1rem + 2.5vw, X)`. O preferido mistura `rem` com `vw`, assim o texto continua respondendo ao zoom do usuário (só `vw` não responderia).

| Título | X (máx.) | ~360px | ~1280px |
|---|---|---|---|
| Vitrine da home (`.titulo-main h1`) | 2.75rem (44px) | 16 + 9 = **25px** | 16 + 32 = 48 → trava no máx. **44px** |
| Página do local (`.titulo-local`) | 2.5rem (40px) | **25px** | 48 → trava no máx. **40px** |

- O h1 do local era `2rem` fixo. Passou a `clamp` com `text-wrap: balance`, mantendo o `overflow-wrap: anywhere` que já existia.
- O título da vitrine já tinha `clamp`; mantive.
- Em 360px o mínimo (24px) nem chega a agir; ele protege telas menores que ~320px.

## 2. Mídia
- Regra universal no topo do CSS: `img, svg, video { max-width: 100%; height: auto; }` (fora do reset escopado, vale para qualquer imagem futura).
- `.foto-local img`: `aspect-ratio: 16 / 9` + `object-fit: cover`. A caixa de recorte mantém a proporção mesmo se trocarem a foto por uma vertical.
- `alt` da foto trocado: era igual à legenda ("Parque Airton Nogueira"), o leitor de tela lia o mesmo texto duas vezes. Agora descreve a imagem.

## 3. Usuário
- **Viewport:** já existia nas duas páginas (`index.html`, `atividade.html`). Conferido.
- **Área de toque ~44px** (`min-height: 44px` + `display: flex/inline-flex` para a altura valer em link):
  menu do cabeçalho, menu lateral, sub-menu da detalhe, links de contato e títulos dos cards sem página.
  O card "Ayton Nogueira" já é inteiro clicável.
- **Foco visível:** `a:focus-visible` global com contorno de 3px. Azul-escuro sobre fundos claros, amarelo sobre cabeçalho/rodapé azuis. Antes só o menu do cabeçalho e o sub-menu tinham foco desenhado.

## 4. Auditoria (Lighthouse › Acessibilidade)

> **Preciso ser transparente:** não consigo rodar o Lighthouse aqui. Os achados abaixo foram encontrados lendo o código e calculando contrastes. As notas são para **você preencher** depois de rodar (DevTools › Lighthouse › só Acessibilidade).

| Página | Nota antes | Nota depois |
|---|---|---|
| `index.html` | ___ | ___ |
| `atividade.html` | ___ | ___ |

**Achado principal (as duas páginas): contraste insuficiente.**
- *O que aponta:* `#2980b9` com texto branco tem contraste de ~4,3:1 (mínimo 4,5:1 para texto normal). Atinge links do menu, `.cidade`, rodapé da home, datas da agenda, links do sub-menu e `.links`.
- *Correção:* token `--azul: #1a6496` (6,4:1 com branco; ~5,5:1 sobre o fundo `#e8f0f7` do sub-menu), aplicado em todos os usos do azul.

**Outros achados corrigidos**
- `index.html` com `lang="en"` e conteúdo em português → `pt-BR`.
- Home sem `h1` e pulando de `h2` para `h3` → vitrine virou `h1`, "Próximos eventos" virou `h2`.
- Menu da home sem landmark `nav` (a detalhe tinha) → `<nav aria-label="Menu principal">`.

**Onde posso discordar de uma ferramenta:** os cards com `href="#"` (locais sem página ainda) podem ser sinalizados por alguns validadores. Mantive: são placeholders honestos até as páginas existirem.

## 5. Registro por página

| | Folha de estilos única resolveu sozinha | Precisou de correção própria da página |
|---|---|---|
| Tipografia | `clamp` no h1 da vitrine e do local, mesma fórmula | Só o máximo muda (44px vs 40px): vitrine é hero, local é título de página |
| Mídia | Regra universal `img { max-width:100% }` vale para as duas | `object-fit/aspect-ratio` só na `.foto-local` da detalhe; `alt` é por página |
| Usuário | Foco global, tokens de cor, viewport | Alvos de toque por componente: menu lateral e cards (home); sub-menu e links de contato (detalhe) |
| Auditoria | Contraste: um token corrige as duas | `lang`, hierarquia de títulos e `nav` (home); `alt` da imagem (detalhe) |

Conclusão: o que é **cor, foco e regra de imagem** vive na folha única e se propaga. O que é **estrutura e conteúdo** (idioma, títulos, textos alternativos, landmarks) é por página. Por isso o guia é um site e não um documento.

## 6. Verificação
- [ ] DevTools com redimensionamento contínuo: home e detalhe, sem rolagem horizontal.
- [ ] Emulação de aparelho real (ex.: iPhone SE 375px): dedo acerta os links, foco aparece com Tab.
- [ ] Lighthouse ≥ 90 nas duas páginas (preencher tabela do item 4).
