# API de Integração do DAM Yapoli — Documentação

Referência voltada para clientes da API de integração com terceiros
da plataforma DAM Yapoli. Renderizada com
[Redoc](https://redocly.com/redoc) a partir de uma única especificação
OpenAPI 3.1 e hospedada no GitHub Pages.

> Site publicado: `https://<org>.github.io/<repo>/` (atualize após
> habilitar o Pages — veja abaixo).

## Estrutura do repositório

| Arquivo                       | Finalidade                                                                |
| ----------------------------- | ------------------------------------------------------------------------- |
| `openapi.yaml`                | Fonte da verdade da especificação. Edite este arquivo para alterar a doc. |
| `index.html`                  | Renderizador Redoc standalone. Carrega o YAML via HTTP.                   |
| `.github/workflows/pages.yml` | Publica o site estático no GitHub Pages em cada push para `main`.         |
| `README.md`                   | Este arquivo.                                                             |

## Pré-visualização local

O Redoc precisa carregar a especificação via HTTP (não por
`file://`), então sirva a pasta com qualquer servidor estático:

```bash
# Opção A — Python, sem instalação
python3 -m http.server 8080

# Opção B — Node
npx http-server -p 8080
```

Depois abra <http://localhost:8080>. Edite `openapi.yaml`, salve e
recarregue a página — sem etapa de build.

## Validar a especificação

Antes de fazer push:

```bash
npx @redocly/cli lint openapi.yaml
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
`https://<org>.github.io/<repo>/`.

## Convenções de autoria

- Mantenha os exemplos dos endpoints alinhados com a coleção Postman
  utilizada pelos parceiros de integração.
- Prefira `x-codeSamples` nas operações para snippets `curl` de
  copiar e colar. O Redoc os renderiza em uma aba dedicada.
- Agrupe endpoints relacionados com `tags`; a ordem das tags define
  a ordem da barra lateral.
- Reutilize schemas via `$ref` para manter os formatos de requisição
  e resposta consistentes entre operações.
