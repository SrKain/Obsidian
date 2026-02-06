# Prompt para Lovable - Melhorias da Tela de Transações Mobile (iOS-like)

## Contexto

Este projeto é um aplicativo de controle financeiro (FinControl) construído com React + TypeScript + Tailwind CSS. A tela de Transações (Index.tsx) é a tela principal do app e precisa ser otimizada para mobile seguindo os padrões da Apple (Human Interface Guidelines), mantendo 100% da funcionalidade atual. **Todas as alterações devem se refletir APENAS no formato mobile, preservando a experiência desktop inalterada.**

## Objetivo

Transformar a tela de Transações em uma experiência mobile-first sofisticada e ergonômica, similar aos aplicativos nativos do iOS, com foco em interações por gestos e uso com uma mão.

---

## Tarefa 1: Floating Action Button (FAB) para Nova Transação

### O que fazer (APENAS MOBILE):

1. **Remover** o botão "Nova Transação" do fluxo principal da tela **apenas em mobile** (usando `md:block hidden`)
2. **Criar** Floating Action Button (FAB) fixo no canto inferior direito **visível apenas em mobile** (usando `md:hidden`)
3. **Manter** botão original visível no desktop

### Especificações técnicas:

#### FAB (mobile only):

```typescript
// Posição: fixed bottom-[calc(env(safe-area-inset-bottom)+5rem)] right-4 z-30
// Considerar altura da Bottom Tab Bar (4rem) + margem (1rem)
// Classe de visibilidade: md:hidden (visível apenas em mobile)
```

#### Estilo do FAB:

- Tamanho: `h-14 w-14`
- Forma: `rounded-full`
- Sombra: `shadow-lg`
- Background: `bg-primary hover:bg-primary/90`
- Ícone: `Plus` com `h-6 w-6 text-primary-foreground`
- Transição suave: `transition-all active:scale-95`

#### Botão Desktop:

- Manter botão "Nova Transação" original
- Adicionar classe: `hidden md:flex` (visível apenas em desktop)
- Manter todas as funcionalidades atuais

### Integração:

```tsx
// src/pages/Index.tsx

// Botão desktop (manter posição atual)
<Button className="hidden md:flex gap-2">
  <Plus className="h-4 w-4" /> Nova Transação
</Button>

// FAB mobile (adicionar antes do fechamento do Layout)
<div className="md:hidden fixed bottom-[calc(env(safe-area-inset-bottom)+5rem)] right-4 z-30">
  <Dialog>
    <DialogTrigger asChild>
      <Button 
        size="icon" 
        className="h-14 w-14 rounded-full shadow-lg bg-primary hover:bg-primary/90 transition-all active:scale-95"
      >
        <Plus className="h-6 w-6 text-primary-foreground" />
      </Button>
    </DialogTrigger>
    {/* Reutilizar DialogContent existente do TransactionForm */}
  </Dialog>
</div>
```

---

## Tarefa 2: Otimizar TransactionForm para Mobile (Sheet/Drawer)

### O que fazer (APENAS MOBILE):

1. **Desktop:** Manter Dialog centralizado (comportamento atual)
2. **Mobile:** Usar Sheet (Drawer) que desliza de baixo para cima

### Especificações:

#### Criar componente responsivo:

```tsx
// Usar Sheet em mobile, Dialog em desktop
// Mobile: Sheet com SheetContent className="h-[90vh]"
// Desktop: Dialog atual (sem alterações)
```

#### Melhorias do formulário (ambas as versões):

- Agrupar campos relacionados visualmente
- Usar `<Label>` clara para cada campo
- Considerar **Segmented Control** para `tipoFluxo` (Entrada/Saída) em vez de Select:
    
    ```tsx
    // Exemplo de Segmented Control (2 botões toggle)<div className="flex gap-1 p-1 bg-muted rounded-lg">  <Button     variant={tipoFluxo === 'entrada' ? 'default' : 'ghost'}    className="flex-1"    onClick={() => setTipoFluxo('entrada')}  >    Entrada  </Button>  <Button     variant={tipoFluxo === 'saida' ? 'default' : 'ghost'}    className="flex-1"    onClick={() => setTipoFluxo('saida')}  >    Saída  </Button></div>
    ```
    

#### Safe Areas:

- Sheet mobile deve ter `pb-safe` no conteúdo
- Garantir que botões de ação (Salvar/Cancelar) estejam sempre visíveis

---

## Tarefa 3: MonthSelector com Seletor Visual (APENAS MOBILE)

### O que fazer:

1. **Desktop:** Manter exatamente como está
2. **Mobile:** Adicionar funcionalidade de abrir um seletor de mês ao tocar no texto

### Especificações (mobile only):

#### Interação:

- Tocar no texto do mês (ex: "Janeiro 2024") abre um **Dialog ou Sheet**
- Conteúdo: Grade de meses do ano + seletor de ano
- Manter botões de seta para navegação sequencial

#### Implementação:

```tsx
// Mobile: texto do mês clicável
<button 
  onClick={openMonthPicker}
  className="md:pointer-events-none text-lg font-semibold"
>
  {formattedMonth}
</button>

// Desktop: texto estático (sem onClick)
```

#### Seletor de mês (Sheet mobile):

- Grid de 3x4 com os 12 meses
- Seletor de ano (dropdown ou stepper)
- Mês atual destacado
- Fechar automaticamente ao selecionar

---

## Tarefa 4: StatsCards Responsivo

### O que fazer:

1. **Desktop:** Manter grid `lg:grid-cols-4` (4 colunas)
2. **Mobile:** Implementar uma das opções:

#### Opção A (Recomendada): Carousel horizontal

```tsx
// Mobile: Carousel deslizável
// Desktop: Grid fixo (atual)

// Usar componente Carousel existente (src/components/ui/carousel.tsx)
// Mobile: 1 card por vez, deslizar horizontalmente
// Indicadores de página (dots) opcionais
```

#### Opção B: Stack vertical otimizado

```tsx
// Mobile: grid-cols-1 gap-3
// Tablet: grid-cols-2 gap-4
// Desktop: grid-cols-4 gap-6 (atual)
```

### Classes responsivas:

```tsx
// Se usar Carousel:
<div className="md:hidden">
  <Carousel>{statsCards}</Carousel>
</div>
<div className="hidden md:grid md:grid-cols-4 gap-6">
  {statsCards}
</div>

// Se usar Grid adaptativo:
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-3 sm:gap-4 lg:gap-6">
  {statsCards}
</div>
```

---

## Tarefa 5: TransactionFilters em Modal (APENAS MOBILE)

### O que fazer:

1. **Desktop:** Manter filtros inline (comportamento atual)
2. **Mobile:** Consolidar filtros em um Sheet/Dialog acessado por botão

### Especificações (mobile only):

#### Botão de filtro (mobile):

```tsx
<Button 
  variant="outline" 
  size="sm"
  className="md:hidden gap-2"
  onClick={openFiltersSheet}
>
  <Filter className="h-4 w-4" />
  Filtros
  {activeFiltersCount > 0 && (
    <Badge variant="secondary" className="ml-1">
      {activeFiltersCount}
    </Badge>
  )}
</Button>
```

#### Sheet de filtros:

- Título: "Filtros"
- Seções:
    1. **Classificação:** Segmented control ou lista de opções
    2. **Tags:** Grid de badges clicáveis (multi-seleção)
    3. **Período:** Range de datas (opcional)
- Botões de ação:
    - "Limpar filtros" (secondary)
    - "Aplicar" (primary)

#### Desktop:

- Manter componente `TransactionFilters` visível inline
- Adicionar classe `hidden md:block`

---

## Tarefa 6: Swipe Actions nos TransactionCards (APENAS MOBILE)

### O que fazer:

1. **Desktop:** Manter DropdownMenu (comportamento atual)
2. **Mobile:** Implementar swipe gestures para revelar ações

### Especificações (mobile only):

#### Comportamento:

- **Swipe esquerda:** Revelar botões "Editar" e "Excluir"
- **Swipe direita:** Revelar botão "Duplicar" (opcional)
- **Sem swipe:** Card com aparência limpa (sem DropdownMenu visível)

#### Implementação sugerida:

```tsx
// Pode usar biblioteca como react-swipeable ou implementar com touch events

// Botões revelados (swipe esquerda):
// - Editar: bg-blue-500 text-white (ícone Pencil)
// - Excluir: bg-red-500 text-white (ícone Trash2)

// Layout do card em mobile:
<div className="relative overflow-hidden">
  {/* Camada de ações (atrás do card) */}
  <div className="absolute inset-y-0 right-0 flex">
    <button className="w-20 bg-blue-500 flex items-center justify-center">
      <Pencil className="h-5 w-5 text-white" />
    </button>
    <button className="w-20 bg-red-500 flex items-center justify-center">
      <Trash2 className="h-5 w-5 text-white" />
    </button>
  </div>
  
  {/* Card principal (desliza sobre as ações) */}
  <div className="relative bg-card transform transition-transform">
    {/* Conteúdo do card */}
  </div>
</div>
```

#### Desktop:

- Manter DropdownMenu com as 3 ações
- Card sem funcionalidade de swipe

#### Confirmação de exclusão:

- Manter AlertDialog atual
- Funciona tanto para swipe (mobile) quanto dropdown (desktop)

---

## Tarefa 7: Edição de Transação em Sheet (APENAS MOBILE)

### O que fazer:

1. **Remover** modo de edição inline do TransactionCard **apenas em mobile**
2. **Abrir Sheet** com formulário de edição (reutilizar TransactionForm)
3. **Desktop:** pode manter edição inline ou também usar Dialog (decisão do desenvolvedor)

### Especificações (mobile):

#### Ao tocar "Editar" (via swipe ou menu):

```tsx
// Abrir Sheet com TransactionForm pré-preenchido
<Sheet open={isEditing} onOpenChange={setIsEditing}>
  <SheetContent side="bottom" className="h-[90vh]">
    <SheetHeader>
      <SheetTitle>Editar Transação</SheetTitle>
    </SheetHeader>
    <TransactionForm 
      mode="edit"
      initialData={transaction}
      onSave={handleUpdate}
      onCancel={() => setIsEditing(false)}
    />
  </SheetContent>
</Sheet>
```

#### Simplificação do TransactionCard:

- Remover estado `isEditing` e renderização condicional de formulário inline
- Card sempre exibe informações (modo leitura)
- Ações abrem modais/sheets apropriados

---

## Breakpoints e Responsividade

### CRÍTICO - Usar consistentemente:

- **Mobile:** `< 768px` (padrão, sem prefixo ou com classes específicas mobile)
- **Desktop:** `≥ 768px` (prefixo `md:`)

### Checklist de classes:

```tsx
// FAB: md:hidden (mobile only)
// Botão Nova Transação original: hidden md:flex (desktop only)
// Filtros inline: hidden md:block (desktop only)
// Botão de filtro: md:hidden (mobile only)
// Swipe actions: implementar apenas quando largura < 768px
// DropdownMenu no card: hidden md:block (desktop only)
```

---

## O Que NÃO Muda

- Lógica de negócio (hooks, queries, mutations)
- Fluxo de autenticação
- Sistema de rotas
- Componentes shadcn/ui (apenas reorganizados)
- Validações de formulário
- Cálculos de estatísticas
- **Layout e comportamento desktop (permanece inalterado)**
- Temas (dark/light mode)

---

## Checklist de Responsividade - Tela de Transações

### Desktop (≥768px):

- [ ] Botão "Nova Transação" inline visível
- [ ] FAB oculto
- [ ] TransactionForm em Dialog centralizado
- [ ] MonthSelector com texto não-clicável
- [ ] StatsCards em grid de 4 colunas
- [ ] TransactionFilters inline visível
- [ ] TransactionCard com DropdownMenu
- [ ] Sem swipe actions

### Mobile (<768px):

- [ ] Botão "Nova Transação" inline oculto
- [ ] FAB visível e funcional (canto inferior direito)
- [ ] TransactionForm em Sheet (desliza de baixo)
- [ ] MonthSelector com texto clicável (abre seletor)
- [ ] StatsCards em Carousel ou grid de 1-2 colunas
- [ ] TransactionFilters em Sheet (botão "Filtros")
- [ ] TransactionCard com swipe actions
- [ ] DropdownMenu oculto
- [ ] Safe areas respeitadas (pb-safe, bottom positioning)

---

## Arquivos Resumo

|Arquivo|Ação|
|---|---|
|`src/pages/Index.tsx`|Modificar (adicionar FAB mobile com `md:hidden`, ocultar botão desktop em mobile com `hidden md:flex`)|
|`src/components/transactions/TransactionForm.tsx`|Modificar (responsivo: Sheet em mobile, Dialog em desktop)|
|`src/components/transactions/MonthSelector.tsx`|Modificar (adicionar seletor de mês clicável apenas em mobile)|
|`src/components/transactions/StatsCards.tsx`|Modificar (Carousel em mobile ou grid responsivo)|
|`src/components/transactions/TransactionFilters.tsx`|Modificar (inline em desktop com `hidden md:block`, Sheet em mobile)|
|`src/components/transactions/TransactionCard.tsx`|Modificar (swipe actions em mobile, DropdownMenu em desktop com `hidden md:block`)|

---

## Bibliotecas Sugeridas (Opcionais)

### Para Swipe Actions:

- `react-swipeable` - gestos de deslizar
- Ou implementação manual com `onTouchStart`, `onTouchMove`, `onTouchEnd`

### Para Carousel (se optar):

- Componente `Carousel` já existe em `src/components/ui/carousel.tsx`
- Usar Embla Carousel (já configurado no projeto)

---

## Resultado Esperado

**Mobile:**

- Interface fluida e ergonômica para uso com uma mão
- Gestos intuitivos (swipe, tap)
- Modais deslizando de baixo (sheets)
- FAB sempre acessível
- Filtros organizados em modal

**Desktop:**

- Interface inalterada
- Navegação tradicional mantida
- Todas as funcionalidades preservadas

**Ambos:**

- Zero perda de funcionalidade
- Transições suaves
- Safe areas respeitadas
- Performance mantida