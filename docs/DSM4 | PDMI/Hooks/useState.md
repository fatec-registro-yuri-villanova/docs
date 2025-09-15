#  `useState`
## Gerenciamento de Estado com `useState` no React Native  

## O que é o `useState`?  
- **Hook do React** para adicionar estado a componentes funcionais.  
- Retorna um **valor** (estado atual) e uma **função** para atualizá-lo.  
- Ideal para estados **locais** e simples (ex: formulários, contadores, visibilidade de UI).  

---

## Vantagens  
✅ **Simplicidade:** Facilita a criação e atualização de estados sem classes.  
✅ **Integração:** Funciona perfeitamente com outros Hooks (ex: `useEffect`).  
✅ **Performance:** Atualizações eficientes com re-renderizações otimizadas.  

---

## Como usar?  
1. **Importe o Hook:**  
   ``` javascript  
   import { useState } from 'react';  
   ```

2. **Inicialize o estado:**  
   ``` javascript  
   const [count, setCount] = useState(0); // Valor inicial: 0  
   ```

3. **Atualize o estado:**  
   ``` javascript  
   setCount(count + 1); // Atualiza para 1  
   ```

## Exemplo Prático
   ``` javascript  
    import { View, Text, Button } from 'react-native';  

    const Counter = () => {  
    const [count, setCount] = useState(0);  

    return (  
        <View>  
        <Text>Contador: {count}</Text>  
        <Button  
            title="Incrementar"  
            onPress={() => setCount(count + 1)}  
        />  
        </View>  
    );  
    };  
   ```

## Dicas Importantes
- Use para estados locais (escopo do componente).
- Evite objetos complexos; prefira múltiplos useState.
- Para estados globais, combine com Context API ou bibliotecas (ex: Redux). 

---

## Quando utilizar?
- Controle de inputs, modais, dados temporários.
- Componentes funcionais que precisam de estado simples.
- Evite para lógicas complexas ou compartilhamento entre muitos componentes.

---