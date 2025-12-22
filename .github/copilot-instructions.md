# Instruções de Estilo para Documentação - GOPAG

## Objetivo
Manter a documentação com tom profissional, direto e sem redundâncias, eliminando linguagem informal e possessiva.

## Padrões de Escrita

### ❌ EVITAR - Linguagem Informal/Redundante

#### 1. Pronomes diretos ao usuário
- ❌ "você pode ver"
- ❌ "você precisa"
- ❌ "você deve"
- ❌ "você irá"
- ❌ "quando você for"
- ❌ "para você"

#### 2. Linguagem possessiva informal
- ❌ "nosso sistema"
- ❌ "nossa plataforma"
- ❌ "nosso exemplo"
- ❌ "nossa demonstração"

#### 3. Expressões coloquiais
- ❌ "vejamos"
- ❌ "vamos ver"
- ❌ "vamos fazer"

### ✅ PREFERIR - Linguagem Profissional

#### 1. Formas impessoais ou passivas
- ✅ "é possível ver" / "está visível"
- ✅ "será necessário" / "é necessário"
- ✅ "deve-se" / "recomenda-se"
- ✅ "ao criar" / "durante a criação"

#### 2. Artigos definidos neutros
- ✅ "o sistema"
- ✅ "a plataforma"
- ✅ "o exemplo"
- ✅ "a demonstração"

#### 3. Imperativo ou presente do indicativo
- ✅ "veja"
- ✅ "observe"
- ✅ "confira"

## Exemplos de Transformações

### Exemplo 1: Barra de ferramentas
❌ **Antes:**
```markdown
Ao lado direito da tela, você pode ver a `barra de ferramentas`. Vejamos abaixo cada opção:
```

✅ **Depois:**
```markdown
Ao lado direito da tela, está a `barra de ferramentas`. Veja abaixo cada opção:
```

### Exemplo 2: Campos obrigatórios
❌ **Antes:**
```markdown
No momento do cadastro, você precisará preencher alguns campos obrigatórios em nosso sistema.
```

✅ **Depois:**
```markdown
No momento do cadastro, será necessário preencher alguns campos obrigatórios no sistema.
```

### Exemplo 3: Ações disponíveis
❌ **Antes:**
```markdown
Quando você for criar uma venda, você pode escolher entre várias opções disponíveis em nossa plataforma.
```

✅ **Depois:**
```markdown
Ao criar uma venda, é possível escolher entre várias opções disponíveis na plataforma.
```

### Exemplo 4: Exemplos e demonstrações
❌ **Antes:**
```markdown
Em nosso exemplo, vamos escolher a opção de boleto. Observe nossa demonstração abaixo:
```

✅ **Depois:**
```markdown
No exemplo, vamos escolher a opção de boleto. Observe a demonstração abaixo:
```

### Exemplo 5: Remoção de redundâncias
❌ **Antes:**
```markdown
O campo de produtos tem busca automática, mas você pode digitar o nome do produto para buscar caso queira.
```

✅ **Depois:**
```markdown
O campo de produtos tem busca automática, mas também é possível digitar o nome do produto para buscar caso queira.
```

## Regras Específicas por Contexto

### Interface do Usuário
- Use presente do indicativo para elementos visíveis: "está", "há", "encontra-se"
- Evite "você pode ver" → Use "está visível", "está disponível", "há"

### Ações e Procedimentos
- Prefira infinitivo ou formas impessoais: "é possível", "será necessário"
- Evite "você precisa fazer" → Use "é necessário fazer", "deve-se fazer"

### Exemplos e Demonstrações
- Use "o exemplo", "a demonstração", nunca "nosso exemplo"
- Prefira "veja", "observe", "confira" ao invés de "vejamos", "vamos ver"

### Requisitos de Sistema
- Use "o sistema", "a plataforma", nunca "nosso sistema", "nossa plataforma"
- Mantenha foco no produto, não na empresa

## Exceções Permitidas

### Emojis
- ✅ Podem ser mantidos quando apropriados: 😁, 😎, 👍
- Adicam tom amigável sem comprometer profissionalismo

### Avisos e Alertas
- ✅ Mantenha blocos de hint/warning/danger do GitBook:
```markdown
{% hint style="info" %}
**Informação:** Texto do aviso.
{% endhint %}
```

### Links e Referências
- ✅ "clique aqui", "acesse o guia" são aceitáveis para chamadas à ação

## Checklist de Revisão

Ao revisar ou criar nova documentação markdown, verifique:

- [ ] Substituiu "você pode/precisa/deve" por formas impessoais
- [ ] Trocou "nosso/nossa" por artigos definidos neutros
- [ ] Converteu "vejamos" para "veja" ou "observe"
- [ ] Removeu "para você" redundantes
- [ ] Eliminou repetições desnecessárias
- [ ] Manteve tom profissional mas acessível
- [ ] Preservou estrutura e formatação markdown
- [ ] Não alterou nomes de arquivos, pastas ou estrutura macro

## Processo de Aplicação

### Para Novos Documentos
1. Escreva usando formas impessoais desde o início
2. Evite pronomes pessoais diretos
3. Use linguagem neutra e profissional

### Para Documentos Existentes
1. Busque padrões: `(você pode|você precisa|nosso sistema|nossa plataforma|vejamos)`
2. Substitua conforme os padrões estabelecidos
3. Revise contexto para garantir clareza
4. Mantenha exemplos e screenshots inalterados

## Ferramentas de Busca (Regex)

Para encontrar padrões que precisam revisão:
```regex
(você pode|você precisa|você deve|você irá|vamos|vejamos|nossa plataforma|nosso sistema|nosso exemplo|para você|quando você for)
```

**Nota:** Estas diretrizes visam manter consistência, profissionalismo e clareza em toda a documentação, facilitando a compreensão pelos usuários e mantendo um padrão corporativo adequado.
