# API de Integração do DAM Yapoli — Documentação

Referência voltada para clientes da API de integração com terceiros
da plataforma DAM Yapoli. Renderizada com
[Redoc](https://redocly.com/redoc) a partir de especificações
OpenAPI 3.1 — uma por idioma (PT-BR e EN) — e hospedada no GitHub
Pages.

> Site publicado (após habilitar o Pages — veja abaixo):
>
> - PT-BR: `https://yapoli-tech.github.io/dam-api-docs/`
> - EN: `https://yapoli-tech.github.io/dam-api-docs/en/`

## Estrutura do repositório

| Arquivo                       | Finalidade                                                                 |
| ----------------------------- | -------------------------------------------------------------------------- |
| `openapi.yaml`                | Fonte da verdade da especificação (PT-BR). Edite este arquivo primeiro.    |
| `openapi.en.yaml`             | Tradução em inglês. Deve espelhar a estrutura de `openapi.yaml` (ver abaixo). |
| `index.html`                  | Renderizador Redoc standalone (PT-BR). Carrega o YAML via HTTP.            |
| `en/index.html`               | Renderizador Redoc da versão em inglês.                                    |
| `.github/workflows/pages.yml` | Lint + verificação de paridade PT/EN + publicação no GitHub Pages.         |
| `README.md`                   | Este arquivo.                                                              |

## Pré-visualização local

O Redoc precisa carregar a especificação via HTTP (não por
`file://`), então sirva a pasta com qualquer servidor estático:

```bash
# Opção A — Python, sem instalação
python3 -m http.server 8080

# Opção B — Node
npx http-server -p 8080
```

Depois abra <http://localhost:8080> (PT-BR) ou
<http://localhost:8080/en/> (EN). Edite o YAML, salve e recarregue a
página — sem etapa de build.

## Validar a especificação

Antes de fazer push:

```bash
npx @redocly/cli lint openapi.yaml openapi.en.yaml
```

Verifique também a paridade estrutural entre os dois idiomas (o
mesmo check roda no CI; requer [yq](https://github.com/mikefarah/yq)):

```bash
strip() {
  yq 'del(.. | .description?)
    | del(.. | .summary?)
    | del(.. | .title?)
    | del(.. | .tags?)
    | del(.. | .["x-codeSamples"]?)
    | del(.info.contact)
    | del(.info.["x-logo"])' "$1"
}
diff <(strip openapi.yaml) <(strip openapi.en.yaml)
```

Opcionalmente, gere um HTML único e autossuficiente (sem dependência
de CDN):

```bash
npx @redocly/cli build-docs openapi.yaml -o dist/index.html
```

## Deploy para o GitHub Pages

O workflow incluído (`.github/workflows/pages.yml`) publica o
repositório como site estático em cada push para `main`.

Configuração única:

1. Faça push deste repositório para o GitHub.
2. Acesse **Settings → Pages**.
3. Em **Build and deployment → Source**, escolha **GitHub Actions**.

A partir do próximo push, o site ficará acessível em
`https://yapoli-tech.github.io/dam-api-docs/`.

## Tradução (PT-BR ↔ EN)

A documentação é publicada em dois idiomas a partir de dois arquivos
irmãos: `openapi.yaml` (PT-BR, fonte da verdade) e `openapi.en.yaml`
(EN). Regras para mantê-los em sincronia:

- **Toda mudança estrutural** (endpoints, parâmetros, schemas,
  exemplos, códigos de resposta) deve ser aplicada **nos dois
  arquivos**. O CI compara a estrutura e falha se divergirem.
- **Somente prosa difere entre os arquivos**: `description`,
  `summary`, `title` e nomes/descrições de `tags`. Todo o restante
  deve ser byte a byte idêntico.
- **Não traduza exemplos de payload.** Os exemplos de resposta
  mostram payloads literais da API — que pode retornar mensagens em
  português (ex.: `message: Acesso negado.`) — e devem permanecer
  idênticos nos dois idiomas. A versão EN explica isso na seção
  "Response envelope".
- **`x-codeSamples`** ficam fora da verificação de paridade para
  permitir localizar textos ilustrativos (ex.: caminho de arquivo no
  curl de upload); mantenha os comandos em sincronia manualmente.
- O link de troca de idioma fica no topo do `info.description` de
  cada spec.

## Convenções de autoria

- Mantenha os exemplos dos endpoints alinhados com a coleção Postman
  utilizada pelos parceiros de integração.
- Prefira `x-codeSamples` nas operações para snippets `curl` de
  copiar e colar. O Redoc os renderiza em uma aba dedicada.
- Agrupe endpoints relacionados com `tags`; a ordem das tags define
  a ordem da barra lateral.
- Reutilize schemas via `$ref` para manter os formatos de requisição
  e resposta consistentes entre operações.
