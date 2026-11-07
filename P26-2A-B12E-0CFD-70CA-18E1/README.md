# Projeto #43: Sorteador Auditável de Equipes e Ordem de Apresentação

- **Modalidade:** Projeto Oficial Sorteado (Valendo 100 pontos)
- **Estudante:** Bruno Kenyu Ochiai
- **Turma:** 2A
- **Chave Única:** `P26-2A-B12E-0CFD-70CA-18E1`
- **URL_CLOUDFLARE:** `https://p26-2a-b12e-0cfd-70ca-18e1.pages.dev` *(Atualize com a URL pública real do seu deploy na Cloudflare antes de abrir o Pull Request)*

---

## Especificação do Projeto

Projete uma ferramenta utilitária para professores e representantes de turma realizarem sorteios de grupos de trabalho ou definirem a ordem de apresentação de seminários. O organizador faz login via Discord, insere a lista de participantes (ou permite que os próprios alunos entrem na sala clicando em um botão com seu login do Discord) e define os parâmetros (ex: tamanho máximo de cada grupo).

Para garantir transparência e impossibilitar acusações de favorecimento, o algoritmo de embaralhamento (Fisher-Yates) é executado dentro da Cloudflare Function. O resultado é gravado de forma imutável no Cloudflare D1 com um código de verificação e carimbo de data/hora, permitindo que qualquer aluno consulte a ata pública daquele sorteio posteriormente.

---

## Como Publicar esta Subpasta no Cloudflare Pages + D1 + Discord OAuth2

1. No painel da Cloudflare (**Workers & Pages** → **Create** → **Pages** → **Connect to Git**), selecione o seu fork do repositório `Avalia2026-2`.
2. Configure os parâmetros de build apontando para esta subpasta:
   - **Root directory (Diretório raiz):** `P26-2A-B12E-0CFD-70CA-18E1`
   - **Build command:** *(deixe em branco — HTML/CSS/JS puro)*
   - **Build output directory:** `public`
3. Crie o banco de dados D1 e aplique a migração inicial (no terminal do laboratório usando Scoop):
   ```powershell
   cd P26-2A-B12E-0CFD-70CA-18E1
   npx.cmd wrangler d1 create db-p26-2a-b12e-0cfd-70ca-18e1
   # Copie o database_id gerado para o arquivo wrangler.jsonc e execute:
   npx.cmd wrangler d1 migrations apply DB --remote
   ```
4. **Vincule o Banco D1 ao seu Cloudflare Pages (Obrigatório para ativar env.DB):**
   - No painel da Cloudflare: acesse seu projeto Pages → **Settings** → **Functions** → **D1 database bindings**.
   - Clique em **Add binding**:
     - **Variable name:** `DB`
     - **D1 database:** selecione o banco que você criou (`db-p26-2a-b12e-0cfd-70ca-18e1`).
5. **Configuração do Discord OAuth2 (Obrigatório para autenticação real):**
   - Acesse o **Discord Developer Portal** (https://discord.com/developers/applications) e crie uma nova Application.
   - Na aba **OAuth2**:
     - Copie o **Client ID** e gere o **Client Secret**.
     - Em **Redirects**, adicione a URL exata do deploy:
       `https://p26-2a-b12e-0cfd-70ca-18e1.pages.dev/api/auth/discord/callback`
   - No painel do seu projeto no Cloudflare Pages:
     - Acesse **Settings** → **Environment variables**.
     - Adicione as variáveis de ambiente:
       - `DISCORD_CLIENT_ID`
       - `DISCORD_CLIENT_SECRET`
6. Atualize o campo **URL_CLOUDFLARE** no topo deste `README.md` com o endereço público gerado pela Cloudflare.
7. Na raiz do repositório, execute `node validar-local.mjs P26-2A-B12E-0CFD-70CA-18E1` para validar todos os critérios antes de fazer o commit e abrir o Pull Request.
