## 1. Classe Simples
Crie uma classe chamada `Livro` com os atributos `titulo` e `autor`. Implemente um construtor e um método para exibir as informações do livro.

### Teste de Mesa
```java
Livro livro = new Livro("O Hobbit", "J.R.R. Tolkien");
livro.exibirInfo();
// Saída esperada:
// Título: O Hobbit
// Autor: J.R.R. Tolkien
```

---

## 2. Encapsulamento
Crie uma classe `ContaBancaria` com os atributos privados `numero` e `saldo`. Implemente métodos `depositar`, `sacar` e um método para exibir o saldo. Use getters e setters quando necessário.

### Teste de Mesa
```java
ContaBancaria conta = new ContaBancaria("1234");
conta.depositar(1000);
conta.sacar(250);
conta.exibirSaldo();
// Saída esperada:
// Saldo da conta 1234: 750.0
```

---

## 3. Métodos com Parâmetros
Crie uma classe `Calculadora` com métodos para somar, subtrair, multiplicar e dividir dois números. Implemente um método para exibir o resultado de cada operação.

### Teste de Mesa
```java
Calculadora calc = new Calculadora();
calc.somar(5, 3);      // Saída esperada: Soma: 8
calc.subtrair(10, 4);  // Saída esperada: Subtração: 6
calc.multiplicar(2, 7);// Saída esperada: Multiplicação: 14
calc.dividir(20, 4);   // Saída esperada: Divisão: 5
```

---

## 4. Estrutura de Controle e Laço de Repetição
Crie uma classe `Tabuada` com um método que recebe um número inteiro e imprime a tabuada desse número de 1 a 10 usando um laço de repetição.

### Teste de Mesa
```java
Tabuada tab = new Tabuada();
tab.imprimirTabuada(7);
// Saída esperada:
// 7 x 1 = 7
// 7 x 2 = 14
// ...
// 7 x 10 = 70
```

---

## 5. Arrays
Crie uma classe `NotasAlunos` que armazena as notas de 5 alunos em um array. Implemente um método para calcular e exibir a média das notas.

### Teste de Mesa
```java
NotasAlunos notas = new NotasAlunos();
notas.setNotas(new double[]{7.5, 8.0, 6.0, 9.0, 5.5});
notas.calcularMedia();
// Saída esperada:
// Média das notas: 7.2
```

---

## 6. Matrizes
Crie uma classe `AgendaContatos` que armazena nomes e telefones em uma matriz 5x2 (5 contatos, cada um com nome e telefone). Implemente métodos para:

- **Adicionar um contato em uma linha específica**
- **Exibir todos os contatos cadastrados**
- **Buscar e exibir o telefone de um contato pelo nome**

### Teste de Mesa
```java
AgendaContatos agenda = new AgendaContatos();
agenda.adicionarContato(0, "Ana", "9999-1111");
agenda.adicionarContato(1, "Bruno", "8888-2222");
agenda.adicionarContato(2, "Carla", "7777-3333");
agenda.exibirContatos();
// Saída esperada:
// Ana - 9999-1111
// Bruno - 8888-2222
// Carla - 7777-3333

agenda.buscarTelefone("Bruno");
// Saída esperada:
// Telefone de Bruno: 8888-2222
```

---

## 7. Construtores com Parâmetros
Crie uma classe `Aluno` com os atributos `nome` e `matricula`. Implemente um construtor que receba esses valores e um método para exibir os dados do aluno.

### Teste de Mesa
```java
Aluno aluno = new Aluno("Ana", "2023001");
aluno.exibirDados();
// Saída esperada:
// Nome: Ana
// Matrícula: 2023001
```

---

## 8. Modificadores de Acesso
Crie uma classe `Produto` com os atributos privados `nome` e `preco`. Implemente métodos públicos para acessar e modificar esses atributos (getters e setters). Adicione um método para exibir as informações do produto.

### Teste de Mesa
```java
Produto prod = new Produto();
prod.setNome("Caderno");
prod.setPreco(15.5);
prod.exibirInfo();
// Saída esperada:
// Produto: Caderno
// Preço: 15.5
```

---

## 9. Builder Pattern
Implemente uma classe `Carro` utilizando o padrão Builder. O carro deve ter atributos como `modelo`, `ano`, `cor` e `motor`. Crie objetos de `Carro` usando o Builder e exiba suas informações.

### Teste de Mesa
```java
Carro carro = new Carro.Builder()
    .setModelo("Civic")
    .setAno(2022)
    .setCor("Prata")
    .setMotor("2.0")
    .build();
carro.exibirInfo();
// Saída esperada:
// Modelo: Civic
// Ano: 2022
// Cor: Prata
// Motor: 2.0
```

---

> Tente resolver cada exercício antes de consultar exemplos prontos. Bons estudos!
