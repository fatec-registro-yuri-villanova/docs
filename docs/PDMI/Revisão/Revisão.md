# Revisão Geral

### 1. Criação do Projeto com Expo

```bash
# criar diretamente com o template TypeScript:
npx create-expo-app CalculadoraIR --template blank-typescript

# Navegar para o diretório do projeto
cd CalculadoraIR
```

### 2. Configuração do Expo (app.json/app.json)

```json
{
  "expo": {
    "name": "CalculadoraIR",
    "slug": "CalculadoraIR",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "userInterfaceStyle": "light",
    "newArchEnabled": true,
    "splash": {
      "image": "./assets/splash-icon.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "ios": {
      "supportsTablet": true
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#ffffff"
      },
      "edgeToEdgeEnabled": true
    },
    "web": {
      "favicon": "./assets/favicon.png"
    }
  }
}
```

### 3. Estrutura do Projeto Expo

```
CalculadoraIR/
├── src/
│   ├── components/
│   │   ├── Button/
│   │   │   ├── index.tsx
│   │   │   └── styles.ts
│   │   ├── Input/
│   │   │   ├── index.tsx
│   │   │   └── styles.ts
│   │   ├── ResultCard/
│   │   │   ├── index.tsx
│   │   │   └── styles.ts
│   │   └── TaxBracketItem/
│   │       ├── index.tsx
│   │       └── styles.ts
│   ├── screens/
│   │   └── Home/
│   │       ├── index.tsx
│   │       └── styles.ts
│   ├── utils/
│   │   └── irCalculator.ts
│   └── types/
│       └── index.ts
├── assets/
│   ├── icon.png
│   ├── splash.png
│   ├── adaptive-icon.png
│   └── favicon.png
├── App.tsx
├── app.json
└── package.json
```

### 4. Tipos TypeScript (src/types/index.ts)

```typescript
// Definição de tipos para melhor tipagem e organização
export interface IRResult {
  shouldPay: boolean;
  taxAmount: number;
  netSalary: number;
  taxRate: number;
  taxBracket: string;
}

export interface TaxBracket {
  min: number;
  max: number | null;
  rate: number;
  deduction: number;
  description: string;
}

export interface InputProps {
  value: string;
  onChangeText: (text: string) => void;
  placeholder: string;
  keyboardType?: 'numeric' | 'default';
}

export interface ButtonProps {
  title: string;
  onPress: () => void;
  disabled?: boolean;
}

export interface ResultCardProps {
  result: IRResult | null;
}

export interface TaxBracketItemProps {
  item: TaxBracket;
  index: number;
}
```

**Conceitos Aplicados:**

- **Interfaces TypeScript**: Definem contratos para objetos, garantindo tipagem forte
- **Union Types**: `number | null` permite valores número ou nulo
- **Props Interfaces**: Tipagem para propriedades de componentes React
- **Optional Properties**: `?` indica propriedades opcionais

### 5. Utilitário de Cálculo (src/utils/irCalculator.ts)

```typescript
import { IRResult, TaxBracket } from '../types';

// Tabela do Imposto de Renda 2024 - exportada para uso na FlatList
export const TAX_BRACKETS: TaxBracket[] = [
  {
    min: 0,
    max: 2112.00,
    rate: 0,
    deduction: 0,
    description: 'Isento'
  },
  {
    min: 2112.01,
    max: 2826.65,
    rate: 0.075,
    deduction: 158.40,
    description: '7,5%'
  },
  {
    min: 2826.66,
    max: 3751.05,
    rate: 0.15,
    deduction: 370.40,
    description: '15%'
  },
  {
    min: 3751.06,
    max: 4664.68,
    rate: 0.225,
    deduction: 651.73,
    description: '22,5%'
  },
  {
    min: 4664.69,
    max: null,
    rate: 0.275,
    deduction: 884.96,
    description: '27,5%'
  }
];

const TAX_BRACKETS_INTERNAL: TaxBracket[] = TAX_BRACKETS;

/**
 * Calcula o Imposto de Renda baseado no salário bruto
 * @param grossSalary - Salário bruto mensal
 * @returns Objeto com informações do cálculo
 */
export const calculateIR = (grossSalary: number): IRResult => {
  // Encontra a faixa de tributação apropriada
  const bracket = TAX_BRACKETS_INTERNAL.find(bracket => {
    if (bracket.max === null) {
      return grossSalary >= bracket.min;
    }
    return grossSalary >= bracket.min && grossSalary <= bracket.max;
  });

  if (!bracket) {
    throw new Error('Faixa de tributação não encontrada');
  }

  // Calcula o imposto devido
  const taxAmount = bracket.rate === 0 
    ? 0 
    : (grossSalary * bracket.rate) - bracket.deduction;

  // Calcula o salário líquido
  const netSalary = grossSalary - Math.max(0, taxAmount);

  return {
    shouldPay: taxAmount > 0,
    taxAmount: Math.max(0, taxAmount),
    netSalary,
    taxRate: bracket.rate * 100,
    taxBracket: bracket.description
  };
};

/**
 * Formata valor para moeda brasileira
 * @param value - Valor numérico
 * @returns String formatada como moeda
 */
export const formatCurrency = (value: number): string => {
  return new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  }).format(value);
};

/**
 * Valida se o valor inserido é um número válido
 * @param value - String do input
 * @returns Boolean indicando se é válido
 */
export const isValidSalary = (value: string): boolean => {
  const numValue = parseFloat(value.replace(',', '.'));
  return !isNaN(numValue) && numValue > 0;
};
```

**Conceitos Aplicados:**

- **Pure Functions**: Funções que não causam efeitos colaterais
- **Array.find()**: Método para encontrar elemento em array
- **Ternary Operator**: Operador condicional `? :`
- **Math.max()**: Função para obter o maior valor
- **Error Handling**: Tratamento de erros com `throw`
- **JSDoc**: Documentação de funções
- **Intl.NumberFormat**: API de internacionalização para formatação

### 6. Componente Input (src/components/Input/index.tsx)

```typescript
import React from 'react';
import { TextInput } from 'react-native';
import { InputProps } from '../../types';
import { styles } from './styles';

/**
 * Componente de input reutilizável
 * Demonstra componentização e reutilização de código
 */
export const Input: React.FC<InputProps> = ({
  value,
  onChangeText,
  placeholder,
  keyboardType = 'default'
}) => {
  return (
    <TextInput
      style={styles.input}
      value={value}
      onChangeText={onChangeText}
      placeholder={placeholder}
      keyboardType={keyboardType}
      placeholderTextColor="#999"
    />
  );
};
```

**Conceitos Aplicados:**

- **Functional Component**: Componente baseado em função
- **React.FC**: Tipo TypeScript para componentes funcionais
- **Destructuring**: Desestruturação das props
- **Default Parameters**: Valor padrão para parâmetros

### 7. Estilos do Input (src/components/Input/styles.ts)

```typescript
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  input: {
    height: 50,
    borderWidth: 1,
    borderColor: '#ddd',
    borderRadius: 8,
    paddingHorizontal: 15,
    fontSize: 16,
    backgroundColor: '#fff',
    marginBottom: 15,
    // Sombra para iOS
    shadowColor: '#000',
    shadowOffset: {
      width: 0,
      height: 1,
    },
    shadowOpacity: 0.1,
    shadowRadius: 2,
    // Elevação para Android
    elevation: 2,
  },
});
```

**Conceitos Aplicados:**

- **StyleSheet.create**: Otimização de estilos no React Native
- **Cross-platform Styling**: Estilos que funcionam em iOS e Android
- **Shadow vs Elevation**: Diferenças entre plataformas para sombras

### 8. Componente Button (src/components/Button/index.tsx)

```typescript
import React from 'react';
import { TouchableOpacity, Text } from 'react-native';
import { ButtonProps } from '../../types';
import { styles } from './styles';

/**
 * Componente de botão personalizado
 * Demonstra estado condicional e acessibilidade
 */
export const Button: React.FC<ButtonProps> = ({
  title,
  onPress,
  disabled = false
}) => {
  return (
    <TouchableOpacity
      style={[
        styles.button,
        disabled && styles.buttonDisabled
      ]}
      onPress={onPress}
      disabled={disabled}
      activeOpacity={0.7}
      accessibilityRole="button"
      accessibilityLabel={title}
    >
      <Text style={[
        styles.buttonText,
        disabled && styles.buttonTextDisabled
      ]}>
        {title}
      </Text>
    </TouchableOpacity>
  );
};
```

**Conceitos Aplicados:**

- **Conditional Styling**: Estilos condicionais com array
- **Accessibility**: Propriedades de acessibilidade
- **TouchableOpacity**: Componente para interações touch
- **Active Opacity**: Feedback visual ao toque

### 9. Estilos do Button (src/components/Button/styles.ts)

```typescript
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  button: {
    backgroundColor: '#007AFF',
    paddingVertical: 15,
    paddingHorizontal: 30,
    borderRadius: 8,
    alignItems: 'center',
    justifyContent: 'center',
    marginVertical: 10,
    shadowColor: '#000',
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
    elevation: 5,
  },
  buttonDisabled: {
    backgroundColor: '#ccc',
    shadowOpacity: 0,
    elevation: 0,
  },
  buttonText: {
    color: '#fff',
    fontSize: 16,
    fontWeight: 'bold',
  },
  buttonTextDisabled: {
    color: '#999',
  },
});
```

### 10. Componente TaxBracketItem (src/components/TaxBracketItem/index.tsx)

```typescript
import React from 'react';
import { View, Text } from 'react-native';
import { TaxBracketItemProps } from '../../types';
import { formatCurrency } from '../../utils/irCalculator';
import { styles } from './styles';

/**
 * Componente para exibir uma faixa de tributação
 * Demonstra renderização de item de lista personalizado
 */
export const TaxBracketItem: React.FC<TaxBracketItemProps> = ({ item, index }) => {
  /**
   * Formata o range de valores da faixa
   */
  const formatRange = (): string => {
    if (item.max === null) {
      return `Acima de ${formatCurrency(item.min)}`;
    }
    
    if (item.min === 0) {
      return `Até ${formatCurrency(item.max)}`;
    }
    
    return `${formatCurrency(item.min)} - ${formatCurrency(item.max)}`;
  };

  /**
   * Retorna a cor baseada na alíquota
   */
  const getBracketColor = (): string => {
    if (item.rate === 0) return '#28a745'; // Verde para isento
    if (item.rate <= 0.075) return '#ffc107'; // Amarelo para 7,5%
    if (item.rate <= 0.15) return '#fd7e14'; // Laranja para 15%
    if (item.rate <= 0.225) return '#dc3545'; // Vermelho para 22,5%
    return '#6f42c1'; // Roxo para 27,5%
  };

  return (
    <View style={[styles.container, { borderLeftColor: getBracketColor() }]}>
      <View style={styles.header}>
        <Text style={styles.bracketNumber}>Faixa {index + 1}</Text>
        <Text style={[styles.rate, { color: getBracketColor() }]}>
          {item.description}
        </Text>
      </View>
      
      <Text style={styles.range}>{formatRange()}</Text>
      
      {item.rate > 0 && (
        <Text style={styles.deduction}>
          Dedução: {formatCurrency(item.deduction)}
        </Text>
      )}
    </View>
  );
};
```

**Conceitos Aplicados:**

- **Conditional Rendering**: Renderiza dedução apenas se rate > 0
- **Inline Functions**: Funções auxiliares dentro do componente
- **Dynamic Styling**: Cores baseadas nos valores das alíquotas
- **String Interpolation**: Template literals para formatação

### 11. Estilos do TaxBracketItem (src/components/TaxBracketItem/styles.ts)

```typescript
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  container: {
    backgroundColor: '#fff',
    borderRadius: 8,
    padding: 15,
    marginBottom: 10,
    borderLeftWidth: 4,
    shadowColor: '#000',
    shadowOffset: {
      width: 0,
      height: 1,
    },
    shadowOpacity: 0.1,
    shadowRadius: 2,
    elevation: 2,
  },
  header: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 8,
  },
  bracketNumber: {
    fontSize: 14,
    fontWeight: '600',
    color: '#666',
  },
  rate: {
    fontSize: 16,
    fontWeight: 'bold',
  },
  range: {
    fontSize: 15,
    color: '#333',
    fontWeight: '500',
    marginBottom: 4,
  },
  deduction: {
    fontSize: 13,
    color: '#888',
    fontStyle: 'italic',
  },
});
```

### 12. Componente ResultCard (src/components/ResultCard/index.tsx)

```typescript
import React from 'react';
import { View, Text } from 'react-native';
import { ResultCardProps } from '../../types';
import { formatCurrency } from '../../utils/irCalculator';
import { styles } from './styles';

/**
 * Componente para exibir resultados do cálculo
 * Demonstra renderização condicional e formatação de dados
 */
export const ResultCard: React.FC<ResultCardProps> = ({ result }) => {
  // Renderização condicional - não renderiza se não há resultado
  if (!result) return null;

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Resultado do Cálculo</Text>
      
      <View style={styles.row}>
        <Text style={styles.label}>Status:</Text>
        <Text style={[
          styles.value,
          result.shouldPay ? styles.shouldPay : styles.exempt
        ]}>
          {result.shouldPay ? 'Deve pagar IR' : 'Isento de IR'}
        </Text>
      </View>

      <View style={styles.row}>
        <Text style={styles.label}>Faixa:</Text>
        <Text style={styles.value}>{result.taxBracket}</Text>
      </View>

      {result.shouldPay && (
        <>
          <View style={styles.row}>
            <Text style={styles.label}>Imposto devido:</Text>
            <Text style={styles.value}>
              {formatCurrency(result.taxAmount)}
            </Text>
          </View>

          <View style={styles.row}>
            <Text style={styles.label}>Alíquota:</Text>
            <Text style={styles.value}>{result.taxRate.toFixed(1)}%</Text>
          </View>
        </>
      )}

      <View style={[styles.row, styles.highlight]}>
        <Text style={styles.labelHighlight}>Salário líquido:</Text>
        <Text style={styles.valueHighlight}>
          {formatCurrency(result.netSalary)}
        </Text>
      </View>
    </View>
  );
};
```

**Conceitos Aplicados:**

- **Conditional Rendering**: `if (!result) return null`
- **Fragment Shorthand**: `<>...</>` para agrupar elementos
- **Array Styling**: Combinação de estilos com arrays
- **Template Literals**: Formatação de strings
- **Early Return**: Retorno antecipado para casos especiais

### 13. Estilos do ResultCard (src/components/ResultCard/styles.ts)

```typescript
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  container: {
    backgroundColor: '#fff',
    borderRadius: 12,
    padding: 20,
    marginTop: 20,
    shadowColor: '#000',
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.1,
    shadowRadius: 3.84,
    elevation: 5,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 15,
    textAlign: 'center',
  },
  row: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 10,
    paddingVertical: 5,
  },
  label: {
    fontSize: 14,
    color: '#666',
    fontWeight: '500',
  },
  value: {
    fontSize: 14,
    color: '#333',
    fontWeight: 'bold',
  },
  shouldPay: {
    color: '#ff4444',
  },
  exempt: {
    color: '#00aa00',
  },
  highlight: {
    backgroundColor: '#f0f8ff',
    borderRadius: 8,
    padding: 10,
    marginTop: 10,
  },
  labelHighlight: {
    fontSize: 16,
    color: '#007AFF',
    fontWeight: 'bold',
  },
  valueHighlight: {
    fontSize: 16,
    color: '#007AFF',
    fontWeight: 'bold',
  },
});
```

### 14. Tela Principal com FlatList (src/screens/Home/index.tsx)

```typescript
import React, { useState } from 'react';
import {
  View,
  Text,
  ScrollView,
  KeyboardAvoidingView,
  Platform,
  Alert,
  FlatList,
} from 'react-native';
import { Input } from '../../components/Input';
import { Button } from '../../components/Button';
import { ResultCard } from '../../components/ResultCard';
import { TaxBracketItem } from '../../components/TaxBracketItem';
import {
  calculateIR,
  isValidSalary,
  TAX_BRACKETS,
} from '../../utils/irCalculator';
import { IRResult, TaxBracket } from '../../types';
import { styles } from './styles';

/**
 * Tela principal da aplicação
 * Demonstra gerenciamento de estado e uso de FlatList
 */
export const HomeScreen: React.FC = () => {
  // State para controlar o valor do input
  const [salary, setSalary] = useState<string>('');
  
  // State para armazenar o resultado do cálculo
  const [result, setResult] = useState<IRResult | null>(null);
  
  // State para controlar loading
  const [isCalculating, setIsCalculating] = useState<boolean>(false);

  /**
   * Função para calcular o IR
   * Demonstra validação e tratamento de erros
   */
  const handleCalculate = () => {
    // Validação do input
    if (!salary.trim()) {
      Alert.alert('Erro', 'Por favor, insira um valor para o salário.');
      return;
    }

    if (!isValidSalary(salary)) {
      Alert.alert('Erro', 'Por favor, insira um valor válido para o salário.');
      return;
    }

    setIsCalculating(true);

    try {
      // Converte string para número (aceita vírgula como separador decimal)
      const numericSalary = parseFloat(salary.replace(',', '.'));
      
      // Calcula o IR
      const calculationResult = calculateIR(numericSalary);
      
      // Atualiza o estado com o resultado
      setResult(calculationResult);
    } catch (error) {
      Alert.alert('Erro', 'Ocorreu um erro ao calcular o imposto de renda.');
      console.error('Erro no cálculo:', error);
    } finally {
      setIsCalculating(false);
    }
  };

  /**
   * Função para limpar os campos
   */
  const handleClear = () => {
    setSalary('');
    setResult(null);
  };

  /**
   * Função para formatar input de salário
   * Remove caracteres não numéricos exceto vírgula e ponto
   */
  const handleSalaryChange = (text: string) => {
    // Remove caracteres especiais, mantém apenas números, vírgula e ponto
    const cleanText = text.replace(/[^0-9,.]/g, '');
    setSalary(cleanText);
  };

  /**
   * Renderiza cada item da FlatList
   * Demonstra render function para FlatList
   */
  const renderTaxBracketItem = ({ item, index }: { item: TaxBracket; index: number }) => (
    <TaxBracketItem item={item} index={index} />
  );

  /**
   * KeyExtractor para FlatList
   * Demonstra como criar chaves únicas para itens da lista
   */
  const keyExtractor = (item: TaxBracket, index: number): string => {
    return `bracket-${index}-${item.min}-${item.max}`;
  };

  return (
    <KeyboardAvoidingView
      style={styles.container}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
      keyboardVerticalOffset={Platform.OS === 'ios' ? 64 : 0}
    >
      <ScrollView
        style={styles.scrollView}
        contentContainerStyle={styles.scrollContent}
        keyboardShouldPersistTaps="handled"
      >
        <View style={styles.header}>
          <Text style={styles.title}>Calculadora de IR</Text>
          <Text style={styles.subtitle}>
            Descubra se você precisa pagar Imposto de Renda
          </Text>
        </View>

        <View style={styles.form}>
          <Text style={styles.inputLabel}>Salário Bruto Mensal</Text>
          <Input
            value={salary}
            onChangeText={handleSalaryChange}
            placeholder="Ex: 5000,00"
            keyboardType="numeric"
          />

          <Button
            title={isCalculating ? 'Calculando...' : 'Calcular IR'}
            onPress={handleCalculate}
            disabled={isCalculating || !salary.trim()}
          />

          <Button
            title="Limpar"
            onPress={handleClear}
            disabled={!salary && !result}
          />
        </View>

        <ResultCard result={result} />

        <View style={styles.bracketsSection}>
          <Text style={styles.bracketsTitle}>Faixas de Tributação 2024</Text>
          <Text style={styles.bracketsSubtitle}>
            Tabela oficial do Imposto de Renda
          </Text>
          
          <FlatList
            data={TAX_BRACKETS}
            renderItem={renderTaxBracketItem}
            keyExtractor={keyExtractor}
            scrollEnabled={false}
            showsVerticalScrollIndicator={false}
            ItemSeparatorComponent={() => <View style={styles.separator} />}
          />
        </View>
      </ScrollView>
    </KeyboardAvoidingView>
  );
};
```

**Conceitos Aplicados:**

- **FlatList**: Componente otimizado para renderizar listas
- **renderItem**: Função para renderizar cada item da lista
- **keyExtractor**: Função para gerar chaves únicas para cada item
- **ItemSeparatorComponent**: Componente separador entre itens
- **scrollEnabled={false}**: Desabilita scroll da FlatList dentro do ScrollView
- **Data Prop**: Array de dados passado para a FlatList

### 15. Estilos da Home com FlatList (src/screens/Home/styles.ts)

```typescript
import { StyleSheet } from 'react-native';

export const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  scrollView: {
    flex: 1,
  },
  scrollContent: {
    flexGrow: 1,
    padding: 20,
  },
  header: {
    alignItems: 'center',
    marginBottom: 30,
    marginTop: 20,
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 8,
  },
  subtitle: {
    fontSize: 16,
    color: '#666',
    textAlign: 'center',
    lineHeight: 22,
  },
  form: {
    backgroundColor: '#fff',
    borderRadius: 12,
    padding: 20,
    shadowColor: '#000',
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.1,
    shadowRadius: 3.84,
    elevation: 5,
    marginBottom: 20,
  },
  inputLabel: {
    fontSize: 16,
    color: '#333',
    marginBottom: 8,
    fontWeight: '500',
  },
  bracketsSection: {
    marginTop: 30,
    marginBottom: 20,
  },
  bracketsTitle: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 5,
    textAlign: 'center',
  },
  bracketsSubtitle: {
    fontSize: 14,
    color: '#666',
    textAlign: 'center',
    marginBottom: 20,
  },
  separator: {
    height: 8,
  },
});
```

### 16. App Principal com StatusBar do Expo (App.tsx)

```typescript
import React from 'react';
import { SafeAreaView } from 'react-native';
import { StatusBar } from 'expo-status-bar';
import { HomeScreen } from './src/screens/Home';

/**
 * Componente principal da aplicação
 * Demonstra configuração com Expo StatusBar e SafeArea
 */
const App: React.FC = () => {
  return (
    <>
      <StatusBar style="dark" backgroundColor="#f5f5f5" />
      <SafeAreaView style={{ flex: 1 }}>
        <HomeScreen />
      </SafeAreaView>
    </>
  );
};

export default App;
```

**Conceitos Aplicados:**

- **Expo StatusBar**: Componente otimizado do Expo para status bar
- **SafeAreaView**: Evita sobreposição com status bar
- **Fragment**: `<>...</>` para agrupar elementos sem wrapper adicional
