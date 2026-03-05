# Wavoip + Digisac - Guia de Setup

## O que e isso?
Pagina web que embute o Wavoip Webphone dentro do Digisac via iframe.
O vendedor abre a conversa, clica no icone, e liga direto sem sair do Digisac.

---

## Passo 1: Hospedar a pagina

### Opcao A: Vercel (gratis, recomendado)
1. Acesse https://vercel.com e faca login com GitHub
2. Clique "New Project" > "Upload"
3. Arraste a pasta `wavoip-digisac/` inteira
4. Clique "Deploy"
5. Copie a URL gerada (ex: `https://branorte-voip.vercel.app`)

### Opcao B: Netlify (gratis)
1. Acesse https://app.netlify.com/drop
2. Arraste a pasta `wavoip-digisac/`
3. Copie a URL gerada

### Opcao C: GitHub Pages (gratis)
1. Crie repositorio no GitHub
2. Suba os arquivos desta pasta
3. Ative GitHub Pages nas Settings
4. URL: `https://seuusuario.github.io/nome-repo/`

---

## Passo 2: Configurar no Digisac

### Opcao A: Via interface (recomendado)

1. Acesse o Digisac (mbranorte2.digisac.io)
2. Menu lateral > **Mais opcoes** > **Integracoes**
3. Clique **"+ Adicionar"**
4. Selecione o tipo **"Contato - barra lateral"**
5. Preencha:

| Campo | Valor |
|-------|-------|
| **Texto do Menu** | Ligar (Wavoip) |
| **Icone** | Phone |
| **URL do iframe** | `https://SUA-URL.vercel.app/?phone={{contact_number}}&name={{contact_name}}` |

6. Clique **Salvar**

### Opcao B: Via API

```bash
curl -X POST https://SEU-DOMINIO.digisac.io/api/v1/integrations \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "context_menu_button_contact_side_list",
    "text": "Ligar (Wavoip)",
    "icon": "Phone",
    "url": "https://SUA-URL.vercel.app/?phone={{contact_number}}&name={{contact_name}}"
  }'
```

> **IMPORTANTE:** O campo `type` na API NAO e validado - aceita qualquer string sem erro.
> Use exatamente `context_menu_button_contact_side_list` para "Contato - barra lateral".
> Icones usam PascalCase: `Phone`, `Whatsapp`, `Calendar`, `Chat`, etc.

### Tipos de integracao (valores API)

| Tipo na Interface | Valor na API |
|-------------------|-------------|
| Contato - barra lateral | `context_menu_button_contact_side_list` |

### Variaveis dinamicas do Digisac
O Digisac substitui automaticamente as variaveis pelos dados do contato:

| Variavel | O que retorna |
|----------|---------------|
| `{{contact_number}}` | Telefone do contato |
| `{{contact_name}}` | Nome do contato |
| `{{contact_digisac_name}}` | Nome Digisac do contato |
| `{{contact_phonebook_name}}` | Nome da agenda do contato |
| `{{contact_fallback_name}}` | Nome fallback do contato |
| `{{protocol_number}}` | Numero do protocolo |
| `{{contactId}}` | UUID do contato |

> **NOTA:** Variaveis usam underscores (contact_number), NAO pontos (contact.phone).
> Formato descoberto diretamente do codigo-fonte JavaScript do Digisac.

---

## Passo 3: Configurar o token Wavoip (uma vez)

1. Acesse https://wavoip.com e va na pagina do seu dispositivo
2. Copie o **Token do dispositivo**
3. Na primeira vez que o vendedor abrir a integracao no Digisac:
   - Abra "Configuracoes do dispositivo" (na parte de baixo)
   - Cole o token
   - Clique "Salvar token"
4. O token fica salvo no navegador (nao precisa repetir)

> **Alternativa:** Passe o token direto na URL:
> `https://SUA-URL.vercel.app/?phone={{contact_number}}&name={{contact_name}}&token=SEU_TOKEN`
> Assim nao precisa configurar em cada maquina.

---

## Passo 4: Testar

1. Abra uma conversa no Digisac
2. Clique no **icone de tomada** (canto superior direito)
3. Selecione **"Ligar (Wavoip)"**
4. O webphone deve aparecer na lateral com:
   - Nome do contato
   - Telefone preenchido
   - Botao "Ligar" verde
5. Clique "Ligar" e teste a chamada

---

## Gravacao

As gravacoes sao gerenciadas pela propria Wavoip:
- Ative gravacao no painel da Wavoip (Configuracoes Gerais > Gravacao)
- As gravacoes ficam disponiveis no painel da Wavoip
- Para receber via webhook, configure em Wavoip > Webhook (Beta)

---

## Troubleshooting

| Problema | Solucao |
|----------|---------|
| "Configure o token" | Cole o token do dispositivo Wavoip |
| Pagina nao carrega no iframe | Verifique se a hospedagem permite iframe (Vercel/Netlify permitem) |
| Telefone nao preenche | Verifique a variavel dinamica do Digisac (testar `{{contact.phone}}`) |
| Erro ao conectar | Verifique se o dispositivo Wavoip esta online e WhatsApp vinculado |
| Sem audio | Permita microfone no navegador (aparece popup na primeira vez) |

---

## Arquivos

| Arquivo | O que faz |
|---------|-----------|
| `index.html` | Pagina do webphone (tudo em um arquivo) |
| `SETUP.md` | Este guia |
