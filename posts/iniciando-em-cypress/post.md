# Iniciando em cypress

### Instalação
'
Node
* Verificar se o npm está instalado corretamente, podendo verificar a versão instalada: npm -v.
* Verificar a versão do node: node -v.
* Instalar as versões pares do Node, as versões LTS. exemplo: 16.13.1.
* Iniciar o projeto: npm init. Será feita algumas perguntas para criação do package.json.
* Na configuração do package.json, informar 'npx cypress  open' no 'test command'
* 'npx cypress  open' é um executor dos pacotes npm

Cypress
```bash
npm i cypress --save-dev 
```

### Como iniciar o Cypress

* Existem duas formas de se executar o cypress, a primeira é utilizando 'npm run test' que vai abrir a ferramenta do cypress, e a segunda é utilizando 'npx cypress run' que vai rodar os testes no terminal.

Mesmo seguindo todas as configurações passo a passo, é comum em algumas máquinas ocorrer na primeira execução do Cypress o erro: “Command timed out after 30000 miliseconds”. Para isso, temos duas alternativas:

1) Executar novamente, com o comando npm run test ou npx cypress open;

2) Aumentar esse tempo de verificação no arquivo verify.js que fica no caminho: node_modules\cypress\lib\tasks
Esse caminho está dentro da pasta node_modules\cypress\tasks e o nome do arquivo é verify.js
Localize a constante VERIFY_TEST_RUNNER_TIMEOUT_MS e altere de 30000 para 100000;
Executar novamente, com o comando npm run test ou npx cypress open

### Pastas

* fixtures: Utilizada para os arquivos de dados fixos, como mocks que serão utilizados nos testes.
* integration: Utilizada para os arquivos de testes.
* plugins: Utilizada para arquivos que eu posso criar/editar para modificar ou estender o comportamento interno no Cypress.
* support: Utilizada para arquivos de importação e comandos personalizados que podemos criar dentro do Cypress.

### Características positivas do Cypress

* Execução rápida dos testes.
* Verifica quais navegadores tenho instalado na máquina, facilitando na execução.
* Possibilidade de acompanhar toda a linha do tempo dos testes, como o estado anterior, o que aconteceu após a ação etc.


### Principais comandos

* describe: é uma função que tem 2 parâmetros, o primério é o nome da suite de testes, o segundo é uma função onde eu posso executar qualquer coisa
* beforeEach: é um comando que é executado antes do inicio de cada cenário de testes.
* it: é um item de testes ou cenário de teste.
* .visit: resposável por abrir o link informado.
* .contains: resposável por identificar elementos pelo texto.
* .get: resposável por identificar elementos pela classe, tag, id etc.
* .type: responsável por realizar a ação de escrita.
* .click: responsável por realizar a ação de click.
* .should: responsável por realizar as validações.
* .only: faz com que seja executado somente o cenário que tenha esse comando.
* .on: usado para eventos, como por exemplo windows.alert.
* .request: responsável por estruturar e validar as requests.
* .expect: responsável por realizar as validações.


### Três As

O teste geralmente é composto por três As, que é o arrange, o act e o assert

* O arrange é a preparação do ambiente
* O act é a ação que eu quero fazer
* O assert é o que eu quero verificar

### Pacotes úteis
mochawesome é um gerador de relatório personalizado. Com ele podemos fazer configurações, verificar se o log será gerado em html, json, onde será gerado, qual o formato de data para ser utilizado no nome, o título, dentre outros parâmetros

```bash
npm i -D mochawesome
```
Após a instalação. deverá configurar o arquivo cypress.json
```bash
{
  "reporter": "mochawesome",
  "reporterOptions": {
    "reportDir": "cypress/report/mochawesome-report",
    "overwrite": true,
    "html": true,
    "json": false,
    "timestamp": "mmddyyyy_HHMMss",
    "charts": "false" ,
    "code": "false",
    "reporterTitle": "Automação com cypress"
  },
  "projectId": "zb9ibc"
}

```

* Não chega a ser um pacote, mas uma funcionalidade do cypress, onde você consegue ter um histórico de testes, relatórios mais detalhados sobre os testes executados. 
Utilizar o comando sempre que for executar os testes informando a key do projeto.
```bash
npx cypress run --record --key 'key'
```

### Criar comandos
Criar comandos facilita na criação dos testes, além de eliminar código repetido. Deverá ser criado na pasta support e importar no arquivo index.js.

```bash
Cypress.Commands.add('login', (nome, senha) => {
    cy.get('input[formcontrolname="userName"]').type(nome);
    cy.get('input[formcontrolname="password"]').type(senha);
    cy.contains('button', 'login').click();
})
```

### Testes de API

Também é possível realizar os testes de API, a sintaxe e as valições são bem tranquilas e parecidas as que temos no mercado.

```bash
   it('buscar fotos do flavio', () => {
        cy.request({
            method: 'GET',
            url: 'https://apialurapic.herokuapp.com/flavio/photos',

        }).then((res) => {
            expect(res.status).to.be.equal(200)
            expect(res.body).is.not.empty
            expect(res.body[0]).to.have.property('description')
            expect(res.body[0].description).to.be.equal('Farol iluminado')
        })
    })
```

### Proteger dados sensíveis 

É possível guardar configurações nas próprias variáveis de ambiente do cypress, como o cypress.json, porém, ele é um arquivo que precisamos enviar para o repositório. Vamos utilizar o cypress.env.json, que também é um arquivo de configuração, mas que não precisamos enviar para nosso repositório, deixando-o no gitignore.


### Boas práticas
Evitar flaky tests: São testes que ocorrem erros de forma aleatória, não necessariamente são falhas. Exemplo: era esperada que a request fosse realizada em 3 segundos, mas demorou 5, não quer dizer que houve uma falha, mas sim uma demora que pode ou não acontecer.

Definir uma baseurl

Utilizar variáveis de ambiente para dados sensíveis
```bash
cy.login(Cypress.env('userName'), Cypress.env('password'))
```

Utilizar {log: false} para dados sensíveis
```bash
type(senha, {log: false});
```
stub: muito útil para 'mockar' um status esperado.
```bash
cy.intercept('POST', `https://apialurapic.herokuapp.com/user/login`, {
            statusCode: 400
        }).as('stubPost')
adicionar wait no cenário(it) para que o teste aguarde a interceptação
cy.wait('@stubPost')
```

mock: muito útil quando queremos saber se o endpoint vai ser chamado corretamente, se os parâmetros esperados estão corretos etc.