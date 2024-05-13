# SOLID
SOLID é um acrônimo que consolida 5 itens que são considerados como boas práticas no mundo do desenvolvimento orientado a objetos.

O idealizador dessa ideia nasceu no livro **Agile software Development - Principles, Patterns, and Practices by Robert C. Martin**. Robert C. Martin, ou conhecido também como Uncle Bob, foi um dos responsáveis por assinar o **Manifesto Ágil** e de criação do **Clean Code**.

- SRP = Single Responsibility Principle
- OCP = Open-closed Principle
- LSP = Liskov Substitution Principle
- ISP = Interface Segregation Principle
- DIP = Dependency Inversion Principle

## Single Responsibility Principle (SRP)
Significa que um classe deve ter apenas uma responsabilidade. Uma classe não pode fazer coisas a mais do que ela foi criada para fazer. Quando uma classe começa a ter muita responsabilidade, ela passa a ferir o princípio da SRP, além de complicar a forma como está se trabalhando com Orientação a Objetos. 

Uma forma simples de saber se estamos ou não ferindo essa regra é:
- Uma classe deve ter um e apenas um motivo para mudar.

### Exemplo de classe que está ferindo o princípio
```php
<?php

class Course
{
    private $name;
    private $categoria;
    private $descrição;

    public function connection()
    {
        $pdo = new PDO();
        return $pdo;
    }

    public function createCategoria()
    {
        $this->connection()->insert($this->categoria);
    }

    public function createCourse()
    {
        $this->connection()->insert($this->name);
    }

    public function getName()
    {
        return $this->name;
    }

    public function setName($name)
    {
        $this->name = $name;
        return $this;
    }

    public function getCategoria()
    {
        return $this->categoria;
    }

    public function setCategoria($categoria)
    {
        $this->categoria = $categoria;
        return $this;
    }

    public function getDescrição()
    {
        return $this->descrição;
    }

    public function setDescrição($descrição)
    {
        $this->descrição = $descrição;
        return $this;
    }
}
```
A classe a cima rem responsabilidade de:
- Criar conexão com banco de dados;
- Persistir dados; 
- Mudar o valor das propriedades dessa classe.

Toda vez que tivermos que efetuar uma modificação essa modificação vai poder impactar mais lugares de uma mesma classe. Portanto, isso significa que essa classe tem mais de uma responsabilidade.

Uma forma simples de resolver o problema da classe **Course** seria:
- Deixar a clase Course como entidade anemica (apenas com geters e setters);
- Criar uma entidade de Categoria. Onde poderíamos adicionar uma categoria nesse curso.
- Criar um repositório que faz a conexão com banco de dados e persiste o valor dessas classes criadas.

## Open-closed Principle (OCP)
Toda classe tem que ser **aberta para extenão** e **fechada para modificação**.

Muitas vezes nós temos uma classe e toda vez que precisamos adicionar um componente novo ou um comportamento novo no nosso sistema nós percebos que estamos aumentando determinada classe de tamanho, com novas condições para ela poder suportar esse novo requisito.

O problema é que estamos trabalhando com Orientação Objeto nós somos capazes de conseguir trabalhar com esses objetos e facilitar o processo de manutenção.

Basicamente a aplicação desse princípio funciona da seguinte maneira. Ao invés de criarmos uma classe e toda vez que mudar alguma coisa ter que incrementar essa classe, crie uma **base de uma classe** e toda vez que precisarmos adicionar algo novo no sistema, nós simplesmente extendemos essa base e conseguimos componentizar melhor essa aplicação.

### Exemplo de classe que está ferindo o princípio
```php
<?php

class Video
{
    private $type;

    public function calculaInteresse()
    {
        if ($this->type == "Movie") {
            // calcula interesse baseado em filme
        } elseif ($this->type == "TVShow") {
            // calcula interesse baseado em serie
        }
    }
}
```

O problema dessa classe é que se surgir um novo interesse, teremos que alterar essa classe adicionando esse novo interesse. Portanto, essa classe está engessada. O conceito do Open-Closed é o oposto disso.

### Exemplo de classe seguindo o princípio do Open-Closed
```php
<?php

abstract class Video
{
    abstract public function calcularInteresse();
}
```

```php
<?php

class Movie extends Video
{
    public function calcularInteresse()
    {
        // calcula
    }
}
```

```php
<?php

class TVShow extends Video
{
    public function calcularInteresse()
    {
        // calcula
    }
}
```

## Liskov Substitution Principle (LSP)
A ideia do princípio de Liskov são:
- As subclasses podem ser substituídas por suas classes pai. Uma vez que temos uma subclasse que extende de uma classe pai, essa subclasse pode ser substituída pela sua classe pai.
- Um exemplo clássico é o exemplo do pato de verdade com o pato de brinquedo que usa pilhas. Não é possível efetuar a substituíção deles, pois um deles utiliza pilha.

### Exemplo de classe seguindo o princípio de Liskov Substitution
```php
<?php

class Movie
{
    public function play()
    {
        // play no vídeo
    }

    public function increaseVolume()
    {
        // aumenta o volume
    }
}
```

```php
<?php

class TheLionKing extends Movie
{
    
}
```

Em tese, toda vez que criarmos:
```php
$movie = new Movie();
```

Nós podemos substituir por:
```php
$movie = new TheLionKing();
```

É essa a ideia do princípio de Liskov Substitution. Portanto, uma vez que o **TheLionKing** é um **Movie** nós podemos utiliza-lo no lugar do **Movie**.

Agora se por um acaso nós tivermos uma classe que por exemplo altere algum método utilizado pelo **Movie**, por exemplo:

```php
<?php

class ModernTimes extends Movie
{
    public function increaseVolume()
    {
        // não tem som
    }
}
```

E tentarmos utilizar por exemplo:
```php
$movie = new ModernTimes();
$movie->increaseVolume();
```
Vai lançar um erro, pois esse tipo de Movie não tem som. E portanto não consiguimos substituír com a subclasse a classe pai e portanto violando o princípio de Liskov Substitution. O **ModerTimes** parece ser um **Movie**, mas **não é** pois não é possível utilizar o método **increaseVolume**.

## Interface Segregation Principle (ISP)
- Uma classe não é obrigada a implementar interfaces que ela não utilizará. Se Por um acaso precisar não implementar algum método de alguma interface que for necessária utilizar, nós criamos uma interface somente com esse método, ou seja nós segregamos essa classe.

### Exemplo de classe seguindo o princípio de Interface Segregation
```php
<?php

interface Movie
{
    public function play();

    public function increaseVolume();
}
```

```php
<?php

class TheLionKing implements Movie
{
    public function play()
    {
        // play the movie
    }

    public function increaseVolume()
    {
        // increasing volume
    }
}
```
Até aqui tudo certo, porém quando formos criar a classe **ModernTimes** será diferente, pois o filme é mudo:

```php
<?php
class ModernTimes implements Movie
{
    public function play()
    {
        // play the movie
    }

    public function increaseVolume()
    {
        // esse método não será utilizado, porém terá de ser implementado
    }
}
```
Para resolver esse problema de ter um método não implementado nós vamos segregar essa classe criando uma nova interface.

```php
<?php

interface Movie
{
    public function play();
}
```

```php
<?php

interface AudioControl
{
    public function increaseVolume();
}
```

```php
<?php

class TheLionKing implements Movie, AudioControl
{
    public function play()
    {
        // play the movie
    }

    public function increaseVolume()
    {
        // increasing volume
    }
}
```

```php
<?php
class ModernTimes implements Movie
{
    public function play()
    {
        // play the movie
    }
}
```

Com isso nós conseguimos remover o método inutilizado na classe.
