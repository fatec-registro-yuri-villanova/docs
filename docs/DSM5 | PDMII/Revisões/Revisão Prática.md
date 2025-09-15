# Revisão Prática: PDMII e LDDM


## Repo
```
https://github.com/fatec-registro-yuri-villanova/bovino
```
## Estrutura do Projeto

Imagine a seguinte estrutura de arquivos Kotlin em um projeto Android Studio:

```
app/
├── src/
│   └── main/
│       └── java/
│           └── com/
│               └── example/
│                   └── bovinehealthmanager/
│                       ├── MainActivity.kt
│                       ├── ui/
│                       │   ├── theme/
│                       │   │   ├── Theme.kt
│                       │   │   └── Color.kt
│                       │   ├── screens/
│                       │   │   ├── BovineListScreen.kt
│                       │   │   └── BovineDetailScreen.kt
│                       │   └── components/
│                       │       └── BovineCard.kt
│                       └── data/
│                           └── Bovine.kt
```

## Conceitos Aplicados

### 1. MainActivity.kt - Ponto de Entrada e Navegação

Este arquivo conteria a Activity principal e configuraria o NavController e o NavHost para gerenciar a navegação entre as telas.

```kotlin
// MainActivity.kt
package com.fatec.bovino

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Surface
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import com.fatec.bovino.ui.screens.BovineDetailScreen
import com.fatec.bovino.ui.screens.BovineListScreen
import com.fatec.bovino.ui.theme.BovinoTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContent {
            BovinoTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    BovineAppNavigation() // Componente de navegação
                }

            }
        }
    }
}

@Composable
fun BovineAppNavigation() {
    val navController = rememberNavController() // O NavController lembra o estado da navegação

    NavHost(navController = navController, startDestination = "bovine_list") {
        // Define a rota para a tela de listagem de bovinos
        composable("bovine_list") {
            BovineListScreen(
                onNavigateToDetail = { bovineId ->
                    navController.navigate("bovine_detail/$bovineId") // Navega para detalhes
                }
            )
        }
        // Define a rota para a tela de detalhes do bovino, com um argumento
        composable("bovine_detail/{bovineId}") { backStackEntry ->
            val bovineId = backStackEntry.arguments?.getString("bovineId")
            BovineDetailScreen(bovineId = bovineId) // Passa o ID para a tela de detalhes
        }
    }
}
```

### 2. Bovine.kt - Modelo de Dados

Define a estrutura de dados para um bovino.

```kotlin
// data/Bovine.kt
package com.fatec.bovino.data

data class Bovine(
    val id: String,
    var name: String,
    var breed: String,
    var healthStatus: HealthStatus,
    var lastVaccinationDate: String
)

enum class HealthStatus {
    SAUDAVEL, // SAUDÁVEL
    RISCO, // EM RISCO
    VACINACAO_PENDENTE // VACINAÇÃO PENDENTE
}
```

### 3. BovineListScreen.kt - Tela de Listagem de Bovinos

Esta tela demonstra o gerenciamento de estado (lista de bovinos), Column para organização vertical, Row e Text dentro do BovineCard, e Modifier.

```kotlin
// ui/screens/BovineListScreen.kt
package com.fatec.bovino.ui.screens

import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material3.*
import androidx.compose.runtime.* // Importa remember e mutableStateOf
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import com.fatec.bovino.data.Bovine
import com.fatec.bovino.data.HealthStatus
import com.fatec.bovino.ui.components.BovineCard

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun BovineListScreen(onNavigateToDetail: (String) -> Unit) {
    // Gerenciamento de estado para a lista de bovinos
    // Usando 'remember' para manter a lista entre recomposições
    // Usando 'mutableStateOf' para tornar a lista observável
    // O 'by' permite acesso direto ao valor da lista sem .value
    var bovines by remember {
        mutableStateOf(
            listOf(
                Bovine("001", "Mimosa", "Jersey", HealthStatus.SAUDAVEL, "2024-05-01"),
                Bovine("002", "Estrela", "Holandesa", HealthStatus.VACINACAO_PENDENTE, "2023-11-15"),
                Bovine("003", "Fofinha", "Nelore", HealthStatus.RISCO, "2024-03-20"),
                Bovine("004", "Pintada", "Angus", HealthStatus.SAUDAVEL, "2024-04-10")
            )
        )
    }

    // Estado para determinar se há algum alerta geral (para Top App Bar)
    val hasAlert by remember {
        derivedStateOf {
            bovines.any { it.healthStatus == HealthStatus.RISCO || it.healthStatus == HealthStatus.VACINACAO_PENDENTE }
        }
    }

    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Gerenciador de Bovinos") },
                // A cor da Top App Bar muda dinamicamente com base no estado 'hasAlert'
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = if (hasAlert) Color(0xFFEF5350) else MaterialTheme.colorScheme.primary // Vermelho para alerta
                )
            )
        }
    ) { paddingValues ->
        // LazyColumn é eficiente para listas longas, renderizando apenas os itens visíveis
        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues) // Aplica o padding da Scaffold
                .padding(horizontal = 16.dp, vertical = 8.dp) // Modificador para padding externo
        ) {
            items(bovines) { bovine ->
                // BovineCard é um Composable reutilizável
                BovineCard(bovine = bovine, onClick = { onNavigateToDetail(bovine.id) })
                Spacer(modifier = Modifier.height(8.dp)) // Espaçamento entre os cards
            }
        }
    }
}
```

### 4. BovineCard.kt - Componente Reutilizável (State Hoisting)

Este componente demonstra Column, Row, Text, Modifier e o conceito de State Hoisting, onde o estado (o objeto Bovine) é passado de cima para baixo.

```kotlin
// ui/components/BovineCard.kt
package com.fatec.bovino.ui.components

import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.Card
import androidx.compose.material3.CardDefaults
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import com.fatec.bovino.data.Bovine
import com.fatec.bovino.data.HealthStatus

@Composable
fun BovineCard(bovine: Bovine, onClick: () -> Unit) {
    // Determina a cor de fundo do card com base no status de saúde
    val cardBackgroundColor = when (bovine.healthStatus) {
        HealthStatus.SAUDAVEL -> Color(0xFFE8F5E9) // Verde claro
        HealthStatus.RISCO -> Color(0xFFFFCC80) // Laranja claro
        HealthStatus.VACINACAO_PENDENTE -> Color(0xFFFFCDD2) // Vermelho claro
    }

    Card(
        modifier = Modifier
            .fillMaxWidth()
            .clickable(onClick = onClick), // Torna o card clicável
        shape = RoundedCornerShape(8.dp), // Cantos arredondados
        colors = CardDefaults.cardColors(containerColor = cardBackgroundColor), // Cor de fundo do card
        elevation = CardDefaults.cardElevation(defaultElevation = 4.dp) // Sombra
    ) {
        // Column para organizar as informações verticalmente
        Column(
            modifier = Modifier.padding(16.dp) // Padding interno
        ) {
            // Row para alinhar ID e Status horizontalmente
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.SpaceBetween, // Espaçamento entre os elementos
                verticalAlignment = Alignment.CenterVertically
            ) {
                Text(
                    text = "ID: ${bovine.id}",
                    style = MaterialTheme.typography.titleMedium // Estilo de texto do Material Theme
                )
                // Box para "encaixotar" o status com um fundo colorido
                Box(
                    modifier = Modifier
                        .background(
                            color = when (bovine.healthStatus) {
                                HealthStatus.SAUDAVEL -> Color.Green.copy(alpha = 0.7f)
                                HealthStatus.RISCO -> Color.Yellow .copy(alpha = 0.7f)
                                HealthStatus.VACINACAO_PENDENTE -> Color.Red.copy(alpha = 0.7f)
                            },
                            shape = RoundedCornerShape(4.dp) // Cantos arredondados para o Box
                        )
                        .padding(horizontal = 6.dp, vertical = 2.dp) // Padding interno do Box
                ) {
                    Text(
                        text = bovine.healthStatus.name.replace("_", " "),
                        color = Color.White,
                        style = MaterialTheme.typography.labelSmall
                    )
                }
            }
            Spacer(modifier = Modifier.height(4.dp)) // Espaçamento vertical
            Text(
                text = "Nome: ${bovine.name}",
                style = MaterialTheme.typography.bodyMedium
            )
            Text(
                text = "Raça: ${bovine.breed}",
                style = MaterialTheme.typography.bodySmall
            )
            Text(
                text = "Última Vacinação: ${bovine.lastVaccinationDate}",
                style = MaterialTheme.typography.bodySmall
            )
        }
    }
}
```

### 5. BovineDetailScreen.kt - Tela de Detalhes do Bovino

Esta tela demonstra como receber argumentos de navegação e pode incluir mais gerenciamento de estado local.

```kotlin
// ui/screens/BovineDetailScreen.kt
package com.fatec.bovino.ui.screens

import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.unit.dp
import com.fatec.bovino.data.Bovine
import com.fatec.bovino.data.HealthStatus

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun BovineDetailScreen(bovineId: String?) {
    // Simula a busca do bovino por ID (em um app real, viria de um ViewModel/repositório)
    val bovine = remember(bovineId) { // remember o objeto Bovine baseado no ID
        // Exemplo de dados mockados
        when (bovineId) {
            "001" -> Bovine("001", "Mimosa", "Jersey", HealthStatus.SAUDAVEL, "2024-05-01")
            "002" -> Bovine("002", "Estrela", "Holandesa", HealthStatus.VACINACAO_PENDENTE, "2023-11-15")
            "003" -> Bovine("003", "Fofinha", "Nelore", HealthStatus.RISCO, "2024-03-20")
            "004" -> Bovine("004", "Pintada", "Angus", HealthStatus.SAUDAVEL, "2024-04-10")
            else -> null
        }
    }

    // Estado local para o status de saúde, permitindo alteração na tela de detalhes
    // Demonstra o uso de 'by' e 'remember' para estado mutável
    var currentHealthStatus by remember { mutableStateOf(bovine?.healthStatus ?: HealthStatus.SAUDAVEL) }

    Scaffold(
        topBar = {
            TopAppBar(title = { Text("Detalhes do Bovino: $bovineId") })
        }
    ) { paddingValues ->
        if (bovine != null) {
            Column(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(paddingValues)
                    .padding(16.dp),
                horizontalAlignment = Alignment.CenterHorizontally,
                verticalArrangement = Arrangement.Center
            ) {
                Text(text = "ID: ${bovine.id}", style = MaterialTheme.typography.headlineMedium)
                Spacer(modifier = Modifier.height(8.dp))
                Text(text = "Nome: ${bovine.name}", style = MaterialTheme.typography.titleLarge)
                Text(text = "Raça: ${bovine.breed}", style = MaterialTheme.typography.titleMedium)
                Text(text = "Última Vacinação: ${bovine.lastVaccinationDate}", style = MaterialTheme.typography.bodyLarge)
                Spacer(modifier = Modifier.height(16.dp))

                // Exibe o status atual com base no estado local
                Text(text = "Status de Saúde: ${currentHealthStatus.name.replace("_", " ")}",
                    style = MaterialTheme.typography.titleMedium,
                    color = when (currentHealthStatus) {
                        HealthStatus.SAUDAVEL -> Color.Green
                        HealthStatus.RISCO -> Color.Yellow
                        HealthStatus.VACINACAO_PENDENTE -> Color.Red
                    }
                )
                Spacer(modifier = Modifier.height(16.dp))

                // Botões para mudar o status (demonstração de gerenciamento de estado)
                Row(
                    horizontalArrangement = Arrangement.spacedBy(8.dp)
                ) {
                    Button(onClick = { currentHealthStatus = HealthStatus.SAUDAVEL }) {
                        Text("Saudável")
                    }
                    Button(onClick = { currentHealthStatus = HealthStatus.RISCO }) {
                        Text("Em Risco")
                    }
                    Button(onClick = { currentHealthStatus = HealthStatus.VACINACAO_PENDENTE }) {
                        Text("Vacinar")
                    }
                }
            }
        } else {
            Box(
                modifier = Modifier.fillMaxSize(),
                contentAlignment = Alignment.Center
            ) {
                Text("Bovino não encontrado.")
            }
        }
    }
}
```

### 6. Theme.kt e Color.kt - Tematização do Material Design

Estes arquivos definiriam as cores e a tipografia para o MaterialTheme da aplicação.

```kotlin
// ui/theme/Theme.kt
package com.fatec.bovino.ui.theme

import android.os.Build
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.darkColorScheme
import androidx.compose.material3.dynamicDarkColorScheme
import androidx.compose.material3.dynamicLightColorScheme
import androidx.compose.material3.lightColorScheme
import androidx.compose.runtime.Composable
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.platform.LocalContext

private val DarkColorScheme = darkColorScheme(
    primary = Purple80,
    secondary = PurpleGrey80,
    tertiary = Pink80,
    background = Color(0xFF1C1B1F),
    surface = Color(0xFF1C1B1F),
    onPrimary = Color.White,
    onSecondary = Color.White,
    onTertiary = Color.White,
    onBackground = Color(0xFFE6E1E5),
    onSurface = Color(0xFFE6E1E5),
)

private val LightColorScheme = lightColorScheme(
    primary = Purple40,
    secondary = PurpleGrey40,
    tertiary = Pink40,
    background = Color(0xFFFFFBFE),
    surface = Color(0xFFFFFBFE),
    onPrimary = Color.White,
    onSecondary = Color.White,
    onTertiary = Color.White,
    onBackground = Color(0xFF1C1B1F),
    onSurface = Color(0xFF1C1B1F),
)

@Composable
fun BovinoTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    // Dynamic color is available on Android 12+
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }

        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}
```