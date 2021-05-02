# Grupo de Estudos

## 🚀 Projeto Linha Montagem Site

- Objetivo
Projeto de estudo de conceitos de pipeline CIQCD através do Gitlab.
Disponibilização de um site estático numa esteira contento build, testes, deploy em ambientes diferentes.

- Ferramentas e softwares utilizadas
  > Gatsby: site estático
  > Surge.sh: DNS (Serveless)
  > AWS - S3
  > Gitlab: repositorio e Pipeline CI/CD
  > Github: repositorio "refletido" (mirroring)

  --------------------------------------------------------

  <https://github.com/trilhalabs/linha-montagem-site.git>
  
  --------------------------------------------------------

## Montando ambiente local

- Pre reqs

  > Git Bash
  > Python
  > SSH (Github e Gitlab)
  > Pip
  > AWS Cli

- Rodando Ambiente Local

  > node
  > npm
    <https://nodesource.com/blog/installing-nodejs-tutorial-windows/>
    ou
    <https://docs.microsoft.com/pt-br/windows/nodejs/setup-on-windows>
    ou
    <https://www.gatsbyjs.com/docs/tutorial/part-zero/>

  > Gatsby <https://www.gatsbyjs.com/docs/quick-start/>

    ```shell
    $ npm install -g gatsby-cli
    $ npm init gatsby
    $ cd "diretorio-criado"
    $ npm install
    $ gatsby build
    $ npm run develop
    >> No browser: <http://localhost:8000>
    ```

  > Surge.sh
  Irá simular nosso ambiente de Pre Producao

    ```shell
    $ cd public
    $ npm install --global surge
    $ surge
      >> Preecnher informacoes solicitadas
      >> Se ainda não possuir, indique um e-mail e senha
      >> Sera gerado um dominio ".surge.sh"
      >> Obs: No caso de erro, execute novamente. Provavelmente o dominio ja existe. Ou crie um nome proprio .surge.sh
      >> No browser: <http://seu-dominio.surge.sh>

      >> Obs2: O surge sera usado como ambiente de pre producao na pipeline

    Gere o token a ser configurado no Gitlab
    $ surge token
    ```

  > Plusgin Gatsby AWS S3
    >> Configurar AWS Cli

    ```shell
    $ aws configure --profile nome-da-sua-profile
    >> Obs: --profile é opcional
    ```

    >> Alterar "gatsby-config.js"`

    plugins [
      {
        resolve: "gatsby-plugin-s3",
        options: {
          bucketName: "nome-do-seu-bucket-unico",
        },
      },
    ]

    >> Alterar "package.json"

    "scripts": {
      ...
      "deploy": "gatsby-plugin-s3 deploy --yes",
      ...
    }
  
  > Instalar plugin localmente

    ```shell
    $npm install  gatsby-plugin-s3
    $npm run build

    $ AWS_PROFILE=gatsby-useast1 npm run deploy
    ```

  > Git local

    ```shell
    $git remote -v
    $git remote add gitlab git@gitlab.com:seu-usuario/seu-projeto.git 
    $git remote -v
    ```

## Configuracoes Gitlab

- Merge Request
Configurar o Gitlab para receber push de branches diferente de master (Exemplo. branch "develop")

  > Menu Settings / General
    >> Expanda a opção "Merge requests"
      >>> Marque "Fast-forward merge"
    >> Expanda a opção "Protected branches"
      >>> Em "master (default)", selecione em "Allowed to push: No one" (desmarque outras opções)

- Variaveis de ambiente
Variaveis necessarias para acessar recursos externos

  > Menu Settings > CI/CD
    >> SURGE_LOGIN
    >> SURGE_TOKEN
    >> AWS_ACCESS_KEY_ID
    >> AWS_SECRET_ACCESS_KEY
    >> AWS_DEFAULT_REGION

## AWS

- Criar Bucket S3

  > liberar como publico
  > disponibilizar como site estatico

## Rodar a Pipeline no Gitlab

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
  
  > Inicia execucao dos stages "Build" e "Test"
  > Apos sucesso, entre no menu Repository / Branches
    >> Na branch develop, clique no botao "Create Merge Request"
    >> Execute os passos para o Merge Request
      >>> Inicia execucao do stage "deploy-preprod"
  > Apos sucesso, inicia esteira final
    >> Processo entrara em Blocked no stage "deploy-production"
    >> Aprove manualmente
  > Deverao ser executados 4 stages

- Alinhar commits

  ```shell
  $git log --oneline
  $git checkout master
  $git merge develop
  $git pull gitlab master
  $git log --oneline

  ```

## BOM TRABALHO
