# Wavoip + Digisac - Guia de Setup

## O que e isso?
Pagina web que embute o Wavoip Webphone dentro do Digisac via iframe.
O vendedor abre a conversa, clica no icone, e liga direto sem sair do Digisac.

A pagina recebe o `contactId` do Digisac, busca o telefone via API, e ja abre pronta pra ligar.

---

## Passo 1: Hospedagem (ja feito)

A pagina esta hospedada no GitHub Pages:
- **URL:** `https://danielpetersbr.github.io/branorte-voip/`
- **Repo:** `https://github.com/danielpetersbr/branorte-voip`

### Alternativas de hospedagem

| Plataforma | Custo | Como |
|------------|-------|------|
| GitHub Pages | Gratis | Repo publico + Pages ativado |
| Vercel | Gratis | Upload ou connect repo |
| Netlify | Gratis | Drag and drop |

---

## Passo 2: Integracao no Digisac (ja feito)

A integracao ja esta configurada no Digisac da Branorte.

### Como funciona

O Digisac cria um iframe na barra lateral com a URL:
```
https://danielpetersbr.github.io/branorte-voip/?contactId={{contactId}}&ticketId={{ticketId}}
```

O Digisac substitui `{{contactId}}` pelo UUID do contato aberto. A pagina entao:
1. Recebe o `contactId` via URL
2. Chama a API do Digisac (`/api/v1/contacts/{contactId}`)
3. Extrai nome e telefone do contato
4. Mostra na tela pronto pra ligar

### Configuracao da integracao (referencia)

| Campo | Valor |
|-------|-------|
| **Tipo** | Contato - barra lateral |
| **Texto** | Ligar (Wavoip) |
| **Icone** | Phone |
| **URL** | `https://danielpetersbr.github.io/branorte-voip/?contactId={{contactId}}&ticketId={{ticketId}}` |
| **API type** | `context_menu_button_contact_side_list` |
| **Integration ID** | `3d884f22-2019-4370-a15c-1c86f0164e63` |

### Variaveis disponiveis em integracoes de barra lateral

> **IMPORTANTE:** Integracoes de barra lateral do Digisac so passam estas variaveis:

| Variavel | O que retorna |
|----------|---------------|
| `{{contactId}}` | UUID do contato |
| `{{ticketId}}` | UUID do ticket/conversa |
| `{{userId}}` | UUID do usuario logado |
| `{{accountId}}` | UUID da conta |

> **NOTA:** `{{contact_number}}`, `{{contact_name}}` e similares NAO funcionam em integracoes
> de barra lateral. Essas variaveis so existem no contexto de templates de mensagem.
> Descoberto via analise do codigo-fonte JavaScript do Digisac (funcao eP7).

---

## Passo 3: Configuracao inicial (primeira vez por maquina)

Na primeira vez que o vendedor abrir a integracao:

1. Clique em **"Configuracoes"** (na parte de baixo)
2. Preencha os 3 campos:

| Campo | Onde encontrar | Valor Branorte |
|-------|----------------|----------------|
| **Token Wavoip** | Painel Wavoip > Dispositivo > Token | (copiar do painel) |
| **Token Digisac** | Admin Digisac > API | `3acd073d86835b7a13a4aeff1577dd84327c5d00` |
| **Dominio Digisac** | URL do Digisac | `mbranorte2.digisac.io` |

3. Clique **"Salvar configuracoes"**
4. Os tokens ficam salvos no navegador (nao precisa repetir)

> **Alternativa:** Passe tokens direto na URL para nao precisar configurar em cada maquina:
> ```
> ?contactId={{contactId}}&digisacToken=SEU_TOKEN&digisacDomain=mbranorte2.digisac.io&token=WAVOIP_TOKEN
> ```

---

## Passo 4: Usar

1. Abra uma conversa no Digisac
2. Clique no **icone de tomada/plug** (canto superior direito da conversa)
3. Selecione **"Ligar (Wavoip)"**
4. O webphone aparece na lateral com:
   - Nome do contato (buscado da API)
   - Telefone preenchido automaticamente
   - Botao "Ligar" verde
5. Clique **"Ligar"** e fale normalmente

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
| "Configure os tokens abaixo" | Abra Configuracoes e preencha os 3 tokens |
| "Erro de CORS" | A API do Digisac pode bloquear chamadas cross-origin. Solucao: use proxy ou passe dados via URL |
| "Numero nao encontrado" | O contato no Digisac nao tem telefone no campo `data.number` |
| Nome mostra "Carregando..." | Token Digisac invalido ou dominio errado |
| Pagina nao carrega no iframe | Verifique se a hospedagem permite iframe (GitHub Pages permite) |
| Erro ao conectar Wavoip | Verifique se o dispositivo Wavoip esta online e WhatsApp vinculado |
| Sem audio | Permita microfone no navegador (aparece popup na primeira vez) |
| Telefone formatado errado | A pagina espera formato brasileiro (+55DDDNUMERO) |

---

## Arquitetura tecnica

```
Digisac (iframe)
    |
    | URL: ?contactId=UUID&ticketId=UUID
    v
index.html (GitHub Pages)
    |
    | fetch /api/v1/contacts/{contactId}
    v
Digisac API --> nome + telefone
    |
    v
Wavoip SDK --> liga via SIP/WhatsApp
```

---

## Arquivos

| Arquivo | O que faz |
|---------|-----------|
| `index.html` | Pagina do webphone (tudo em um arquivo) |
| `SETUP.md` | Este guia |
