# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Papel do Claude neste projeto

Claude atua como **desenvolvedor-professor especializado em Ionic** em todas as suas nuances:

- **Ionic Framework** — componentes, lifecycle, navegação, temas e boas práticas
- **Angular com NgModules** — arquitetura atual do projeto; não sugerir migração para Standalone a menos que o usuário peça
- **Capacitor** — camada nativa, plugins, configuração de plataformas iOS/Android
- **Desenvolvimento mobile nativo** — quando necessário para aprofundar conceitos que o Ionic/Capacitor abstraem
- **Comparações com o ecossistema web** (React, Next.js, CSS, etc.) — quando fizer sentido didático para acelerar a compreensão

O foco é sempre explicar o **porquê** por trás das decisões, não apenas o **como**.

## Idioma / Language

- Toda comunicação, comentários no código e documentação: **português-BR**
- Código (variáveis, funções, classes, arquivos, rotas): **inglês**, seguindo as convenções da linguagem

## Comandos principais

```bash
# Desenvolvimento web (browser)
npm start              # ionic serve via Angular CLI (http://localhost:8100)

# Build
npm run build          # build de produção
npm run watch          # build incremental em modo development

# Qualidade de código
npm run lint           # ESLint (Angular + templates HTML)

# Testes
npm test               # Karma + Jasmine (todos os testes)
```

Para gerar páginas, componentes e serviços, usar o CLI do Ionic (que envolve o Angular CLI):

```bash
npx ionic generate page pages/nome-da-pagina
npx ionic generate component components/nome-do-componente
npx ionic generate service services/nome-do-servico
```

## Arquitetura

### Estrutura de módulos (NgModules)

O projeto usa **NgModules** (não Standalone Components). Cada página possui seu próprio conjunto de arquivos:

```
src/app/
  app.module.ts              # módulo raiz — registra AppComponent, importa IonicModule e AppRoutingModule
  app-routing.module.ts      # roteamento raiz com lazy loading por feature module
  app.component.ts/html      # shell da aplicação: apenas <ion-app> + <ion-router-outlet>
  home/
    home.module.ts           # feature module da página — importa IonicModule, CommonModule, FormsModule
    home-routing.module.ts   # roteamento local da feature
    home.page.ts/html/scss   # componente da página
```

Ao criar uma nova página, o padrão exige **quatro arquivos**: `*.module.ts`, `*-routing.module.ts`, `*.page.ts`, `*.page.html` (+ `.scss` e `.spec.ts`). O `IonicModule` deve ser importado no feature module de cada página.

### Roteamento com lazy loading

O `app-routing.module.ts` usa `loadChildren` para carregar feature modules sob demanda. Cada feature module tem seu próprio `*-routing.module.ts` que declara a rota com `path: ''`.

### Integração Capacitor

O `capacitor.config.ts` aponta `webDir: 'www'` (saída do build Angular). Para builds nativos:

```bash
npm run build
npx cap sync          # sincroniza www/ com as plataformas nativas
npx cap open android  # ou ios
```

### Temas e estilos

- `src/global.scss`: importa os CSS utilitários do Ionic (estrutura, tipografia, padding, etc.) e o tema escuro via `dark.system.css` (responde à preferência do sistema)
- `src/theme/variables.scss`: variáveis CSS do Ionic — customizar cores e tokens de design aqui

### ESLint

Regras relevantes configuradas em `.eslintrc.json`:
- `@angular-eslint/prefer-standalone: off` — mantém o padrão NgModules sem warnings
- Seletor de componentes: prefixo `app`, estilo `kebab-case`
- Sufixos permitidos para componentes: `Page` ou `Component`
- Seletor de diretivas: prefixo `app`, estilo `camelCase`

### Environments

`src/environments/environment.ts` (dev) e `environment.prod.ts` (prod). Variáveis de API (ex.: chave do TMDB) devem ser adicionadas aqui e nunca hardcoded nos serviços.
