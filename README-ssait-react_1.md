# SSAIT — Migração para React

> **Objetivo:** Converter o site institucional `index.html` em uma aplicação React moderna, mantendo 100% da identidade visual, com arquitetura preparada para crescimento (novas rotas, dados dinâmicos, blog).

---

## Stack

| Ferramenta | Versão | Motivo |
|---|---|---|
| Vite | 5.x | Build rápido, DX excelente |
| React | 18.x | — |
| React Router DOM | 6.x | Rotas declarativas |
| CSS Modules | nativo Vite | Escopo de estilos sem overhead |
| — | — | Sem Tailwind, sem styled-components — manter os CSS tokens do design original |

> **Por que não Next.js?** O site é estático/institucional. Next.js adiciona complexidade desnecessária agora. Se precisar de SSR/SEO avançado no futuro, a migração é simples.

---

## Estrutura de Pastas

```
ssait-web/
├── public/
│   └── favicon.svg
├── src/
│   ├── assets/              # imagens, ícones
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Navbar.jsx
│   │   │   └── Footer.jsx
│   │   ├── sections/        # seções da Home
│   │   │   ├── Hero.jsx
│   │   │   ├── Frentes.jsx
│   │   │   ├── Projetos.jsx
│   │   │   ├── Servicos.jsx
│   │   │   ├── Sobre.jsx
│   │   │   └── CTA.jsx
│   │   └── ui/              # componentes reutilizáveis
│   │       ├── Button.jsx
│   │       ├── SectionHeader.jsx
│   │       ├── ProjectCard.jsx
│   │       ├── ServiceCard.jsx
│   │       ├── MetricCard.jsx
│   │       └── StackGrid.jsx
│   ├── data/                # conteúdo fora do JSX
│   │   ├── projects.js
│   │   ├── services.js
│   │   └── stack.js
│   ├── hooks/
│   │   └── useReveal.js     # IntersectionObserver reutilizável
│   ├── pages/
│   │   ├── Home.jsx         # monta todas as sections
│   │   ├── ProjectDetail.jsx  # /projetos/:slug (futuro)
│   │   └── NotFound.jsx
│   ├── styles/
│   │   ├── tokens.css       # CSS variables do design (--navy, --blue, etc.)
│   │   └── global.css       # reset, body, typography base
│   ├── App.jsx              # Router + layout shell
│   └── main.jsx             # entry point
├── index.html
├── vite.config.js
└── package.json
```

---

## Roteamento

```jsx
// App.jsx
<BrowserRouter>
  <Navbar />
  <Routes>
    <Route path="/"                    element={<Home />} />
    <Route path="/projetos/:slug"      element={<ProjectDetail />} />
    <Route path="/blog"                element={<Blog />} />         {/* futuro */}
    <Route path="/blog/:slug"          element={<BlogPost />} />     {/* futuro */}
    <Route path="*"                    element={<NotFound />} />
  </Routes>
  <Footer />
</BrowserRouter>
```

> Rotas `/blog` podem ser criadas depois — a estrutura já está preparada. Basta criar o `page` e registrar a rota.

---

## Componentes — Contratos

### `<Button variant="primary|outline" href? onClick? />`
Substitui `.btn-primary` e `.btn-outline`. Renderiza `<a>` se `href` for passado, `<button>` caso contrário.

### `<SectionHeader tag label title titleHighlight sub />`
Substitui o padrão repetido `.section-tag + .section-title + .section-sub` em todas as seções.

### `<ProjectCard title status tags description slug? />`
Renderiza um card de projeto. `slug` opcional — quando presente, o card vira link para `/projetos/:slug`.

### `<ServiceCard num title description />`
Cards numerados da seção Serviços.

### `<MetricCard value suffix label />`
Cards de métricas da seção Sobre.

### `useReveal(ref)`
```js
// hooks/useReveal.js
// Retorna { isVisible } usando IntersectionObserver
// Substitui o script global do HTML original
```

---

## Dados (separados do JSX)

```js
// data/projects.js
export const projects = [
  {
    slug: "smartlist",
    title: "SmartList",
    status: "active",         // "active" | "planned"
    description: "...",
    tags: ["React", "LLM", "OCR", "Java 21"],
  },
  // ...
];

// data/services.js
export const services = [
  { num: "01", title: "Diagnóstico de sistema legado", description: "..." },
  // ...
];

// data/stack.js
export const stack = [
  { label: "Java 21", highlight: true },
  // ...
];
```

> **Vantagem:** Adicionar um projeto novo = editar apenas `projects.js`, sem tocar em JSX.

---

## Estilos — Estratégia

- `tokens.css` contém **todas** as CSS variables do design atual (copiadas do `:root` do HTML).
- Cada componente usa **CSS Modules** (`.module.css`) para escopo local.
- Sem biblioteca de CSS externa — o design já está resolvido.

```css
/* styles/tokens.css — importado uma vez no main.jsx */
:root {
  --navy: #0a1e3d;
  --navy-mid: #0f2a55;
  --blue-bright: #2271d4;
  /* ... todos os tokens originais ... */
}
```

---

## Deploy

O site é 100% estático — sem backend. Opções recomendadas:

| Plataforma | Custo | Observação |
|---|---|---|
| **Vercel** | Grátis | `vite build` + deploy automático no push |
| **Netlify** | Grátis | Igual ao Vercel |
| **GitHub Pages** | Grátis | Requer config de `base` no `vite.config.js` |

---

## Ordem de Implementação Sugerida

1. `vite create` + instalar React Router
2. Copiar `tokens.css` e `global.css` do HTML original
3. Criar `Navbar` e `Footer`
4. Criar `Home.jsx` com todas as sections em sequência (copiar HTML → JSX)
5. Extrair `ui/` components (Button, SectionHeader, etc.)
6. Mover dados para `data/`
7. Criar `useReveal` hook
8. Configurar rotas e `ProjectDetail` placeholder
9. Deploy na Vercel

---

## O que NÃO mudar agora

- Design visual — manter 100% fiel ao HTML atual
- Fontes (Syne + DM Sans via Google Fonts)
- Animações CSS (`fadeUp`, `reveal`)
- Paleta de cores

---

## Futuro (fora do escopo inicial)

- [ ] Blog com MDX
- [ ] Página de detalhe de cada projeto (SmartList, HelpTea, GarimpeiMóvel)
- [ ] Formulário de contato com backend
- [ ] Internacionalização PT/EN (i18n)
- [ ] Dark mode toggle

---

*Gerado em: 2026-04-15*
