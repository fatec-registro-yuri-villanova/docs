# Hooks
## Entendendo a Revolução dos Componentes Funcionais 🎣  

## O que são Hooks?  
**Hooks** são funções especiais introduzidas no **React 16.8** (2019) que permitem usar **estado**, **lifecycle methods** e outros recursos do React **sem classes**. Eles "conectam" (*hook into*) a funcionalidade do React diretamente em **componentes funcionais**, simplificando a escrita e a organização do código.  

---

## Principais Hooks  
🔹 **`useState`:** Gerencia estados em componentes funcionais.  
🔹 **`useEffect`:** Executa efeitos colaterais (como chamadas API) após renderização.  
🔹 **`useContext`:** Acessa o contexto global da aplicação.  
🔹 **`useReducer`:** Gerencia estados complexos com lógica centralizada.  
🔹 **`useRef`:** Referencia elementos do DOM ou armazena valores mutáveis.  
🔹 **Hooks Customizados:** Funções reutilizáveis criadas por você (ex: `useFetch`).  

---

## Por que os Hooks Foram Criados?  
1. **Problemas com Classes:**  
   - Complexidade em reutilizar lógica entre componentes.  
   - Dificuldade em entender `this` e métodos de lifecycle.  
2. **Objetivo dos Hooks:**  
   - Simplificar componentes com **funções puras**.  
   - Facilitar o compartilhamento de lógica entre componentes.  

---

## Regras dos Hooks 🚨  
1. **Só use Hooks no Top-Level:**  
   - Não chame Hooks dentro de loops, condições ou funções aninhadas.  
2. **Só use em Componentes Funcionais ou Custom Hooks:**  
   - Não funcionam em classes.  

---

## Benefícios dos Hooks ✨  
✅ **Código mais limpo:** Menos boilerplate comparado a classes.  
✅ **Reutilização de lógica:** Custom Hooks permitem compartilhar funcionalidades.  
✅ **Organização:** Separação clara de preocupações (ex: estado, efeitos).  
✅ **Adoção gradual:** Podem ser usados ao lado de componentes em classe.  

---

## Exemplo: `useState` + `useEffect`  
```javascript  
    import { useState, useEffect } from 'react';  

    function Timer() {  
    const [seconds, setSeconds] = useState(0);  

    useEffect(() => {  
        const interval = setInterval(() => {  
        setSeconds(prev => prev + 1);  
        }, 1000);  

        return () => clearInterval(interval); // Limpeza do efeito  
    }, []); // Executa apenas uma vez (mount)  

    return <p>Tempo: {seconds} segundos</p>;  
    }  
```

## Quando Usar Hooks? 
🔹Componentes funcionais que precisam de estado ou efeitos.
🔹Substituir componentes em classe para simplificar o código.
🔹Compartilhar lógica entre múltiplos componentes (via Custom Hooks).
