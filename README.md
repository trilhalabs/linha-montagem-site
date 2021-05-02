# Grupo de Estudos

## 🚀 Projeto Linha Montagem Site

- Objetivo
  > Projeto de estudo de conceitos de pipeline CI/CD através do Gitlab. Disponibilização de um site estático numa esteira contendo build, testes, deploy em ambientes diferentes.

- Ferramentas e softwares utilizadas
  1. Gatsby: site estático
  2. Surge.sh: DNS (Serveless)
  3. AWS - S3
  4. Gitlab: repositorio e Pipeline CI/CD
  5. Github: repositorio "refletido" (mirroring)

  --------------------------------------------------------

  <link><https://github.com/trilhalabs/linha-montagem-site.git>
  
  --------------------------------------------------------

## Montando ambiente local

### Pre reqs

  1. Git Bash
  2. Python
  3. SSH (Github e Gitlab)
  4. Pip
  5. AWS Cli

### Rodando Ambiente Local

- **node**
- **npm**
  <link><https://nodesource.com/blog/installing-nodejs-tutorial-windows/>
  ou
  <link><https://docs.microsoft.com/pt-br/windows/nodejs/setup-on-windows>
  ou
  <link><https://www.gatsbyjs.com/docs/tutorial/part-zero/>  
- **Gatsby** <link><https://www.gatsbyjs.com/docs/quick-start/>

    ```shell
    $ npm install -g gatsby-cli
    $ npm init gatsby
    $ cd "diretorio-criado"
    $ npm install
    $ gatsby build
    $ npm run develop
    >> No browser: <http://localhost:8000>
    ```

- **Surge.sh**
  Irá simular nosso ambiente de Pre Producao

    ```shell
    $ cd public
    $ npm install --global surge
    $ surge
      >> Preecnher informacoes solicitadas
      >> Se ainda não possuir, indique um e-mail e senha
      >> Sera gerado um dominio ".surge.sh"
      #Obs: No caso de erro, execute novamente. Provavelmente o dominio ja existe. Ou crie um nome proprio .surge.sh
      >> No browser: <http://seu-dominio.surge.sh>

      #Obs2: O surge sera usado como ambiente de pre producao na pipeline

    Gere o token a ser configurado no Gitlab
    $ surge token
    ```

- **Plusgin Gatsby AWS S3**
  - Configurar AWS Cli

    ```shell
    $ aws configure --profile nome-da-sua-profile
    >> Obs: --profile é opcional
    ```

  - Alterar "gatsby-config.js

    plugins [
      {
        resolve: "gatsby-plugin-s3",
        options: {
          bucketName: "nome-do-seu-bucket-unico",
        },
      },
    ]

  - Alterar "package.json"

    "scripts": {
      ...
      "deploy": "gatsby-plugin-s3 deploy --yes",
      ...
    }
  
  - Instalar plugin localmente

    ```shell
    $npm install  gatsby-plugin-s3
    $npm run build

    $ AWS_PROFILE=gatsby-useast1 npm run deploy
    ```

  - Git local

    ```shell
    $git remote -v
    $git remote add gitlab git@gitlab.com:seu-usuario/seu-projeto.git 
    $git remote -v
    ```

### Configuracoes Gitlab

- Merge Request

  - Configurar o Gitlab para receber push de branches diferente de master (Exemplo. branch "develop")

  1. Menu Settings / General
    => Expanda a opção "Merge requests"
    => Marque "Fast-forward merge"
    => Expanda a opção "Protected branches"
    => Em "master (default)", selecione em "Allowed to push: No one" (desmarque outras opções)

- Variaveis de ambiente

  - Variaveis necessarias para acessar recursos externos

  1. Menu Settings > CI/CD
    => SURGE_LOGIN
    => SURGE_TOKEN
    => AWS_ACCESS_KEY_ID
    => AWS_SECRET_ACCESS_KEY
    => AWS_DEFAULT_REGION

### AWS

- Criar Bucket S3

  1. liberar como publico
  2. disponibilizar como site estatico

### Rodar a Pipeline no Gitlab

- No terminal local

    ```shell
    $cd /diretorio-projeto
    $git checkout -b develop
    $git merge master
    $git status
    $git git add .
    $git commit -m "descreva seu commit"
    $git push gitlab develop
    ```

- No Gitlab

  - Inicia execucao dos stages "Build" e "Test". Apos sucesso, entre no menu Repository / Branches
  1. Na branch develop, clique no botao "Create Merge Request"
    => **Execute os passos para o Merge Request**
    => **Inicia execucao do stage "deploy-preprod"**
  
  2. Apos sucesso, inicia esteira final
    => **Processo entrara em Blocked no stage "deploy-production"**
    => **Aprove manualmente**
    => Deverao ser executados 4 stages

- Alinhar commits

  ```shell
  $git log --oneline
  $git checkout master
  $git merge develop
  $git pull gitlab master
  $git log --oneline

  ```

## BOM TRABALHO
