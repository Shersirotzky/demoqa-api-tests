<div align="center">

# 📚 Book Store API

### Desafio Técnico · Accenture

Um fluxo completo de testes: do cadastro do usuário<br>à validação dos livros escolhidos.

![Postman](https://img.shields.io/badge/Postman-FF6C37?style=flat-square&logo=postman&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-222222?style=flat-square&logo=javascript&logoColor=F7DF1E)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![Newman](https://img.shields.io/badge/Newman-222222?style=flat-square&logo=postman&logoColor=FF6C37)

**Postman · Newman · JSON Schema · HTML Reports**

[Começar](#-comece-aqui) · [Fluxo](#-o-que-é-testado) · [Executar](#-execute-os-testes) · [Relatório](#-consulte-o-relatório)

</div>

---

## ✨ O projeto

Automação de testes da API Book Store do DemoQA. A coleção cria um usuário, autentica o acesso, sorteia dois livros diferentes e verifica os livros associados ao perfil.

**O que as validações cobrem**

- **Status HTTP:** o código esperado em cada operação.
- **Contrato JSON:** campos obrigatórios, tipos e formatos.
- **Dados:** usuário correto e os mesmos ISBNs selecionados.
- **Autenticação:** token preenchido, resposta de sucesso e expiração futura.

Os nomes dos testes seguem **Dado → Quando → Então** para facilitar a leitura. A execução usa `pm.test()`; Cucumber não é necessário.

## 🧭 O que é testado

**01 · Criar**  
Cadastra um usuário e salva as credenciais e o identificador.

**02 · Autenticar**  
Gera e salva o token de acesso do usuário criado.

**03 · Autorizar**  
Confirma que a resposta de autorização é `true`.

**04 · Consultar**  
Valida os dados do perfil e sua identificação.

**05 · Escolher**  
Consulta o catálogo e sorteia dois ISBNs diferentes.

**06 · Adicionar**  
Associa os dois livros à coleção do usuário.

**07 · Conferir**  
Consulta o perfil e valida os detalhes dos livros escolhidos.

> “Alugar livros” é o nome usado no desafio para a associação de livros ao usuário. Essa operação não inclui prazo ou pagamento.

## ⚡ Comece aqui

**Você vai precisar de:** Node.js, npm, a coleção exportada no formato Postman v2.1 e o ambiente exportado em JSON.

No ambiente, configure:

```text
baseUrl = https://demoqa.com
```

O Before request da criação deve preparar o nome de usuário e a senha. Os scripts seguintes salvam o ID, o token e os ISBNs durante a execução.

### 1. Organize os arquivos

Na pasta do projeto, mantenha os dois JSONs exportados e uma subpasta chamada `demoqa-newman`.

- **Coleção:** `Accenture DemoQA - Desafio Tecnico.postman_collection.json`
- **Ambiente:** `DemoQA - Desafio Accenture.postman_environment.json`
- **Execução:** pasta `demoqa-newman`

### 2. Prepare a instalação

No terminal, dentro de `demoqa-newman`, execute este comando se ainda não existir um `package.json`:

```bash
npm init -y
```

Instale as dependências localmente:

```bash
npm install --save-dev newman newman-reporter-htmlextra
```

<details>
<summary><strong>Já clonou um projeto com as dependências configuradas?</strong></summary>

Se `package.json` e `package-lock.json` já registram Newman e o reporter, instale as versões registradas com:

```bash
npm ci
```

</details>

## 🚀 Execute os testes

Os comandos abaixo devem ser executados **dentro de `demoqa-newman`**. Os arquivos JSON ficam na pasta imediatamente acima.

Defina os caminhos uma vez para manter o comando de execução curto:

```bash
COLECAO="../Accenture DemoQA - Desafio Tecnico.postman_collection.json"
AMBIENTE="../DemoQA - Desafio Accenture.postman_environment.json"
```

Execute a coleção e gere o relatório:

```bash
mkdir -p reports

npx newman run "$COLECAO" \
  -e "$AMBIENTE" \
  -r cli,htmlextra \
  --reporter-htmlextra-export reports/resultado.html
```

Os caminhos definidos acima valem para a sessão atual do terminal. Ao abrir outro terminal, defina-os novamente antes de executar.

## 📊 Consulte o relatório

**No terminal:** acompanhe os resultados por requisição e o resumo das asserções.

**No navegador:** abra o relatório HTML gerado em:

```text
reports/resultado.html
```

No macOS:

```bash
open reports/resultado.html
```

O resultado corresponde à execução mais recente que gravou nesse arquivo. Testes reprovados também aparecem no relatório; a geração do HTML, por si só, não indica aprovação.

## 🧩 Como os scripts funcionam

**Before request**  
Prepara os dados e armazena o contrato esperado em uma variável local.

**After response**  
Recupera o contrato, verifica a resposta e salva os dados necessários para a próxima etapa.

<details>
<summary><strong>Variáveis compartilhadas pelo fluxo</strong></summary>

- `baseUrl`: endereço da API.
- `randomUserName`: nome gerado para o usuário.
- `userName`: nome salvo pelos scripts que utilizam essa variável.
- `password`: senha usada na criação e na autenticação.
- `userID`: identificador retornado na criação.
- `token`: token retornado pela autenticação.
- `isbn1` e `isbn2`: ISBNs dos dois livros escolhidos.

`userID` identifica o usuário; `token` autentica as requisições. Nas operações protegidas, use **Bearer Token** com `{{token}}`.

</details>

<details>
<summary><strong>Cuidados para repetir a execução</strong></summary>

- Execute as requisições na ordem da coleção.
- Crie um novo usuário a cada execução.
- Gere o token com as credenciais desse mesmo usuário.
- Faça o sorteio antes de adicionar os livros.
- Não sorteie novos ISBNs antes da consulta final.
- Salve e exporte novamente a coleção após alterações no Postman.
- Alterações de variáveis durante o Newman não são gravadas automaticamente no JSON original do ambiente.

</details>

<details>
<summary><strong>Algo deu errado?</strong></summary>

**Arquivo não encontrado (`ENOENT`)**  
Confira a pasta atual, os caminhos e os nomes dos JSONs, incluindo espaços.

**Criação retorna 400**  
Leia o corpo da resposta e confira se o nome e a senha estão preenchidos no envio.

**Requisição protegida retorna 401**  
Confira o token e se ele corresponde ao usuário indicado por `userID`.

**Resposta HTML em vez de JSON**  
Confira a URL. A consulta do perfil usa `/Account/v1/User/{{userID}}`, sem `?` antes do ID.

**Testes passam no Postman e falham no Newman**  
Compare os arquivos exportados e as variáveis. Comece pela primeira falha da execução, pois as etapas seguintes dependem dela.

</details>

---

<div align="center">

### Documentação

[Swagger DemoQA](https://demoqa.com/swagger/)

[Newman](https://github.com/postmanlabs/newman) · [HTML Extra Reporter](https://github.com/DannyDainton/newman-reporter-htmlextra)

**API testing com um fluxo reproduzível e resultados inspecionáveis.**

</div>
