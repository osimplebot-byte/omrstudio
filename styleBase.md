# OMR Studio - Design System Specifications v1.0

## 📱 Mobile-First & Responsive

### Breakpoints
```css
Mobile: 320px - 767px (base)
Tablet: 768px - 1023px
Desktop: 1024px+
```

### Prevenção de Zoom em Inputs Mobile
```css
input, textarea, select {
  font-size: 16px !important; /* Evita zoom no iOS */
}

@media (min-width: 768px) {
  input, textarea, select {
    font-size: 15px; /* Tamanho ideal para desktop */
  }
}
```

### Viewport Meta Tag
```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=5">
```

---

## 🎨 Color Palette

### Cores Principais
| Nome | Light Mode | Dark Mode | Uso |
|------|------------|-----------|-----|
| **Primary** | `#E91E63` | `#E91E63` | Botões, links, destaques |
| **Primary Dark** | `#C2185B` | `#C2185B` | Hover states |
| **Background** | `#FFFFFF` | `#0F0F1E` | Fundo principal |
| **Background Light** | `#F8F9FA` | `#1A1A2E` | Seções alternadas |
| **Card Background** | `#FFFFFF` | `#1E1E2E` | Cards, modais, drawer |
| **Text Primary** | `#0F0F1E` | `#FFFFFF` | Texto principal |
| **Text Secondary** | `#5A6772` | `#A8B2BC` | Texto secundário |
| **Border** | `#E5E7EB` | `#2D2D3E` | Bordas e divisores |
| **Input Background** | `#F9FAFB` | `#252536` | Campos de formulário |

### Gradientes
```css
/* Gradient Avatar/Icons */
background: linear-gradient(135deg, #E91E63, #C2185B);
```

---

## ✏️ Typography

### Font Families
```css
/* Headings & Logo */
font-family: 'Montserrat', sans-serif;
font-weight: 600, 700;

/* Body Text */
font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
font-weight: 400, 500, 600, 700;

/* Code/Monospace */
font-family: 'JetBrains Mono', monospace;
```

### Import Fonts
```html
<link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@600;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
```

### Scale
| Elemento | Size | Weight | Line Height | Uso |
|----------|------|--------|-------------|-----|
| **H1** | 24px | 700 | 1.3 | Títulos principais |
| **H2** | 20px | 700 | 1.3 | Subtítulos |
| **H3** | 18px | 600 | 1.4 | Seções |
| **H4** | 16px | 600 | 1.4 | Cards headers |
| **Body** | 15px | 400 | 1.5 | Texto padrão |
| **Small** | 13px | 400 | 1.5 | Descrições |
| **Tiny** | 12px | 400 | 1.4 | Labels, badges |
| **Input** | 16px* | 400 | 1.5 | Campos de formulário |

*16px obrigatório em mobile para evitar zoom

---

## 📐 Spacing System

### Scale (baseado em 4px)
```css
4px   - xs   (gap mínimo)
8px   - sm   (espaçamento interno pequeno)
12px  - md   (gap entre elementos relacionados)
16px  - lg   (padding padrão)
20px  - xl   (padding de containers mobile)
24px  - 2xl  (padding de containers desktop)
32px  - 3xl  (margem entre seções)
40px  - 4xl  (espaçamento grande)
48px  - 5xl  (touch targets)
64px  - 6xl  (height de navs)
```

### Aplicações Comuns
```css
/* Padding interno de cards */
padding: 20px; /* mobile */
padding: 24px; /* desktop */

/* Gap entre elementos */
gap: 12px; /* relacionados */
gap: 16px; /* padrão */

/* Margem entre seções */
margin-bottom: 24px; /* mobile */
margin-bottom: 32px; /* desktop */

/* Padding de botões */
padding: 14px 24px;

/* Touch targets */
min-width: 48px;
min-height: 48px;
```

---

## 🔲 Border Radius

### Scale
```css
/* Inputs, campos pequenos */
border-radius: 8px;

/* Botões icon/ação */
border-radius: 8px;

/* Cards, containers */
border-radius: 12px;

/* Botões principais */
border-radius: 12px;

/* Botões secundários/chips */
border-radius: 20px;

/* Mensagens de chat - usuário */
border-radius: 18px 18px 4px 18px;

/* Mensagens de chat - bot */
border-radius: 18px 18px 18px 4px;

/* Input de chat */
border-radius: 24px;

/* Botões circulares (FAB, avatar) */
border-radius: 50%;
```

---

## 🌑 Shadows

### Níveis
```css
/* Low - hover states */
box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);

/* Medium - cards, drawer */
box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);

/* High - modals, popovers */
box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);

/* Floating - FAB buttons */
box-shadow: 0 8px 24px rgba(233, 30, 99, 0.3);
```

---

## 🏗️ Layout Structure

### Top Navigation
```css
height: 64px;
background: var(--card-bg);
border-bottom: 1px solid var(--border);
padding: 0 20px;
position: sticky;
top: 0;
z-index: 100;

/* Layout interno */
display: flex;
align-items: center;
justify-content: space-between;
```

**Estrutura:**
- **Left:** Hamburger button (40x40px)
- **Center:** Logo + Text
- **Right:** Theme button + Logout button (40x40px cada)

### Side Drawer
```css
width: 280px;
height: 100vh;
background: var(--card-bg);
border-right: 1px solid var(--border);
position: fixed;
top: 0;
left: -280px; /* closed */
z-index: 300;
transition: left 0.3s ease-in-out;

/* Open state */
left: 0;
```

**Estrutura:**
- **Header:** Logo + versão + botão fechar (altura auto)
- **Menu Items:** Lista de navegação (flex: 1)
- **Footer:** Dica/info adicional (altura auto)

**Menu Item:**
```css
width: 100%;
padding: 14px 16px;
border-radius: 10px;
border-left: 3px solid transparent; /* active: #E91E63 */
display: flex;
align-items: center;
gap: 12px;
margin-bottom: 4px;

/* Active state */
background: var(--input-bg);
border-left-color: #E91E63;
```

### Bottom Navigation
```css
height: 64px;
background: var(--card-bg);
border-top: 1px solid var(--border);
position: fixed;
bottom: 0;
left: 0;
right: 0;
z-index: 100;

/* Layout interno */
display: flex;
justify-content: space-around;
align-items: center;
padding: 0 8px;

/* Hide em desktop */
@media (min-width: 768px) {
  display: none;
}
```

**Tab Item:**
```css
display: flex;
flex-direction: column;
align-items: center;
gap: 4px;
padding: 8px 12px;
min-width: 60px;

/* Icon: 24px */
/* Label: 11px, weight 400/600 */
```

### Main Content Area
```css
flex: 1;
overflow: auto;
padding: 24px 20px 80px 20px; /* bottom padding para bottom nav */

/* Centralizar conteúdo vazio (opcional) */
display: flex;
align-items: center;
justify-content: center;
```

### Overlay (Drawer Backdrop)
```css
position: fixed;
top: 0;
left: 0;
right: 0;
bottom: 0;
background: rgba(0, 0, 0, 0.5);
z-index: 200; /* entre content (100) e drawer (300) */
animation: fadeIn 0.2s ease-in-out;
```

---

## 🔘 Components Specifications

### Botões

#### Botão Principal
```css
padding: 14px 24px;
min-height: 48px;
background: #E91E63;
color: #FFFFFF;
border: none;
border-radius: 12px;
font-size: 16px;
font-weight: 600;
cursor: pointer;
transition: all 0.2s;

/* Hover */
background: #C2185B;

/* Active/Press */
transform: scale(0.98);

/* Disabled */
opacity: 0.5;
cursor: not-allowed;
```

#### Botão Secundário
```css
padding: 14px 24px;
min-height: 48px;
background: transparent;
color: #E91E63;
border: 1px solid #E91E63;
border-radius: 12px;
font-size: 16px;
font-weight: 600;
cursor: pointer;
transition: all 0.2s;

/* Hover */
background: rgba(233, 30, 99, 0.08);
```

#### Icon Button (Nav)
```css
width: 40px;
height: 40px;
background: transparent;
border: none;
border-radius: 8px;
display: flex;
align-items: center;
justify-content: center;
cursor: pointer;
transition: background 0.2s;

/* Hover */
background: var(--input-bg);

/* Icon size: 20-24px */
```

#### Floating Action Button (FAB)
```css
width: 48px;
height: 48px;
background: #E91E63;
border: none;
border-radius: 50%;
display: flex;
align-items: center;
justify-content: center;
cursor: pointer;
box-shadow: 0 8px 24px rgba(233, 30, 99, 0.3);
transition: transform 0.2s;

/* Press */
transform: scale(0.95);
```

### Inputs

```css
width: 100%;
padding: 14px 16px;
background: var(--input-bg);
color: var(--text);
border: 1px solid var(--border);
border-radius: 12px;
font-size: 16px; /* CRÍTICO: evita zoom mobile */
outline: none;
transition: border 0.2s;
box-sizing: border-box;

/* Focus */
border-color: #E91E63;

/* Error */
border-color: #EF4444;

/* Disabled */
opacity: 0.6;
cursor: not-allowed;
```

**Input com Label:**
```css
/* Label */
font-size: 14px;
font-weight: 500;
color: var(--text);
margin-bottom: 8px;
display: block;
```

### Cards

```css
background: var(--card-bg);
border: 1px solid var(--border);
border-radius: 12px;
padding: 20px;
transition: all 0.2s;

/* Hover (se clicável) */
transform: translateY(-2px);
box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
cursor: pointer;
```

### Chat Bubbles

#### Mensagem do Usuário (Right)
```css
max-width: 75%;
padding: 12px 16px;
background: #E91E63;
color: #FFFFFF;
border-radius: 18px 18px 4px 18px;
font-size: 15px;
line-height: 1.5;
margin-left: auto; /* alinha à direita */
```

#### Mensagem do Bot (Left)
```css
max-width: 75%;
padding: 12px 16px;
background: var(--card-bg);
color: var(--text);
border: 1px solid var(--border);
border-radius: 18px 18px 18px 4px;
font-size: 15px;
line-height: 1.5;
```

### Quick Reply Chips

```css
padding: 10px 16px;
background: var(--card-bg);
color: var(--text);
border: 1px solid var(--border);
border-radius: 20px;
font-size: 14px;
cursor: pointer;
transition: all 0.2s;
white-space: nowrap;

/* Hover */
border-color: #E91E63;
color: #E91E63;
```

### Badges/Status

```css
padding: 6px 12px;
border-radius: 6px;
font-size: 12px;
font-weight: 600;
display: inline-flex;
align-items: center;
gap: 6px;

/* Conectado */
background: #10B981;
color: #FFFFFF;

/* Conectando */
background: #F59E0B;
color: #FFFFFF;

/* Desconectado */
background: #EF4444;
color: #FFFFFF;
```

### Toast Notifications

```css
padding: 14px 16px;
border-radius: 12px;
font-size: 14px;
font-weight: 500;
display: flex;
align-items: center;
gap: 8px;
box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
animation: slideUp 0.3s ease-out;
position: fixed;
top: 80px; /* abaixo do top nav */
left: 50%;
transform: translateX(-50%);
z-index: 1000;
min-width: 280px;
max-width: 400px;

/* Success */
background: #10B981;
color: #FFFFFF;

/* Error */
background: #EF4444;
color: #FFFFFF;
```

### Avatar/Icon Container

```css
/* Pequeno (nav) */
width: 32px;
height: 32px;

/* Médio (lista) */
width: 40px;
height: 40px;

/* Grande (perfil) */
width: 64px;
height: 64px;

/* Estilo padrão */
background: linear-gradient(135deg, #E91E63, #C2185B);
border-radius: 50%;
display: flex;
align-items: center;
justify-content: center;
color: #FFFFFF;
```

---

## 🎭 Animations & Transitions

### Padrão
```css
transition: all 0.2s ease-in-out;
```

### Estados Específicos
```css
/* Background/Color changes */
transition: background 0.2s, color 0.2s;

/* Transform only */
transition: transform 0.2s;

/* Drawer slide */
transition: left 0.3s ease-in-out;
```

### Keyframes

```css
/* Fade In (Overlay) */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Slide Up (Toast) */
@keyframes slideUp {
  from {
    transform: translate(-50%, 20px);
    opacity: 0;
  }
  to {
    transform: translate(-50%, 0);
    opacity: 1;
  }
}

/* Slide In From Left (Drawer) */
@keyframes slideInLeft {
  from { transform: translateX(-100%); }
  to { transform: translateX(0); }
}
```

### Hover States
```css
/* Elevação suave */
transform: translateY(-2px);
box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);

/* Escala */
transform: scale(1.02);

/* Background highlight */
background: var(--input-bg);
```

### Active/Press States
```css
/* Botões */
transform: scale(0.98);

/* Cards clicáveis */
transform: scale(0.99);
```

---

## 📦 CSS Variables (Recomendado)

```css
:root {
  /* Colors - Light Mode */
  --primary: #E91E63;
  --primary-dark: #C2185B;
  --background: #FFFFFF;
  --background-light: #F8F9FA;
  --card-bg: #FFFFFF;
  --text: #0F0F1E;
  --text-secondary: #5A6772;
  --border: #E5E7EB;
  --input-bg: #F9FAFB;

  /* Spacing */
  --space-xs: 4px;
  --space-sm: 8px;
  --space-md: 12px;
  --space-lg: 16px;
  --space-xl: 20px;
  --space-2xl: 24px;
  --space-3xl: 32px;
  --space-4xl: 40px;
  --space-5xl: 48px;
  --space-6xl: 64px;

  /* Border Radius */
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 20px;
  --radius-xl: 24px;
  --radius-full: 50%;

  /* Shadows */
  --shadow-low: 0 1px 3px rgba(0, 0, 0, 0.08);
  --shadow-medium: 0 2px 8px rgba(0, 0, 0, 0.08);
  --shadow-high: 0 4px 16px rgba(0, 0, 0, 0.12);
  --shadow-fab: 0 8px 24px rgba(233, 30, 99, 0.3);

  /* Z-index */
  --z-base: 1;
  --z-nav: 100;
  --z-overlay: 200;
  --z-drawer: 300;
  --z-modal: 400;
  --z-toast: 1000;
}

[data-theme="dark"] {
  --background: #0F0F1E;
  --background-light: #1A1A2E;
  --card-bg: #1E1E2E;
  --text: #FFFFFF;
  --text-secondary: #A8B2BC;
  --border: #2D2D3E;
  --input-bg: #252536;
}
```

### Exemplo de Uso
```css
.button-primary {
  background: var(--primary);
  color: white;
  padding: var(--space-lg) var(--space-2xl);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-medium);
  transition: all 0.2s;
}

.button-primary:hover {
  background: var(--primary-dark);
}
```

---

## ♿ Accessibility

### Touch Targets
```css
/* Mínimo absoluto */
min-width: 44px;
min-height: 44px;

/* Recomendado (thumb-friendly) */
min-width: 48px;
min-height: 48px;
```

### Contraste de Cores
- Texto normal (15px): mínimo 4.5:1 ✅
- Texto grande (18px+): mínimo 3:1 ✅
- Primary (#E91E63) em branco: 5.52:1 ✅
- Primary (#E91E63) em dark (#0F0F1E): 8.2:1 ✅

### Focus States
```css
/* Navegação por teclado */
outline: 2px solid var(--primary);
outline-offset: 2px;

/* Remover outline padrão apenas se houver alternativa visual */
:focus-visible {
  outline: 2px solid var(--primary);
  outline-offset: 2px;
}
```

### ARIA Labels
```html
<!-- Botão hamburger -->
<button aria-label="Abrir menu de navegação">
  <MenuIcon />
</button>

<!-- Botão tema -->
<button aria-label="Alternar tema">
  <SunIcon />
</button>

<!-- Nav items -->
<button aria-label="Ir para Dados" aria-current="page">
  <DatabaseIcon />
  <span>Dados</span>
</button>
```

---

## 📱 Platform-Specific

### iOS
```css
/* Remover tap highlight */
-webkit-tap-highlight-color: transparent;

/* Suavizar fontes */
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;

/* Safe area (notch) */
padding-top: env(safe-area-inset-top);
padding-bottom: env(safe-area-inset-bottom);
```

### Android
```css
/* Remover tap highlight */
-webkit-tap-highlight-color: transparent;

/* Scroll suave */
-webkit-overflow-scrolling: touch;
```

### PWA Meta Tags
```html
<meta name="theme-color" content="#E91E63">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
```

---

## 🎯 Princípios de UX

### 1. Linguagem Direta
- Zero jargão técnico
- Mensagens curtas e objetivas (máx. 1 linha)
- Tom amigável e humano
- Emoji com moderação (apenas quando adiciona clareza)

### 2. Feedback Imediato
- Toasts aparecem em até 200ms
- Animações entre 200-300ms
- Loading states visíveis
- Confirmações discretas

### 3. Mobile-First
- Design pensado para uso com polegar
- Inputs com 16px para evitar zoom
- Áreas de toque generosas (48px)
- Navegação acessível na parte inferior

### 4. Microinterações
- Hover states em todos os elementos clicáveis
- Press feedback (scale 0.98)
- Transições suaves entre estados
- Animações não bloqueiam interação

### 5. Consistência
- Mesmos padrões em todas as telas
- Componentes reutilizáveis
- Comportamentos previsíveis
- Hierarquia visual clara

---

## 🚀 Checklist de Implementação

### Setup Inicial
- [ ] Configurar viewport meta tag
- [ ] Importar fontes Google (Montserrat, Inter)
- [ ] Configurar CSS variables para temas
- [ ] Adicionar reset/normalize CSS
- [ ] Configurar box-sizing: border-box

### Layout Base
- [ ] Implementar Top Navigation (64px)
- [ ] Implementar Side Drawer (280px)
- [ ] Implementar Bottom Navigation (64px, mobile only)
- [ ] Adicionar overlay do drawer
- [ ] Testar transições e animações

### Componentes Core
- [ ] Botões (primary, secondary, icon, FAB)
- [ ] Inputs com 16px em mobile
- [ ] Cards com hover states
- [ ] Chat bubbles (user/bot)
- [ ] Quick reply chips
- [ ] Badges de status
- [ ] Toast notifications

### Estados Interativos
- [ ] Hover states em todos os clicáveis
- [ ] Active/press feedback
- [ ] Focus states para teclado
- [ ] Loading states
- [ ] Disabled states
- [ ] Error states

### Responsividade
- [ ] Testar em iPhone SE (320px)
- [ ] Testar em iPhone Pro (390px)
- [ ] Testar em iPad (768px)
- [ ] Testar em Desktop (1024px+)
- [ ] Verificar safe areas (notch)

### Acessibilidade
- [ ] Touch targets mínimo 48px
- [ ] Contraste de cores adequado
- [ ] ARIA labels em ícones
- [ ] Navegação por teclado
- [ ] Screen reader friendly

### Performance
- [ ] Animações em 60fps
- [ ] Transições não bloqueantes
- [ ] Lazy load de imagens
- [ ] Otimizar fontes (woff2)
- [ ] Minificar CSS/JS

### Testes
- [ ] Testar em dispositivos reais
- [ ] Testar modo landscape
- [ ] Testar tema claro/escuro
- [ ] Testar com fontes grandes (acessibilidade)
- [ ] Testar conexão lenta

---

## 📋 Estrutura de Arquivos Sugerida

```
/src
  /styles
    reset.css
    variables.css
    layout.css
    components.css
    utilities.css
  /components
    /Layout
      TopNav.jsx
      SideDrawer.jsx
      BottomNav.jsx
    /UI
      Button.jsx
      Input.jsx
      Card.jsx
      Badge.jsx
      Toast.jsx
    /Chat
      ChatBubble.jsx
      QuickReply.jsx
  /screens
    Dados.jsx
    TestDrive.jsx
    Conexoes.jsx
    Ajuda.jsx
```

---

## 🔗 Recursos Úteis

### Design Tools
- Figma: Prototipagem e design
- Adobe Color: Verificação de contraste
- CSS Grid Generator: Layout grids
- Cubic Bezier: Curvas de animação

### Testing Tools
- Chrome DevTools: Device emulation
- Lighthouse: Performance e A11y
- BrowserStack: Cross-browser testing
- WAVE: Accessibility checker

### Libraries (Opcional)
- Framer Motion: Animações avançadas
- React Spring: Physics-based animations
- Tailwind CSS: Utility-first styling
- Radix UI: Componentes acessíveis

---

**Versão:** 1.0  
**Última atualização:** Novembro 2025  
**Mantido por:** Equipe OMR Studio

---

## 📝 Notas Importantes

1. **Input Font Size:** Sempre 16px em mobile para evitar zoom. Isso não é negociável.
2. **Touch Targets:** Mínimo 48x48px para boa experiência mobile.
3. **Z-index:** Seguir a hierarquia definida (100, 200, 300, 400, 1000).
4. **Drawer Width:** 280px fixo para manter consistência.
5. **Bottom Nav:** Apenas em mobile, esconder em desktop.
6. **Transições:** Sempre entre 200-300ms para sentir responsivo.
7. **Tema:** Sempre usar CSS variables para suportar light/dark.
8. **Acessibilidade:** Testar com teclado e screen reader antes de lançar.