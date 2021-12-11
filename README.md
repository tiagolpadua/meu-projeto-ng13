# Dicas para Iniciar um Projeto Angular@13

Existem muitas configurações adicionais interessantes após o `ng new meu-projeto`, como por exemplo adicionar suporte a ESLint, Prettier, pre-commit hooks, etc. Vou listar aqui as principais configurações que recomendo em todo projeto novo Angular.

## ESLint

O TSLint era o linter default utilizado e que já vinha pré-configurado nos projetos Angular até a versão 11 do Angular, no entanto, o projeto foi deprecado e a configuração de um linter passou a ser um passo adicionar nos projetos Angular. Felizmente, é bem fácil fazê-lo. As orientações oficiais se encontram neste link: <https://github.com/angular-eslint/angular-eslint>.

O primeiro passo é executar o schematics do angular-eslint:

```bash
ng add @angular-eslint/schematics
```

A priori, só isso já basta, mas recomendo fazer mais algumas configurações, como por exemplo, incluir um script lint:fix para executar o eslint e corrigir automaticamente as falhas em seu projeto, para isso, ajuste seu `package.json`:

```json
{
  "name": "meu-projeto-ng13",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test",
    "lint": "ng lint",
    "lint:fix": "ng lint --fix"
  },
  ...
}
```

Outra configuração adicional que acho interessante é executar a tarefa de `lint:fix` a cada execução do projeto e a tarefa de `lint` antes de cada build, para garantir que o código esteja seguindo todas as regras quando for construído, para isso, ajuste a tarefa de build incluindo o passo de `lint` previamente:

`package.json`

```json
{
  "name": "meu-projeto-ng13",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "npm run lint:fix && ng serve",
    "build": "npm run lint && ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test",
    "lint": "ng lint",
    "lint:fix": "ng lint --fix"
  },
  ...
}
```

Ao rodar o lint você pode receber esta mensagem de erro:

```pre
Linting "meu-projeto-ng13"...
=============
WARNING: You are currently running a version of TypeScript which is not officially supported by @typescript-eslint/typescript-estree.
You may find that it works just fine, or you may not.
SUPPORTED TYPESCRIPT VERSIONS: >=3.3.1 <4.5.0
YOUR TYPESCRIPT VERSION: 4.5.3
Please only submit bug reports when using the officially supported version.
=============
```

Para corrigi-la, você precisará atualizar a versão do `@typescript-eslint/eslint-plugin` e do `@typescript-eslint/parser` para uma versão mais recente como por exemplo `5.6.0` no seu arquivo `package.json`.

Instale a extensão compatível com o ESLint no seu editor de código, no meu caso utilizo o VSCode e gosto também de incluir no arquivo de recomendações de extensões do VSCode a extensão do ESLint, assim, se um colega de trabalho baixar o projeto e utilizar o VSCode, ele receberá a recomendação de instalação do plugin:

`.vscode/extensions.json`

```json
{
  // For more information, visit: https://go.microsoft.com/fwlink/?linkid=827846
  "recommendations": [
    "angular.ng-template",
    "dbaeumer.vscode-eslint"
  ]
}
```

## Prettier

O ESLint é um bom "linter" de código, irá tentar adequar o seu código às boas práticas e regras que ele tem disponíveis. Porém, para a formatação "visual" do código, o Prettier se sai melhor. Para nossa sorte é possível integrar o ESLint e o Prettier através de plugins. Para isso, primeiro você deve desativar algumas verificações do ESLint que podem entrar em conflito com o Prettier, isso é feito pelo pacote `eslint-config-prettier`:

```bash
npm install - save-dev eslint-config-prettier
```

Inclua mais a extensão prettier no `.eslintrc.json`:

```json
{ 
  ...
  "extends": [
    "plugin:@angular-eslint/template/recommended",
    "prettier"
  ]
  ...
}
```

Agora vamos instalar o Prettier e configurar sua integração com o ESLint:

```bash
npm install --save-dev eslint-plugin-prettier
npm install --save-dev --save-exact prettier
```

Vamos fazer mais um ajuste no `eslintrc.json`:

```json
{
  ...
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  },
  ...
}
```

Devemos também criar um arquivo `.prettierrc` na raiz do projeto com algumas configurações específicas do Prettier:

```yaml
overrides:
  - files: '*.html'
    options:
      parser: 'html'
  - files: '*.component.html'
    options:
      parser: 'angular'
```

Pronto, agora se você executar o comando `npm run lint` serão exibidos alguns erros, basta executar `npm run lint:fix` para que seu projeto fique ajustado às novas configurações.

## Formatando a cada salvamento no VSCode

Pra você que como eu não gosta de ter trabalho formatando código, agora você pode utilizar um recurso do VSCode que irá formatar automaticamente o projeto de acordo com as regras que foram estabelecidas cada vez que você salvar um arquivo. Vá em `File -> Preferences -> Settings`, selecione o seu projeto, busque na caixa a opção "Format On Save" e deixe o checkbox marcado. Deve ser criado um arquivo `.vscode/settings.json` com o seguinte conteúdo:

```json
{
    "editor.formatOnSave": true
}
```

Agora cada vez que você salvar um arquivo ele já vai ser formatado automaticamente.

## pre-commit

Pra fechar com chave de ouro, não seria bacana que antes de cada commit fosse executada automaticamente a verificação de lint para evitar que seja comitado código fora das regras? Isso pode ser feito de maneira simples, com a biblioteca `pre-commit`:

```bash
npm install --save-dev pre-commit
```

E agora podemos configurar os scripts que devem ser executados antes do commit no nosso `package.json`:

```json
{
  ...
  "pre-commit": [
    "lint"
  ],
  ...
}
```
