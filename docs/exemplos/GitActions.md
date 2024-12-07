## Documentação do Workflow do GitHub Actions: Build GitHub Pages

Este workflow do GitHub Actions automatiza o processo de construção e implantação de um site estático usando o MkDocs para o GitHub Pages.

### **Nome do Workflow:** Build GitHub Pages

### **Eventos de Acionamento:**

* **Push:** O workflow é acionado quando há um push para os branches `master` ou `main` e as alterações afetam arquivos na pasta `docs`.
```yaml
on:
  push:
    branches:
      - master
      - main
    paths:
      - 'docs/**'
```
* **Workflow Dispatch:** O workflow pode ser acionado manualmente pelo usuário.

### **Permissões:**

* **contents: write:** Permite que o workflow grave arquivos no repositório.
* **pages: write:** Permite que o workflow escreva para o GitHub Pages.
* **id-token: write:** Permite que o workflow acesse o token de identificação do GitHub.

```yaml
permissions:
  contents: write
  pages: write
  id-token: write
```

### **Jobs ou Trabalhos:**

#### **build_mkdocs:**

* **Ambiente:** `ubuntu-latest`
* **Etapas:**
    * **Checkout code:** Faz o checkout do código-fonte do repositório.
    * **Setup Python:** Define a versão Python a ser usada como `3.x`.
    * **Install MkDocs:** Instala o `mkdocs-material` para o MkDocs.
    * **Build MkDocs:** Executa o comando `mkdocs gh-deploy --force` para construir o site do MkDocs e implantá-lo no GitHub Pages.

```yaml
jobs:
  build_mkdocs:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

#### **deploy_mkdocs:**

* **Depende de:** `build_mkdocs`
* **Ambiente:** `github-pages`
* **URL:** ${{ steps.deployment.outputs.page_url }}
* **Ambiente:** `ubuntu-latest`
* **Etapas:**
    * **Checkout:** Faz o checkout do código-fonte do repositório.
    * **Setup Pages:** Configura o GitHub Pages.
    * **Upload artifact:** Carrega o artefato (o site MkDocs construído) para o GitHub Pages.
    * **Deploy to GitHub Pages:** Implanta o artefato no GitHub Pages.

```yaml
  deploy_mkdocs:
    needs: build_mkdocs
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          ref: gh-pages
      - name: Setup Pages
        uses: actions/configure-pages@v5
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### **Fluxo de Trabalho:**

1. Quando um push é feito para os branches `master` ou `main` com alterações na pasta `docs`, o workflow é acionado.
2. O trabalho `build_mkdocs` é executado, instalando o MkDocs e construindo o site.
3. O trabalho `deploy_mkdocs` é executado, implantando o site construído no GitHub Pages.
4. A URL do site implantado é exibida como uma saída.

### **Observações:**

* Este workflow usa o `mkdocs-material` como tema para o MkDocs. Você pode modificar o tema ou usar outro tema de acordo com sua preferência.
* O workflow é configurado para implantar no branch `gh-pages`. Você pode modificar o branch de implantação conforme necessário.
* O comando `mkdocs gh-deploy --force` garante que a implantação seja feita mesmo se o site já estiver implantado.

### **Personalização:**

Você pode personalizar este workflow de acordo com suas necessidades específicas, incluindo:

* Adicionar outras etapas, como testes ou análise de código.
* Definir outros eventos de acionamento.
* Configurar outros ambientes de implantação.
* Usar diferentes temas ou plugins para o MkDocs.

### **Benefícios:**

* Automatiza a implantação do site MkDocs no GitHub Pages.
* Garante que o site seja implantado sempre que houver alterações na pasta `docs`.
* Fornece um processo consistente e confiável para a implantação do site.

### **Conclusão:**

Este workflow do GitHub Actions fornece uma solução completa para construir e implantar um site estático usando o MkDocs para o GitHub Pages. Ele é fácil de configurar e personalizar, tornando-o uma opção ideal para desenvolvedores que desejam automatizar o processo de implantação do seu site.


