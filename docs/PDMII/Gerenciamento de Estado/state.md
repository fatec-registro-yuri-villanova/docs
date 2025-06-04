# Explicação do Código CounterScreen em Jetpack Compose

Este código demonstra três padrões diferentes de gerenciamento de estado no Jetpack Compose, um framework moderno para construção de UIs no Android.

## Visão Geral

O código cria uma tela simples com três botões que funcionam como contadores, cada um ilustrando uma abordagem diferente para lidar com estado:

1. Estado básico com `remember`
2. Estado persistente com `rememberSaveable`
3. Elevação de estado (State Hoisting)

## Estrutura Principal

```kotlin
@Composable
fun CounterScreen() {
    Column(
        Modifier
            .fillMaxSize()
            .padding(16.dp),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        // Implementações dos contadores aqui
    }
}
```

- Column: Organiza os elementos verticalmente
- Modifier.fillMaxSize(): Ocupa toda a tela disponível
- padding(16.dp): Adiciona espaçamento interno
- Arrangement.Center e Alignment.CenterHorizontally: Centraliza os elementos

## 1. Estado Básico com `remember`
```kotlin
val count = remember { mutableIntStateOf(0) }
Button(onClick = { count.intValue++ }) {
    Text("Count: ${count.intValue}")
}
```
- `remember`: Mantém o estado durante recomposições, mas é destruído quando a tela é removida
- `mutableIntStateOf(0)`: Cria um estado mutável inicializado com 0
- O botão incrementa o valor e exibe o contador atual

## 2.  Estado Persistente com rememberSaveable
kotlin
Copy
val countSaveable = rememberSaveable { mutableIntStateOf(0) }
Button(onClick = { countSaveable.intValue++ }) {
    Text("Count: ${countSaveable.intValue}")
}
rememberSaveable: Similar ao remember, mas sobrevive a mudanças de configuração (como rotação de tela)

Ideal para manter estado entre recreações de atividade

3. Elevação de Estado (State Hoisting)
kotlin
Copy
var countLifting by rememberSaveable { mutableIntStateOf(0) }
Counter(countLifting, onIncrement = { countLifting++ })
kotlin
Copy
@Composable
fun Counter(count: Int, onIncrement: () -> Unit) {
    Button(onClick = onIncrement) {
        Text("Count: $count")
    }
}
State Hoisting: Padrão onde o estado é movido para o componente pai

Counter é um componente stateless (sem estado próprio)

Recebe o valor atual e uma lambda para notificar incrementos

Vantagens:

Reutilização do componente

Estado controlado pelo pai

Fácil teste isolado

Conceitos Importantes
Estado em Compose: Dados que podem mudar e afetam a UI

Recomposição: Processo de redesenhar a UI quando o estado muda

State Hoisting: Padrão recomendado para componentes reutilizáveis

Quando Usar Cada Abordagem
remember: Estado temporário que não precisa persistir

rememberSaveable: Estado que deve sobreviver a mudanças de configuração

State Hoisting: Para componentes reutilizáveis ou quando o estado precisa ser compartilhado


