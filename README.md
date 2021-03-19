# Guide Ember.js

Instalando Ember.js

````
npm install -g ember-cli
````

Criando uma nova Aplicação

````
ember new <nome-da-aplicacao> --lang <idioma>
````

Iniciando server Ember

````
ember serve ou ember start
````

## Escreva algum HTML em um modelo

Começaremos editando o modelo. Este modelo está sempre na tela enquanto o usuário tem seu aplicativo carregado. Em seu editor, abra e mude para o seguinte:`application``app/templates/application.hbs`

app/templates/application.hbs

```handlebars
<h1>PeopleTracker</h1>

{{outlet}}
```

A Ember detecta o arquivo alterado e recarrega automaticamente a página para você em segundo plano. Você deve ver que a página de boas-vindas foi substituída por "PeopleTracker". Você também adicionou um a esta página, o que significa que qualquer rota será renderizada naquele lugar.`{{outlet}}`

## Defina uma Rota

Vamos construir uma aplicação que mostre uma lista de cientistas. Para isso, o primeiro passo é criar uma rota. Por enquanto, você pode pensar em rotas como sendo as diferentes páginas que compõem sua aplicação.

A Brasa vem com *geradores* que automatizam o código da caldeira para tarefas comuns. Para gerar uma rota, digite isso em uma nova janela terminal em seu diretório:`ember-quickstart`

```bash
ember generate route scientists
```

Você verá uma saída assim:

```text
installing route
  create app/routes/scientists.js
  create app/templates/scientists.hbs
updating router
  add route scientists
installing route-test
  create tests/unit/routes/scientists-test.js
```

Abra o modelo recém-criado e adicione o seguinte HTML:`app/templates/scientists.hbs`

app/templates/scientists.hbs

```handlebars
<h2>List of Scientists</h2>

{{outlet}}
```

Agora que temos a renderização do modelo, vamos dar-lhe alguns dados para renderizar. Fazemos isso especificando um *modelo* para essa rota, e podemos especificar um modelo editando .`scientists``app/routes/scientists.js`

Pegaremos o código criado para nós pelo gerador e adicionaremos um método ao:`model()``Route`

app/rotas/cientistas.js

```javascript
import Route from '@ember/routing/route';

export default class ScientistsRoute extends Route {
  model() {
    return ['Marie Curie', 'Mae Jemison', 'Albert Hofmann'];
  }
}
```

Agora vamos dizer a Ember como transformar essa matriz de strings em HTML. Abra o modelo e adicione o seguinte código para loop através do array e imprimi-lo:`scientists`

app/templates/scientists.hbs

```handlebars
<h2>List of Scientists</h2>

<ul>
  {{#each @model as |scientist|}}
    <li>{{scientist}}</li>
  {{/each}}
</ul>
```

## Criar um componente de interface do usuário

À medida que seu aplicativo cresce, você notará que está compartilhando elementos de interface do usuário entre várias páginas ou usando-os várias vezes na mesma página. A brasa facilita a refatoração de seus modelos em componentes reutilizáveis.

Vamos criar um componente que podemos usar em vários lugares para mostrar uma lista de pessoas.`<PeopleList>`

Como sempre, há um gerador que facilita as coisas para nós. Faça um novo componente digitando:

```bash
ember generate component people-list
```

Copie e cole o modelo no modelo do componente e edite-o para parecer o seguinte:`scientists``<PeopleList>`

app/componentes/people-list.hbs

```handlebars
<h2>{{@title}}</h2>

<ul>
  {{#each @people as |person|}}
    <li>{{person}}</li>
  {{/each}}
</ul>
```

Note que mudamos o título de uma string codificada ("Lista de Cientistas") para . O indica que é um argumento que será passado para o componente, o que facilita o reaproveitamento do mesmo componente em outras partes do aplicativo que estamos construindo.`{{@title}}``@``@title`

Também renomeamos para o mais genérico, diminuindo o acoplamento do nosso componente para onde ele é usado.`scientist``person`

Nosso componente é chamado , com base em seu nome no sistema de arquivos. Por favor, note que as letras P e L são maiúsculas.`<PeopleList>`

Salve este modelo e mude de volta para o modelo.`scientists`

Vamos dizer ao nosso componente:

1. Que título usar, através do argumento.`@title`
2. Que conjunto de pessoas usar, através do argumento. Nós forneceremos esta rota como a lista de pessoas.`@people``@model`

Precisamos fazer algumas mudanças no código que escrevemos antes.

No resto dos exemplos de código neste tutorial, sempre que adicionarmos ou removermos código, mostraremos um "diff". As linhas que você precisa remover têm um sinal de menos na frente delas, e as linhas que você deve adicionar têm um sinal de mais. Se você estiver usando um leitor de tela enquanto passa pelos Guias, recomendamos usar o Firefox e o NVDA ou o Safari e o VoiceOver para obter a melhor experiência.

Vamos substituir todo o nosso código antigo pela nossa nova versão componenteda:

app/templates/scientists.hbs

```handlebars
<h2>List of Scientists</h2>

<ul>
  {{#each @model as |scientist|}}
    <li>{{scientist}}</li>
  {{/each}}
</ul>
<PeopleList
  @title="List of Scientists"
  @people={{@model}}
/>
```

Volte para o seu navegador e você deve ver que a interface do usuário parece idêntica. A única diferença é que agora nós componentemos nossa lista em uma versão mais reutilizável e mais mantida.

Você pode ver isso em ação se você criar uma nova rota que mostra uma lista diferente de pessoas. Como um exercício adicional (que não vamos cobrir), você pode tentar criar uma rota que mostre uma lista de programadores famosos. Se você reutilizar o componente, você pode fazê-lo com quase nenhum código.`programmers``<PeopleList>`

## Respondendo às interações do usuário

Até agora, nosso aplicativo está listando dados, mas não há como o usuário interagir com as informações. Em aplicativos web, muitas vezes queremos responder às ações do usuário, como cliques ou pairando. Ember torna isso fácil de fazer.

Primeiro, podemos modificar o componente para incluir um botão:`<PeopleList>`

app/componentes/people-list.hbs

```handlebars
<h2>{{@title}}</h2>

<ul>
  {{#each @people as |person|}}
    <li>
      <button type="button">{{person}}</button>
    </li>
  {{/each}}
</ul>
```

Agora que temos um botão, precisamos ligá-lo para fazer *algo* quando um usuário clica nele. Para simplificar, digamos que queremos mostrar um diálogo com o nome da pessoa quando o botão é clicado.`alert`

Até agora, nosso componente é puramente apresentacional – ele leva algumas entradas como argumentos e as torna usando um modelo. Para introduzir *o comportamento* do nosso componente – manuseando o botão clique neste caso, precisaremos anexar algum *código* ao componente.`<PeopleList>`

Além do modelo, um componente também pode ter um arquivo JavaScript para este propósito exato. Vá em frente e crie um arquivo com o mesmo nome e no mesmo diretório do nosso modelo (), e cole no seguinte conteúdo:`.js``app/components/people-list.js`

app/components/people-list.js

```javascript
import Component from '@glimmer/component';
import { action } from '@ember/object';

export default class PeopleListComponent extends Component {
  @action
  showPerson(person) {
    alert(`The person's name is ${person}!`);
  }
}
```

*Nota: Se você quiser que este arquivo seja criado para você, você pode passar a bandeira `-gc` ao executar o gerador de componentes.*

Aqui, criamos uma classe de componentes básicos e adicionamos um método que aceita uma pessoa como argumento e traz à tona um diálogo de alerta com seu nome. O *decorador* indica que queremos usar esse método como *uma ação* em nosso modelo, em resposta à interação do usuário.`@action`

Agora que implementamos o comportamento desejado, podemos voltar ao modelo do componente e conectar tudo:

app/componentes/people-list.hbs

```handlebars
<h2>{{@title}}</h2>

<ul>
  {{#each @people as |person|}}
    <li>
      <button type="button">{{person}}</button>
      <button type="button" {{on 'click' this.showPerson}}>{{person}}</button>
    </li>
  {{/each}}
</ul>
```

Aqui, usamos o *modificador* para anexar a ação ao botão no modelo.`on``this.showPerson`

Há um problema com isso, porém – se você experimentou isso no navegador, você descobrirá rapidamente que clicar nos botões trará um diálogo de alerta que dizia "O nome da pessoa é !" – eek!`[Object MouseEvent]`

A causa desse bug é que escrevemos nossa ação para fazer uma discussão – o nome da pessoa – e esquecemos de passá-lo. A correção é bastante fácil:

app/componentes/people-list.hbs

```handlebars
<h2>{{@title}}</h2>

<ul>
  {{#each @people as |person|}}
    <li>
      <button type="button" {{on 'click' this.showPerson}}>{{person}}</button>
      <button type="button" {{on 'click' (fn this.showPerson person)}}>{{person}}</button>
    </li>
  {{/each}}
</ul>
```

Em vez de passar a ação diretamente para o modificador, usamos o ajudante para passar o argumento que nossa ação espera.`on``fn``person`

Sinta-se livre para experimentar isso no navegador. Finalmente, tudo deve se comportar exatamente como esperávamos!

## Construção para produção

Agora que escrevemos nosso aplicativo e verificamos que ele funciona em desenvolvimento, é hora de prepará-lo para implantar em nossos usuários.

Para isso, execute o seguinte comando:

```bash
ember build --environment=production
```

O comando empacota todos os ativos que compõem seu aplicativo — JavaScript, modelos, CSS, fontes da Web, imagens e muito mais.`build`

Neste caso, dissemos à Ember para construir para o ambiente de produção através da bandeira. Isso cria um pacote otimizado que está pronto para carregar para o seu host web. Uma vez que a compilação termine, você encontrará todos os ativos concatenados e minificados no diretório do seu aplicativo.`--environment``dist/`

A comunidade Ember valoriza a colaboração e a construção de ferramentas comuns com as qual todos dependem. Se você estiver interessado em implantar seu aplicativo para a produção de forma rápida e confiável, confira o addon [Ember CLI Deploy.](http://ember-cli-deploy.com/)

Se você implantar seu aplicativo em um servidor web Apache, primeiro crie um novo host virtual para o aplicativo. Para garantir que todas as rotas sejam manuseadas, adicione a seguinte diretiva à configuração de host virtual do aplicativo:`index.html`

```apacheconf
FallbackResource index.html
```

