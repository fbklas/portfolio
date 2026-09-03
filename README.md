# Portfólio

Site estático (HTML + CSS puro) para apresentar cases de Business Intelligence / Market Intelligence.
Sem framework, sem build, sem dependências. Hospedado no GitHub Pages.

---

## 1. Estrutura

```
site/
├── index.html                          Home (nome, apresentação, links, lista de projetos)
├── projetos.html                       Listagem de projetos
├── projetos/
│   ├── case-01-ensino-superior.html    Case 01
│   ├── case-02-market-intelligence.html Case 02
│   └── _template.html                  Modelo para novos cases (não é linkado no site)
├── assets/
│   ├── style.css                       Único arquivo de estilo
│   └── img/
│       ├── case-01/                    Screenshots do case 01
│       └── case-02/                    Screenshots do case 02
├── .nojekyll                           Diz ao GitHub Pages para publicar os arquivos como estão
└── README.md
```

---

## 2. Rodar localmente

**Não há nada para instalar.**

Opção A (mais simples): dê duplo clique em `index.html`. Abre no navegador e todos os links funcionam.

Opção B (recomendada se for testar o iframe do Power BI, que às vezes não carrega em `file://`):

```bash
# Windows, dentro da pasta do site (Python já vem instalado na maioria das máquinas)
python -m http.server 8000
```

Depois abra `http://localhost:8000` no navegador. `Ctrl+C` no terminal encerra.

---

## 3. Editar conteúdo

Tudo é editado em qualquer editor de texto (VS Code, Notepad++, até o Bloco de Notas).
Os pontos que você vai editar estão marcados com `<!-- EDITAR -->` nos arquivos.

| O que mudar | Onde |
|---|---|
| Apresentação curta | `index.html`, parágrafo `class="lead"` |
| Link do LinkedIn | Já configurado como `linkedin.com/in/francisco-klas` (botão da home e rodapé de cada página) |
| Currículo | Por decisão, não é publicado no site. Contato via LinkedIn. |
| Cores, fonte, largura | Variáveis no topo de `assets/style.css` (`:root { ... }`) |
| Menu / rodapé | Estão repetidos em cada `.html` (são poucas linhas). Use "Localizar e substituir em todos os arquivos" do editor. |

Dica: no VS Code, `Ctrl+Shift+H` faz localizar/substituir em todos os arquivos da pasta.

---

## 4. Adicionar um projeto

1. Copie `projetos/_template.html` e renomeie, por exemplo `projetos/case-03-nome-curto.html`
   (só letras minúsculas, números e hífens; sem acentos ou espaços).
2. Abra o arquivo novo e preencha as seções. Apague as seções que não fizerem sentido.
3. Crie a pasta de imagens `assets/img/case-03/`.
4. Adicione um card em **`projetos.html`** e em **`index.html`** copiando um bloco `<li> ... </li>` existente:

```html
<li>
  <span class="tag">Case 03</span>
  <h3><a href="projetos/case-03-nome-curto.html">Título do case</a></h3>
  <p>Resumo em uma ou duas frases.</p>
  <p class="meta">Power BI · Excel · Fonte dos dados</p>
</li>
```

5. Ajuste os links "← Case anterior / Próximo case →" no final das páginas de case, se quiser.

Remover o selo "Em construção": apague o trecho `<span class="status">Em construção</span>`.

---

## 5. Adicionar screenshots

1. Tire o print do Power BI em **tela cheia** (no Power BI Service: *View → Full screen*, depois `Win+Shift+S`).
   Resolução recomendada: 1600–1920 px de largura. Salve em PNG.
2. Se o arquivo passar de ~1 MB, comprima (por exemplo em https://squoosh.app ou https://tinypng.com).
   Isso deixa o site rápido no celular.
3. Salve em `assets/img/case-NN/` com nome descritivo: `01-visao-geral.png`, `02-share-regional.png`.
4. Na página do case, troque o bloco `placeholder-img` por:

```html
<figure>
  <img src="../assets/img/case-01/01-visao-geral.png" alt="Descrição da imagem">
  <figcaption>Legenda curta explicando o que o print mostra.</figcaption>
</figure>
```

Sempre preencha o `alt` (acessibilidade) e uma legenda: o recrutador entende o print sem abrir o relatório.

---

## 6. Incorporar Power BI

### 6.1 O que é possível (resumo)

| Opção | Recrutador precisa de licença? | Custo | Serve para o portfólio? |
|---|---|---|---|
| **Publish to web** | Não | Grátis (My Workspace, licença Fabric Free) | **Sim** — é o único caminho viável |
| Secure embed (*Embed report → Website or portal*) | Sim (Pro/PPU no seu tenant) | — | Não |
| Power BI Embedded (app owns data) | Não | Capacidade Azure paga + backend | Não |

Regras do **Publish to web** que importam:

- Tudo fica **público na internet, sem login**, incluindo os dados de detalhe por trás dos gráficos.
  Por isso: **só dados públicos ou sintéticos**, sempre.
- Precisa que o **admin do tenant** tenha habilitado *Publish to web* (Admin portal → Tenant settings →
  Export and sharing settings). Em tenant de empresa/universidade costuma estar desligado.
  Publique de um tenant que você controla.
- Não funciona com RLS, DirectQuery, Live Connection, visuais R/Python, relatórios paginados.
- Cache de 1 h; 1 embed code por relatório; pode ser bloqueado temporariamente sob uso muito intenso.
- Para revogar: Power BI Service → engrenagem ⚙ → *Manage embed codes* → *Delete*.

### 6.2 Passo a passo

1. Publique o `.pbix` na sua **My Workspace** no Power BI Service (app.powerbi.com).
2. Abra o relatório → **File → Embed report → Publish to web (public)** → *Create embed code*.
3. Copie o **link** (`https://app.powerbi.com/view?r=...`) e o **código do iframe**.
4. Na página do case, seção *Relatório interativo*:
   - cole o link no `href` do botão "Abrir relatório no Power BI";
   - descomente o bloco `<div class="powerbi">` (remova `<!--` e `-->`) e cole o link no `src` do iframe.

```html
<p class="links">
  <a class="btn" href="https://app.powerbi.com/view?r=SEU_CODIGO" target="_blank" rel="noopener">Abrir relatório no Power BI</a>
</p>
<div class="powerbi">
  <div class="frame">
    <iframe title="Case 01" src="https://app.powerbi.com/view?r=SEU_CODIGO" allowfullscreen="true"></iframe>
  </div>
  <p class="note">Relatório interativo. Em telas pequenas, recomenda-se abrir em tela cheia ou no desktop.</p>
</div>
```

O CSS já deixa o iframe responsivo (proporção 16:9 + barra do Power BI). Não precisa mexer em `width`/`height`.

### 6.3 Se o embed não for viável

O site já funciona sem ele. Deixe apenas:

- screenshots com legenda (seção 5);
- opcionalmente, o relatório exportado em PDF (*File → Export → PDF* no Power BI) salvo em `assets/` e linkado pelo botão "Baixar relatório em PDF";
- opcionalmente, o `.pbix` para download (só se os dados forem públicos/sintéticos — o arquivo contém os dados).

---

## 7. Criar o repositório e publicar (GitHub Pages)

Pré-requisito: conta no GitHub e o [Git](https://git-scm.com/download/win) instalado (ou o [GitHub Desktop](https://desktop.github.com/), que faz tudo por interface).

### Pela linha de comando

```bash
# 1. dentro da pasta do site
cd "C:\caminho\para\site"
git init
git add .
git commit -m "Site inicial"

# 2. crie um repositório vazio em https://github.com/new
#    nome sugerido: portfolio   (público, sem README, sem .gitignore)

# 3. conecte e envie
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/portfolio.git
git push -u origin main
```

### Ativar o GitHub Pages

1. No GitHub, abra o repositório → **Settings → Pages**.
2. Em *Build and deployment* → *Source*: **Deploy from a branch**.
3. *Branch*: `main`, pasta `/ (root)` → **Save**.
4. Em 1–2 minutos o site fica no ar em **`https://SEU-USUARIO.github.io/portfolio/`**.
   O endereço aparece no topo da mesma tela de Pages.

Quer o site na raiz, sem `/portfolio/`? Nomeie o repositório exatamente `SEU-USUARIO.github.io`.
O endereço vira `https://SEU-USUARIO.github.io/`.

### Pelo GitHub Desktop (sem terminal)

*File → Add local repository* → escolha a pasta → *Publish repository* (desmarque "Keep this code private") → depois siga "Ativar o GitHub Pages" acima.

---

## 8. Atualizar o site depois

Edite os arquivos, depois:

```bash
git add .
git commit -m "Adiciona screenshots do case 01"
git push
```

No GitHub Desktop: escreva a mensagem no campo *Summary* → **Commit to main** → **Push origin**.

O GitHub Pages republica sozinho em 1–2 minutos. Se não vir a mudança, recarregue com `Ctrl+F5`
(cache do navegador).

---

## 9. Domínio próprio (opcional, depois)

Se comprar um domínio (ex.: `franciscoklas.com.br`): Settings → Pages → *Custom domain*, e no
registrador aponte um `CNAME` de `www` para `SEU-USUARIO.github.io`. O GitHub emite o HTTPS
automaticamente. Não é necessário para enviar o link a recrutadores.

---

## 10. Checklist antes de enviar o link a um recrutador

- [x] Link do LinkedIn (linkedin.com/in/francisco-klas) já configurado em todas as páginas
- [ ] Nenhum "A definir" / "Em construção" nos cases que você quer que sejam lidos
- [ ] Screenshots com legenda e `alt`
- [ ] Link do Power BI abre em janela anônima (sem login)
- [ ] Site testado no celular (`https://SEU-USUARIO.github.io/portfolio/`)
