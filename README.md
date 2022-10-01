# Tutorial-React

**O objetivo final do tutorial é aprender um pouco do React,construindo um jogo da velha.**


**O que é React?**
 
 O react é um biblioteca declarativa que é muito eficiente,tudo que é construido através dele é feito por `componentes` ou seja pequenos códigos separados ,que no fim se juntam.
 
 Os componentes tem como função nos mostrar o que queremos ,vale lembrar que o react sempre vai rendenizar nossos componentes.

E vamos iniciar com as subclasses:

```jsx
class ShoppingList extends React.Component {
  render() {
    return (
      <div className="shopping-list">
        <h1>Lista de compras para {this.props.name}</h1>
        <ul>
          <li>Instagram</li>
          <li>WhatsApp</li>
          <li>Oculus</li>
        </ul>
      </div>
    );
  }
}

// Exemplo de uso: <ShoppingList name="Mark" />
```

Podemos ver que o `ShoppingList`  é um `class component`, um componente recebe parâmetros chamados `props`(propriedades), e retorna diversos elementos que são exibidos através do `render`.

* O `render` retorna o que queremos mostrar na tela ,"em particular `render` retorna um `elemento React`,que é uma descrição simplificada do que renderizar". 

* Usamos normalmente  uma sintaxe especial que é a `JSX` que facilita muito a escrita dos elementos ,por exemplo a sintaxe <div> é transformada em tempo de compilação para `React.createElement('div')` por exemplo como ficaria o exemplo acima:

```jsx
return React.createElement('div', {classname:'shopping-list'},
    React.createElement('h1',/* ...filhos de h1 ...*/)
    React.createElement('ul',/* ... filhos de ul ...*/)
);

```

* O jsx tem o mesmo poder do Javascript,podendo usar as expressões de javascript dentro das chaves do `jsx`,o shoppingList está somente rendenizando componentes da `DOM` como o <div> e <li> porém podemos rendenizar componente react personalizados ,como por exemplo o <ShoppingList> que vai possuir uma lista de compras dentro dele ,cada componente do React é encapsulado e pode agir de maneira idependente, o que nos permite contruir aplicações complexas a partir de componentes simples.

**Inspencionando o Código Inicial**

Se olharmos para nosso código no arquivo index.js podemos ver três componentes React :

* Quadrado(Square)
* Tabuleiro(Board)
* Jogo(Game)

O componente `Square` renderiza um único button, o componente `Square` corresponde a um quadradinho do nosso jogo,já o componente `Board`() vai renderizar nove `Square` ou seja o `board` corresponde ao tabuleiro do jogo.

Dentro da classe `Board` temos o método `renderSquare` que tem como parâmetro(i) que são as posições passadas nas div's logo abaixo e um return que nos retorna o componente `<Square/>`, temos o `render` que retorna uma constante e cinco div's,a constante tem nome de `status` e recebe o valor `Next player: X` a primeira `div` possui uma `classname` chamada `status` e dentro dela temos como conteúdo a constante `status`.

Já a segunda `div` possui uma classname chamada `Board row` e possui como conteúdo três `this.renderSquare` cada um com um numero referenciando suas posições,a função dele é acionar o método renderSquare e fazemos isso através do `this.renderSquare` e essa div vai se repetir mais duas vezes formando o tabuleiro.

E mais abaixo temos a class `Game` que vamos trabalhar nela mais tarde.

```Jsx
class Square extends React.Component {
  render() {
    return (
      <button className="square">
        {/* TODO */}
      </button>
    );
  }
}

class Board extends React.Component {
  renderSquare(i) {
    return <Square />;
  }

  render() {
    const status = 'Next player: X';

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}

class Game extends React.Component {
  render() {
    return (
      <div className="game">
        <div className="game-board">
          <Board />
        </div>
        <div className="game-info">
          <div>{/* status */}</div>
          <ol>{/* TODO */}</ol>
        </div>
      </div>
    );
  }
}

// ========================================

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<Game />);

```
**Passando Dados através de Props**

Agora vamos passar props entre nossos componentes,e vamos começar passando um prop chamado `value` para o componente `<square/>`  que está dentro do método `renderSquare`:

```Jsx
class Board extends React.Component {
  renderSquare(i) {
    return <Square value={i} />;
  }
}
```

E dentro do método `Render` vamos alterar o conteúdo do `button` para receber o prop, que acabamos de passar na `class Board` ,vamos recebê-lo através do `{this.props.value}` ou seja cada quadrado vai receber props do tabuleiro ,no tabuleiro temos divs que possuem três métodos cada e que passam uma sequência de números`(value)`, `{this.renderSquare(0)} {this.renderSquare(1)} {this.renderSquare(2)}`,ou seja cada quadrado vai possuir um valor que foi mandado pela props e acessamos pelo `this.props.value` que é o conteúdo do `button`.

**Fazendo um componente Interativo**

Vamos preencher o componente Square com um X quando clicarmos nele ,para isso precisamos ir ate o button do método render que pertence a classe `Square`,e adicionar o método `onClick={}` que executa determinada funcionalidade quando é clicado, para salvar a digitação e evitar o comportamento confuso de this,vamos usar a sintaxe arrow function para manipuladores de eventos:

```Jsx
class Square extends React.Component {
 render() {
   return (
     <button className="square" onClick={() => console.log('click')}>
       {this.props.value}
     </button>
   );
 }
}
```
Repare que com `onClick = {() => console.log('click')}`, estamos passando uma função como prop onClick, o React só chamará essa função depois de um clique. Esquecendo () => e escrevendo somente onClick = {console.log('click')} é um erro comum e seria acionado sempre que o componente fosse renderizado novamente.E o que fizemos agora é só para termos idéia de como funciona o onClick.

Como queremos que ao clicar nosso componente se "lembre" que foi clicado e preencha um X vamos usar o "estado" ou seja state.

>Os componentes React podem ter estado (state) configurando this.state em seus construtores. this.state deve ser considerado como privado para o componente React que o definiu. Vamos armazenar o valor atual do Square em this.state e alterá-lo quando o Square for clicado.

Primeiro vamos adicionar um constructor na classe Square:

```Jsx
class Square extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: null,
    };
  }

  render() {
    return (
      <button className="square" onClick={() => console.log('click')}>
        {this.props.value}
      </button>
    );
  }
}
```
>Em classes JavaScript, você sempre precisa chamar super ao definir o construtor de uma subclasse. Todas os componentes de classe React que possuem um método constructor devem iniciá-lo com uma chamada super (props).

**Movendo o State Para Cima**

Sabemos que cada square(quadrado) carrega seu valor ,podemos desenvolver de uma maneira aonde o Tabuleiro (board) pergunta para cada quadrado seu estado, mesmo sendo possivel, não é aconselhável desenvolver a aplicação dessa maneira,pois a aplicação será muito sucetível a erros, a melhor opção é guardar o estado da aplicação no componente pai (Board) e passar para cada componente através de `props` o que será exibido, da mesma forma que fizemos para passar os valores para cada quadrado.

>Para coletar dados de múltiplos filhos (children), ou para fazer dois filhos se comunicarem entre si, você precisa declarar um estado compartilhado em seu componente pai. O componente pai pode passar o estado de volta para os filhos através do uso de propriedades (props); isso mantém os componentes filhos em sincronia com os seus irmãos e também com o pai.

Agora vamos adicionar `constructor` no componente Tabuleiro e definir que seu estado atual irá ter um array com  9 posições preenchidas por (null), que corresponde aos 9 quadrados:

```Jsx
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
    };
  }

  renderSquare(i) {
    return <Square value={i} />;
  }

  render() {
    const status = 'Next player: X';

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}
```

O método renderSquare do Tabuleiro(board) está definido assim:

```Jsx
  renderSquare(i) {
    return <Square value={i} />;
  }

```
No começo desse tutorial nós passamos o `value` como props para cada quadrado, para que eles nos mostrassem os números passados em cada método e logo após isso trocamos os números pelo "X",que foi determinado no estado do próprio quadrado.
>Isso porque atualmente o Quadrado ignora o valor (value) recebido do Tabuleiro.


Novamente vamos modificar as propriedades ,primeiro iremos modificar o método `renderSquare`para definir o valor a partir do estado ,já que temos definido o array de quadrados no construtor do tabuleiro(board) que está dentro do `estado`.


```Jsx
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
    };
  }

  renderSquare(i) {
    return <Square value={i} />;
  }

  render() {
    const status = 'Next player: X';

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}
```

```Jsx
 renderSquare(i) {
    return <Square value={this.state.squares[i]} />;
  }
```

> Cada Square irá receber um "value" que será um "x" ou "o" ou `"null"para quadrados vazios`.

Agora vamos mudar o que acontece quando um quadrado é clicado,agora o tabuleiro mantém os quadrados que são preenchidos.Vamos criar uma maneira de cada quadrado atualizar o state do Tabuleiro.

>O state é considerado privado ao componente em que é definido, ou seja, nós não podemos atualizar o state do Tabuleiro diretamente do Quadrado.

Para mantermos a privacidade do state do Tabuleiro,*precisamos passar uma função responsável do Tabuleiro para o quadrado*,essa função vai ser chamada assim que o Quadrado for clicado,para isso precisamos editar o método `renderSquare` no Tabuleiro.

```Jsx
renderSquare(i) {
    return (
      <Square
        value={this.state.squares[i]}
        onClick={() => this.handleClick(i)}
      />
    );
  }
```
> Adicionamos parentesis para que o JavaScript não insira ponto e virgula após o return e quebre o código.

Agora iremos passar duas props do `Tabuleiro` para o `quadrado` que serão value e onClick,quando o quadrado for clicado automaticamente a função onClick será chamada e vamos mudar as seguintes coisas no componente `Quadrado`,Substituiremos `this.state.value` por `this.props.value` no método render,também vamos alterar o `this.setState()` por `this.props.onClick()` também no método `render` e por último vamos deletar o construtor do componente quadrado.

```Jsx

class Square extends React.Component {
  render() {
    return (
      <button
        className="square"
        onClick={() => this.props.onClick()}
      >
        {this.props.value}
      </button>
    );
  }
}

```
Quando um quadrado for clicado a função onClick que é provida do tabuleiro será chamada e isso vai acontecer:

* A propriedade onClick do DOM embutida no componente <button> diz ao React para criar um evento de escuta (event listener).
* Quando o botão é clicado, o React irá chamar a função o manipulador de eventos onClick definido no método render() do Quadrado.
* Esse manipulador de eventos chamará a função recebida através da propriedade onClick que foi criada no Tabuleiro (this.props.onClick()).
* Como o Tabuleiro passou onClick={() => this.handleClick(i)} para o Quadrado, a função handleClick(i) será chamada quando o Quadrado for clicado.
* Como nós não definimos a função handleClick() ainda, nosso código quebrará.

Quando clicarmos em um quadrado ocorrerá um erro pois ainda não definimos,a nossa função handleClick,agora adicionaremos ela na classe `Board`:

```Jsx
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
    };
  }

  handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = 'X';
    this.setState({squares: squares});
  }

  renderSquare(i) {
    return (
      <Square
        value={this.state.squares[i]}
        onClick={() => this.handleClick(i)}
      />
    );
  }

  render() {
    const status = 'Next player: X';

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}
```
Após essas mudanças, seremos capazes novamente de clicar nos Squares para preenche-los. Entretanto, agora o state é guardado no componente Board ao invés de em cada Square. Quando o state do Board for alterado, os componentes Square serão re-renderizados automaticamente. Manter o state de todos os quadrados no componente Board nos permitirá determinar o vencedor no futuro.

>Note como na função handleClick, nós chamamos .slice() para criar uma cópia do array de quadrados para o modificar ao invés de faze-lo no array existente. Explicaremos o motivo quando criarmos uma copia do array de quadrados na próxima sessão.

**Porque a Imutabilidade é Importante**

Na função handleClick usamos em uma constante criada o `slice` usado para criar uma cópia,no caso do array squares.

```Jsx
handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = 'X';
    this.setState({squares: squares});
  }
```
E o que isso tem a ver com imutabilidade? bom temos duas maneiras de alterar dados ,uma é alterando ele diretamente em seu valor a outra é substituir o dado antigo por um novo, criando uma cópia com as alterações desejadas.

* Mudando dados com mutação:

```Jsx
var player = {score: 1, name: 'Jeff'};
player.score = 2;
// Agora o player é {score: 2, name: 'Jeff'}

```
* Mudando dados sem mutação:

```Jsx
var player = {score: 1, name: 'Jeff'};

var newPlayer = Object.assign({}, player, {score: 2});
// Agora o player não sofreu alteração, mas o newPlayer é {score: 2, name: 'Jeff'}

// Ou então se você estiver usando a sintaxe "object spread", você pode escrever:
// var newPlayer = {...player, score: 2};
```
Vendo assim os dados parecem iguais porém por não mudarmos diretamente ,nós ganhamos diversos beneficios.


* Primeiro a imutabilidade faz a complexidade das features se tornarem bem mais simples de serem implementadas,por exemplo nessa aplicação vamos adicionar uma feature de "máquina do tempo" que vai possibilitar ver o histórico do jogo e voltar as jogadas anteriores,essa funcionalidade não pertence somente ao jogo ,`desfazer e refazer as ações é um requisito das aplicações` e evitar a mutação mos permite manter o histórico  das versões anteriores do jogo intacta e reutiliza-las mais tarde.

* Segundo Detectar mudanças em objetos que já foram mutados é muito dificil,pois foram mutados diretamente.Detectar mudanças em objetos imutáveis é facil pois podemos compara-lo com a versões anteriores e a partir dai entendermos o que foi alterado.


**Componentes de Função**

Agora vamos alterar o class component Square para um function component ,um componente mais simples que não possui state e só possui um método `render`,na classe é definido o `extends` de `React.Component` porém na função vamos passar uma função `square` recebendo `props`(que são os dados que o Board está enviando) como entrada e retorna o será renderizado.

>Nós modificamos `this.props` para `props` *(pois o this referencia o componente da classe)*.

```Jsx
function Square(props) {
  return (
    <button className="square" onClick={props.onClick}>
      {props.value}
    </button>
  );
}
```
>Quando modificamos Square para ser um componente funcional, também modificamos onClick={() => this.props.onClick()} para uma versão mais curta: onClick={props.onClick} (note a ausência dos parentêses em ambos os lados).

**Trocando Turnos**

Nesse momento vamos consertar um defeito no nosso jogo precisamos passar para ele os "O" para serem marcados no tabuleiro como o "X" já é passado.


Vamos definir o "X" como primeira jogada por padrão,com isso vamos inserir la no state inicial do "Board" .

```Jsx
class Board extends React.Component {
  constructor(props){
    super(props);
    this.state ={
      squares: Array(9).fill(null),
      xIsNext: true,
    };
  }
}
```
Sempre que um jogador fizer uma jogada, `xIsNext` (um boolean) será trocado para determinar qual jogador será o próximo e o state do jogo será salvo. Nós atualizaremos a função `handleClick` do Board para trocar o valor de xIsNext: 

```Jsx
 handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      squares: squares,
      xIsNext: !this.state.xIsNext,
    });
  }
```
Ao fazermos essa mudança ,"X" e "O" podem trocar de turnos.

Também vamos modificar o texto de “status” na função render do Board para que ela passe a exibir quem jogará o próximo turno.

```Jsx
render() {
    const status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');

    return (
      // o restante não tem alterações

```
Depois das alterações o componente Board vai ficar assim.

```Jsx
class Board extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      squares: Array(9).fill(null),
      xIsNext: true,
    };
  }

  handleClick(i) {
    const squares = this.state.squares.slice();
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      squares: squares,
      xIsNext: !this.state.xIsNext,
    });
  }

  renderSquare(i) {
    return (
      <Square
        value={this.state.squares[i]}
        onClick={() => this.handleClick(i)}
      />
    );
  }

  render() {
    const status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}

```

**Declarando um Vencedor**
 
 Agora que mostramos quem jogará o próximo turno, também deveríamos mostrar quando o jogo foi vencido e que não há mais turnos a serem jogados.

 ```Jsx
function calculateWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
}

 ```
 Dado um array de 9 quadrados, esta função irá verificar se há um vencedor e retornará 'X', 'O' ou null conforme apropriado.

 Chamaremos `calculateWinner(squares)` na função render do Board para checar se um jogador venceu. Caso tenha vencido, podemos mostrar um texto como “Winner: X” ou “Winner: O”. Vamos substituir a declaração de status na função render com esse código:

 ```Jsx
 render() {
    const winner = calculateWinner(this.state.squares);
    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }
 };
 ```
 Agora vamos modificar a função `handleClick` do Board para retornar antes,ignorando o click,caso alguém ,tenha vencido o jogo ou se o quadrado (square) já esteja ocupado:

```Jsx
 handleClick(i) {
    const squares = this.state.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      squares: squares,
      xIsNext: !this.state.xIsNext,
    });
  }
```
Com isso finalizamos o nosso jogo da velha.

**Armazenando um Histórico de Jogadas**

Como a nossa modificação do array `square` foi usando o `slice` ,para criar uma nova cópia do array não modificando ele na raiz,Isso nos permitirá o armazenamento de cada versão anterior do array squares e que possamos navegar entre os turnos que já tenham acontecido.

Vamos armazenar os arrays squares anteriores em um outro array chamado history. O array history representa todos os estados do tabuleiro, da primeira à última jogada, e tem uma forma parecida com essa:

```Jsx
history = [
  // Antes da primeira jogada
  {
    squares: [
      null, null, null,
      null, null, null,
      null, null, null,
    ]
  },
  // Depois da primeira jogada
  {
    squares: [
      null, null, null,
      null, 'X', null,
      null, null, null,
    ]
  },
  // Depois da segunda jogada
  {
    squares: [
      null, null, null,
      null, 'X', null,
      null, null, 'O',
    ]
  },
  // ...
]
```
Agora vamos decidir qual componente pertencerá ao tate do `history`.

**Trazendo o State pra Cima, Novamente**

Queremos que o componente Game, o de mais alto nível, mostre uma lista com as jogadas anteriores. Para poder fazer isso, ele precisará acessar o history, então, temos que trazer o state history para cima, colocando-o no componente de mais alto nível, o componente Game.

Ao colocar o state `history` no componente Game conseguimos remover o state squares do seu componente filho Board.Assim como trouxemos para cima o state do componente Square para o componente Board, agora estamos trazendo o state do componente `Board para o componente de mais alto nível, Game`. 

Isso dá ao componente Game total controle sobre os dados do Board e permite que instrua o Board a renderizar turnos anteriores a partir do history.

Primeiro iremos modificar o state inicial para o componente Game em seu construtor.

```Jsx
class Game extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      history: [{
        squares: Array(9).fill(null),
      }],
      xIsNext: true,
    };
  }

  render() {
    return (
      <div className="game">
        <div className="game-board">
          <Board />
        </div>
        <div className="game-info">
          <div>{/* status */}</div>
          <ol>{/* TODO */}</ol>
        </div>
      </div>
    );
  }
}
```

Em seguida, faremos com que o componente Board receba as props squares e onClick do componente Game. Agora, uma vez que temos apenas um manipulador de clique no Board para vários Squares, vamos precisar passar a localização de cada Square para o manipulador onClick para indicar qual Square foi clicado.

* Deletar o contructor do Board.
* Substituir this.state.squares[i] por this.props.squares[i] na função renderSquare do Board.
* Substituir this.handleClick(i) por this.props.onClick(i) na função renderSquare do Board.

E assim que vai ficar o nosso componente Board:

```Jsx
class Board extends React.Component {
  handleClick(i) {
    const squares = this.state.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      squares: squares,
      xIsNext: !this.state.xIsNext,
    });
  }

  renderSquare(i) {
    return (
      <Square
        value={this.props.squares[i]}
        onClick={() => this.props.onClick(i)}
      />
    );
  }

  render() {
    const winner = calculateWinner(this.state.squares);
    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }

    return (
      <div>
        <div className="status">{status}</div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
}

```
Vamos tabém atualizar a função render do componente `Game` para usar a entrada mais recente do histórico para determinar o status do jogo.

```Jsx
render() {
    const history = this.state.history;
    const current = history[history.length - 1];
    const winner = calculateWinner(current.squares);
    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }

    return (
      <div className="game">
        <div className="game-board">
          <Board
            squares={current.squares}
            onClick={(i) => this.handleClick(i)}
          />
        </div>
        <div className="game-info">
          <div>{status}</div>
          <ol>{/* TODO */}</ol>
        </div>
      </div>
    );
  }
```
Agora que o componente Game está renderizando o status do jogo,podemos remover o código correspondente do método `render` do componente `Board`.

```Jsx
  render() {
    return (
      <div>
        <div className="board-row">
          {this.renderSquare(0)}
          {this.renderSquare(1)}
          {this.renderSquare(2)}
        </div>
        <div className="board-row">
          {this.renderSquare(3)}
          {this.renderSquare(4)}
          {this.renderSquare(5)}
        </div>
        <div className="board-row">
          {this.renderSquare(6)}
          {this.renderSquare(7)}
          {this.renderSquare(8)}
        </div>
      </div>
    );
  }
```
E também não podemos esquecer de mover o método handleClick do componente `Board` para o componente Game.Nós também precisamos modificar handleClick pois o state do componente Game está estruturado de maneira diferente. No componente Game, dentro do método handleClick, nós concatenamos novas entradas do histórico de jogadas em history.

```Jsx
handleClick(i) {
    const history = this.state.history;
    const current = history[history.length - 1];
    const squares = current.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      history: history.concat([{
        squares: squares,
      }]),
      xIsNext: !this.state.xIsNext,
    });
  }
```
>Ao contrário do método de arrays push(), que você talvez possa estar mais familiarizado, o método concat() não modifica o array original, por isso preferimos utilizá-lo.

**Mostrando Jogadas anteriores**

Agora que estamos gravando o histórico do jogo da velha ,podemos mostra-lo para o jogador como uma lista de jogadas anteriores.

Aprendemos anterioremente que os elementos React são objetos JavaScript de primeira classe; podemos passá-los livremente por nossas aplicações. Para renderizar múltiplos itens em React, podemos utilizar um array de elementos React.

Usando o método `map()`,nós podemos mapear nosso histórico de jogadas para elementos React,representando botões na tela e também mostrar uma lista de botões que "pulam" para jogadas anteriores.

```Jsx
 render() {
    const history = this.state.history;
    const current = history[history.length - 1];
    const winner = calculateWinner(current.squares);

    *const moves = history.map((step, move) => {
     * const desc = move ?
      *  'Go to move #' + move :
       * 'Go to game start';
      *return (
       * <li>
        *  <button onClick={() => this.jumpTo(move)}>{desc}</button>
        *</li>
      *);
    *});

    let status;
    if (winner) {
      status = 'Winner: ' + winner;
    } else {
      status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
    }

    return (
      <div className="game">
        <div className="game-board">
          <Board
            squares={current.squares}
            onClick={(i) => this.handleClick(i)}
          />
        </div>
        <div className="game-info">
          <div>{status}</div>
         *<ol>{moves}</ol>
        </div>
      </div>
    );
  }
```
À medida que iteramos através do array history, a variável step se refere ao valor do elemento history atual, e move se refere ao índice do elemento history atual. Estamos interessados ​​apenas em move aqui, portanto step não está sendo atribuído a nada.

Em cada jogada no histórico do jogo da velha,nós criamos um itemde lista <li> que possui um botão <button>.O botão tem um manipulador onClick que chama um método chamado this.jumpTo(). Nós ainda não implementamos o método jumpTo().

**Implementando a Viagem no Tempo**

No histórico do Jogo da Velha, cada jogada anterior tem um único ID associado a ela: é o número sequencial da jogada. As jogadas nunca são reordenadas, apagadas, ou inseridas no meio, então é seguro utilizar o index da jogada como a chave.

no método `render` do component `Game`,podemos adicionar uma chave como <li key={move}> assim eliminamos o aviso do React sobre as chaves.

```Jsx
  const moves = history.map((step, move) => {
      const desc = move ?
        'Go to move #' + move :
        'Go to game start';
      return (
       * <li key={move}>
          <button onClick={() => this.jumpTo(move)}>{desc}</button>
        </li>
      );
    });

```
Por não definirmos o método `jumpTo` ao clicar nos botões vai nos causar erros,antes de implementá-lo, vamos adicionar `stepNumber` ao state do componente Game para indicar qual passo estamos visualizando no momento.

```Jsx
class Game extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      history: [{
        squares: Array(9).fill(null),
      }],
      * stepNumber: 0,
        xIsNext: true,
    };
  }
}
```
Agora definiremos o `jumpTo` no componente `Game` para atualizar aquele `stepNumber`.Também definimos xIsNext para true caso o número que estejamos atribuindo a stepNumber seja par:

```Jsx
handleClick(i) {
    // esse método não mudou
  }

  jumpTo(step) {
    this.setState({
      stepNumber: step,
      xIsNext: (step % 2) === 0,
    });
  }

  render() {
    // esse método não mudou
  }
```
Observe no método jumpTo, não atualizamos a propriedade de histórico do estado. Isso ocorre porque as atualizações de estado são mescladas ou, em palavras mais simples, o react atualizará apenas as propriedades mencionadas no método setState, deixando o estado restante como está. 

O state `stepNumber` que adicionamos reflete a jogada mostrada ao usuário nesse momento.Depois de fazermos uma nova jogada, precisamos atualizar stepNumber adicionando `stepNumber: history.length` como parte do argumento de `this.setState`. Isso certifica que não ficaremos presos mostrando a mesma jogada após uma novo ter sido feita.

Também iremos substituir a leitura de `this.state.history` por `this.state.history.slice(0, this.state.stepNumber + 1)`. Isso certifica que se nós “voltarmos no tempo”, e então fizermos uma nova jogada a partir daquele ponto, descartamos todo o histórico do “futuro” que agora se tornaria incorreto.

```Jsx
handleClick(i) {
    const history = this.state.history.slice(0, this.state.stepNumber + 1);
    const current = history[history.length - 1];
    const squares = current.squares.slice();
    if (calculateWinner(squares) || squares[i]) {
      return;
    }
    squares[i] = this.state.xIsNext ? 'X' : 'O';
    this.setState({
      history: history.concat([{
        squares: squares
      }]),
      stepNumber: history.length,
      xIsNext: !this.state.xIsNext,
    });
  }
```
E para finalizar ,vamos modificar o método `render` do componebte `Game` ,que irá deixar de renderizr sempre a última jogada e a partir de agora vai renderizar apenas a jogada selecionada de acordo com `stepnumber`.

```Jsx
render() {
    const history = this.state.history;
    const current = history[this.state.stepNumber];
    const winner = calculateWinner(current.squares);
```