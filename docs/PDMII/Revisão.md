# Revisão Conceitual: PDMII e LDDM

## 1. Introdução ao Jetpack Compose

O Jetpack Compose é um toolkit moderno para construir interfaces de usuário nativas no Android. Ele adota uma abordagem declarativa, o que significa que você descreve como sua UI deve ser em diferentes estados, e o Compose se encarrega de atualizar a UI quando o estado muda.

**Linguagem de Programação:** A linguagem mais utilizada e recomendada para desenvolver aplicativos Android com Jetpack Compose é o Kotlin. O Kotlin oferece recursos que se alinham perfeitamente com a natureza declarativa do Compose, como funções de extensão, lambdas e delegação de propriedades.

## 2. Gerenciamento de Estado no Jetpack Compose

O gerenciamento de estado é um dos pilares do Jetpack Compose, garantindo que a interface do usuário seja reativa e se atualize automaticamente quando os dados subjacentes mudam.

### O que é "Estado"?

Em uma aplicação, "estado" refere-se a qualquer valor que pode mudar ao longo do tempo e que afeta a interface do usuário. Exemplos incluem o texto digitado em um campo, a visibilidade de um elemento, a cor de um componente ou uma lista de itens.

### remember e mutableStateOf

No Jetpack Compose, o principal mecanismo fundamental para gerenciar o estado de forma reativa é a combinação de `remember` e `mutableStateOf`.

**mutableStateOf:** Cria um objeto observável que contém um valor. Quando o valor dentro de um MutableState muda, o Compose é notificado e recompõe (redesenha) os Composables que leem esse estado.

```kotlin
val count = mutableStateOf(0)
```

**remember:** É uma função Composable que armazena um objeto na memória durante a recomposição. Sem `remember`, o estado seria redefinido para seu valor inicial a cada recomposição. Ele "lembra" o valor entre as recomposições da função Composable.

```kotlin
val count = remember { mutableStateOf(0) }
```

Isso garante que, mesmo que a função Composable seja chamada novamente, o valor de `count` persistirá.

### Uso do by com remember (Delegação de Propriedades)

O Kotlin oferece um recurso chamado delegação de propriedades que simplifica a sintaxe ao usar `mutableStateOf` com `remember`. O operador `by` permite que você acesse diretamente o valor do MutableState sem precisar usar `.value`.

**Compare:**

Sem `by`:
```kotlin
val countState = remember { mutableStateOf(0) }
Text(text = "Contagem: ${countState.value}")
Button(onClick = { countState.value++ }) {
    Text("Incrementar")
}
```

Com `by`:
```kotlin
var count by remember { mutableStateOf(0) } // 'var' é necessário aqui
Text(text = "Contagem: $count")
Button(onClick = { count++ }) {
    Text("Incrementar")
}
```

A sintaxe `by` torna o código mais conciso e legível, permitindo que você trate `count` como uma variável comum, enquanto por baixo dos panos, ela ainda é um MutableState.

### State Hoisting (Elevação de Estado)

State Hoisting é um padrão no Jetpack Compose onde o estado é movido para cima na hierarquia de Composables, para um ancestral comum que pode gerenciar e observar esse estado. Isso torna os Composables mais reutilizáveis, testáveis e menos acoplados ao estado.

**Benefícios:**

- **Unidirecionalidade do fluxo de dados:** O estado flui para baixo (do pai para o filho) e os eventos fluem para cima (do filho para o pai).
- **Reutilização:** Composables filhos se tornam "stateless" (sem estado próprio), facilitando a reutilização em diferentes contextos.
- **Testabilidade:** Componentes sem estado são mais fáceis de testar.
- **Separação de preocupações:** Onde o estado é definido e modificado é separado de onde ele é usado na UI.

**Exemplo:** Em vez de um `CattleCardComponent` gerenciar seu próprio estado de "saúde", o estado de saúde seria elevado para uma tela ou ViewModel que gerencia a lista de bovinos, e o `CattleCardComponent` receberia o estado como um parâmetro.

### Tipos de Gerenciamento de Estado

O gerenciamento de estado pode ser categorizado de várias formas:

- **Estado Interno do Composable (remember / mutableStateOf):** Para estados que são relevantes apenas para um Composable específico e não precisam ser compartilhados ou observados por outros. Este estado é perdido em caso de recriação da Activity.

- **Estado Interno Persistente do Composable (rememberSaveable / mutableStateOf):** Para estados que são relevantes para um Composable específico e precisam sobreviver à recriação da Activity (ex., rotação de tela).

- **Estado Elevado (State Hoisting):** Quando o estado é movido para um Composable ancestral ou um ViewModel para ser compartilhado e gerenciado centralmente.

- **Estado Compartilhado (Shared State):** Quando múltiplos Composables precisam acessar e/ou modificar o mesmo pedaço de estado. Isso é geralmente alcançado através de State Hoisting para um ViewModel ou um Composable pai.

## 3. Composables de Layout e Modifiers

O Jetpack Compose oferece uma série de Composables para organizar elementos na tela e Modifiers para personalizar sua aparência e comportamento.

### Text
O Composable mais adequado para exibir dados textuais simples, como o nome de um animal ou um preço.

```kotlin
Text(text = "Nome do Animal: Bovino 123")
```

### Row
Usado para organizar elementos horizontalmente, um ao lado do outro.

```kotlin
Row {
    Text("Brinco: 001")
    Text("Data Nasc.: 01/01/2023")
}
```

### Column
Usado para organizar elementos verticalmente, um abaixo do outro. Ideal para listas de informações ou para o `CattleCardComponent`.

```kotlin
Column {
    Text("ID: 456")
    Text("Raça: Nelore")
    Text("Status: Saudável")
}
```

### Box
Permite empilhar elementos um sobre o outro (como frames em uma caixa) ou usar um Composable como plano de fundo para outro. É útil para criar efeitos de contêiner com fundos personalizados.

```kotlin
Box(
    modifier = Modifier
        .background(Color.Red)
        .padding(8.dp)
) {
    Text("ATENÇÃO!", color = Color.White)
}
```

### Modifier
Uma classe essencial no Compose que permite encadear diversas propriedades visuais e de layout em um Composable. Ele não é um Composable em si, mas um objeto que você passa para os Composables para modificá-los.

```kotlin
Text(
    text = "Meu Texto",
    modifier = Modifier
        .padding(16.dp) // Espaçamento interno
        .background(Color.Blue) // Cor de fundo
        .fillMaxWidth() // Ocupa a largura máxima
)
```

`Modifier` é a classe principal que provê a funcionalidade para definir atributos visuais como margens (`padding`), preenchimento (`padding`), cor de fundo (`background`), tamanho (`size`), etc.

## 4. Temas e Material Design

O Jetpack Compose, em conjunto com o Material Design, oferece ferramentas poderosas para garantir consistência visual e aderência às diretrizes de design modernas.

### MaterialTheme
É o recurso do Jetpack Compose amplamente utilizado que abrange um conjunto de componentes e diretrizes para construir interfaces coesas. Ele define a tipografia, cores e formas que serão usadas em toda a aplicação, garantindo uma aparência unificada.

```kotlin
@Composable
fun MyAppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colors = MyColors, // Cores personalizadas
        typography = MyTypography, // Tipografia personalizada
        shapes = MyShapes, // Formas personalizadas
        content = content
    )
}
```

### Personalização de Cores (Ex: Top App Bar)
Para mudar dinamicamente a cor de fundo de um componente como a Top App Bar, você geralmente usa a propriedade `containerColor` (ou `backgroundColor` em versões mais antigas ou dependendo do Composable específico).

```kotlin
TopAppBar(
    title = { Text("Gerenciamento de Bovinos") },
    colors = TopAppBarDefaults.topAppBarColors(
        containerColor = if (hasAlert) Color.Red else MaterialTheme.colorScheme.primary // Vermelho para alerta
    )
)
```

Dentro do `MaterialTheme`, você pode definir paletas de cores, tipografias e formas que serão automaticamente aplicadas aos componentes do Material Design.

## 5. Escopos no Compose

No Jetpack Compose, "escopos" referem-se aos receptores implícitos de funções lambda que são passadas para certos Composables. Eles permitem que você acesse propriedades ou funções específicas do contexto do Composable pai.

Por exemplo:

**ColumnScope:** Quando você coloca conteúdo dentro de um `Column`, a lambda que define esse conteúdo tem um receptor `ColumnScope`. Isso permite que você use modificadores específicos de Column, como `align(Alignment.CenterHorizontally)`.

```kotlin
Column { // This lambda has ColumnScope
    Text("Item 1", modifier = Modifier.align(Alignment.CenterHorizontally))
    Text("Item 2")
}
```

**RowScope:** Similarmente, para `Row`, a lambda tem um receptor `RowScope`, permitindo modificadores como `weight(1f)` para distribuir o espaço horizontalmente.

```kotlin
Row { // This lambda has RowScope
    Text("Item 1", modifier = Modifier.weight(1f))
    Text("Item 2", modifier = Modifier.weight(1f))
}
```

Esses escopos garantem que você só possa usar modificadores que fazem sentido no contexto do layout pai, melhorando a segurança e a clareza do código.

## 6. Estrutura de Navegação no Jetpack Compose

A navegação no Jetpack Compose é gerenciada principalmente pela biblioteca Navigation-Compose, que faz parte do Android Jetpack. Ela utiliza um grafo de navegação para definir os destinos e as rotas entre eles.

Os componentes chave para a navegação são:

- **NavController:** É o controlador central de navegação. Ele é responsável por navegar entre os destinos, gerenciar a back stack e passar argumentos. Você o obtém usando `rememberNavController()`.

- **NavHost:** Um Composable que hospeda o grafo de navegação. Ele é responsável por exibir o Composable correto para o destino atual.

- **composable:** Uma função que define um destino no grafo de navegação, associando uma rota (string) a um Composable.

### Estrutura Básica:

```kotlin
@Composable
fun MyApp() {
    val navController = rememberNavController() // 1. Obter o NavController

    NavHost(
        navController = navController, // 2. Passar o NavController para o NavHost
        startDestination = "list_screen" // 3. Definir a tela inicial
    ) {
        // 4. Definir os destinos usando 'composable'
        composable("list_screen") {
            // Conteúdo da tela de listagem
            ListScreen(onNavigateToDetail = { navController.navigate("detail_screen/$it") })
        }
        composable("detail_screen/{itemId}") { backStackEntry ->
            // Conteúdo da tela de detalhes, acessando argumentos
            val itemId = backStackEntry.arguments?.getString("itemId")
            DetailScreen(itemId = itemId)
        }
    }
}

@Composable
fun ListScreen(onNavigateToDetail: (String) -> Unit) {
    Column {
        Text("Tela de Listagem")
        Button(onClick = { onNavigateToDetail("bovino001") }) {
            Text("Ver Detalhes do Bovino 001")
        }
    }
}

@Composable
fun DetailScreen(itemId: String?) {
    Column {
        Text("Tela de Detalhes para: $itemId")
        // Exibir detalhes do bovino
    }
}
```

**Rotas:** São strings que identificam cada tela ("list_screen", "detail_screen").

**Argumentos:** Podem ser passados entre telas, como "{itemId}" no exemplo.

**Navegação:** Usa-se `navController.navigate("route_name")` para ir para uma nova tela.

**Voltar:** O NavController gerencia a pilha de navegação, permitindo que o usuário volte para a tela anterior com o botão "Voltar" do sistema.

Este documento de revisão serve como um guia abrangente para os conceitos essenciais do Jetpack Compose, preparando você para desenvolver aplicações Android modernas e reativas.