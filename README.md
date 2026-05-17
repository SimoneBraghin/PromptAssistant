# PromptAssistant / Assistente de Prompts

Um assistente interativo e dinâmico especializado em criar prompts otimizados para o Claude. Oferece um painel de configuração guiada e um chat livre para tirar dúvidas sobre engenharia de prompts.

https://simonebraghin.github.io/PromptAssistant/
---

## O que ele faz

- Gera prompts completos e prontos para uso com base no objetivo, técnicas e tom escolhidos
- Responde perguntas sobre como criar bons prompts
- Exibe o prompt gerado em um bloco destacado com botão de copiar
- Mantém o histórico da conversa durante a sessão

---

## Como funciona

O arquivo `prompt-assistant.html` é uma aplicação de página única (SPA). Toda a lógica roda no navegador: quando o usuário envia uma mensagem, o browser faz uma chamada direta à API do Claude (Anthropic) e exibe a resposta no chat.

Isso significa que:

- **Não precisa de servidor próprio** para hospedar
- **É dinâmico**: gera conteúdo novo a cada interação
- **Requer uma chave de API** da Anthropic para funcionar

---

## ⚠️ Segurança da chave de API

> **Atenção:** A chave de API (`ANTHROPIC_API_KEY`) precisa ser inserida diretamente no código HTML. Isso a expõe a qualquer pessoa que inspecionar o código-fonte da página.

| Cenário | Risco |
|---|---|
| Uso pessoal / local | ✅ Baixo — só você tem acesso |
| Publicado para acesso público | ❌ Alto — qualquer pessoa pode copiar a chave e usar na sua conta |

**Recomendação:** use publicamente apenas se a página tiver acesso restrito (ex: protegida por senha) ou se você criar um backend intermediário (ex: Supabase Edge Function, Vercel Function) que esconde a chave do cliente.

---

## Pré-requisito: inserir a chave de API

Antes de qualquer forma de publicação, você precisa inserir sua chave no arquivo. Abra o `prompt-assistant.html` em um editor de texto e localize:

```javascript
headers: { 'Content-Type': 'application/json' },
```

Adicione a linha da chave logo abaixo:

```javascript
headers: {
  'Content-Type': 'application/json',
  'x-api-key': 'SUA_CHAVE_AQUI',
  'anthropic-version': '2023-06-01'
},
```

Substitua `SUA_CHAVE_AQUI` pela sua chave da Anthropic, obtida em [console.anthropic.com](https://console.anthropic.com).

> **Nota:** O plano pago do Claude.ai (claude.ai/chat) é separado da API. O uso via API é cobrado por tokens consumidos, independentemente do plano do claude.ai.

---

## Opção 1 — Uso local (mais simples)

**Complexidade:** ⭐ Mínima  
**Custo:** apenas o consumo de API  
**Segurança:** ✅ Segura — a chave fica só na sua máquina

### Passo a passo

1. Faça o download do arquivo `prompt-assistant.html`
2. Insira sua chave de API conforme descrito acima
3. Abra o arquivo diretamente no navegador (duplo clique ou arraste para o Chrome/Firefox)
4. Pronto — o assistente já funciona

> Não é necessário instalar nada, nem ter servidor rodando.

---

## Opção 2 — Netlify Drop

**Complexidade:** ⭐ Mínima  
**Custo:** Grátis (hospedagem) + consumo de API  
**Segurança:** ⚠️ A chave fica exposta no código-fonte — use apenas para acesso pessoal

### Passo a passo

1. Insira sua chave de API no arquivo (veja seção acima)
2. Acesse [app.netlify.com/drop](https://app.netlify.com/drop)
3. Arraste o arquivo `prompt-assistant.html` para a área indicada
4. O Netlify gera uma URL pública automaticamente (ex: `https://amazing-name-123.netlify.app`)
5. Acesse a URL — o assistente já está no ar

> Para atualizar, basta arrastar o arquivo novamente na mesma área do projeto no Netlify.

---

## Opção 3 — GitHub Pages

**Complexidade:** ⭐⭐ Baixa  
**Custo:** Grátis (hospedagem) + consumo de API  
**Segurança:** ⚠️ A chave fica exposta — repositório deve ser privado

### Passo a passo

1. Crie uma conta em [github.com](https://github.com) se ainda não tiver
2. Crie um novo repositório **privado** (importante para não expor a chave)
3. Renomeie o arquivo para `index.html` (o GitHub Pages serve esse nome por padrão)
4. Insira sua chave de API no arquivo
5. Faça o upload do `index.html` para o repositório
6. No repositório, vá em **Settings → Pages**
7. Em "Source", selecione a branch `main` e a pasta `/ (root)`
8. Clique em **Save**
9. Aguarde cerca de 1 minuto e acesse a URL gerada (ex: `https://seuusuario.github.io/nome-do-repo`)

> **Atenção:** Mesmo em repositório privado, a URL do GitHub Pages é pública. Qualquer pessoa com o link pode acessar — e ver o código-fonte com a chave. Use apenas para acesso pessoal.

---

## Opção 4 — Lovable (com backend seguro)

**Complexidade:** ⭐⭐⭐ Média  
**Custo:** Plano pago do Lovable + consumo de API  
**Segurança:** ✅ A chave fica protegida no servidor

O Lovable permite criar um backend via **Supabase Edge Function**, que atua como intermediário entre o frontend e a API do Claude. Assim, a chave nunca fica exposta no navegador.

### Arquitetura

```
Usuário → Lovable (React) → Supabase Edge Function → API Claude
```

### Passo a passo resumido

1. Crie conta em [lovable.dev](https://lovable.dev) e em [supabase.com](https://supabase.com)
2. No Lovable, crie um novo projeto e conecte ao Supabase
3. No Supabase, crie uma Edge Function chamada `claude-proxy` que recebe as mensagens e repassa à API do Claude com a chave armazenada como variável de ambiente (secret)
4. No Lovable, substitua as chamadas diretas à API do Claude pela URL da Edge Function
5. Publique — a chave nunca aparece no frontend

> Esta opção é recomendada para aplicações compartilhadas com outros usuários.

---

## Comparativo de opções

| Opção | Complexidade | Custo | Chave exposta | Recomendado para |
|---|---|---|---|---|
| Local | ⭐ | API only | ✅ Não | Uso pessoal |
| Netlify Drop | ⭐ | API only | ⚠️ Sim | Teste rápido |
| GitHub Pages | ⭐⭐ | API only | ⚠️ Sim | Uso pessoal com URL fixa |
| Lovable + Supabase | ⭐⭐⭐ | Lovable + API | ✅ Não | Compartilhar com outros |
