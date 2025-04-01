#  `useEffect`

O useEffect é um dos Hooks mais importantes do React e React Native. Vamos explicá-lo de forma simples e prática com exemplos do seu projeto de conselhos.


## O que é o useEffect? 

É um hook que permite executar efeitos colaterais em componentes funcionais. Pense nele como um substituto moderno para os métodos de ciclo de vida (lifecycle) que existiam em componentes de classe.

## Analogia Simples

Imagine que seu componente é uma cozinha:

- Os ingredientes (props e estado) são suas ferramentas
- O useEffect é como um ajudante que faz tarefas extras quando:
      - Você entra na cozinha (montagem do componente)
      - Você troca de ingredientes (atualização de dependências)
      - Você sai da cozinha (desmontagem do componente) 

---
## Como Funciona no Seu Código?  
No seu componente AdviceCard, você usou:

``` javascript  
useEffect(() => {
   fetchAdvice(); 
}, []);
```

Partes do useEffect:

1. Função de efeito: O código que será executado
   ``` javascript  
   () => {
      fetchAdvice();
   } 
   ```

2. Array de dependências: `[]` (vazio no exemplo)
      - Controla quando o efeito deve ser executado novamente

## Os 3 Comportamentos Principais

1. Executar apenas na montagem (como no seu código)
   ``` javascript  
   useEffect(() => {
   // Código executado uma vez quando o componente é montado
   }, []); // Array vazio
   ```
      - **No exemplo**: Busca o primeiro conselho quando a tela é aberta

2. Executar quando variáveis mudam
   ``` javascript  
   useEffect(() => {
   // Código executado sempre que 'algumaVariavel' mudar
   }, [algumaVariavel]);
  ```

3. Executar limpeza ao desmontar
   ``` javascript  
   useEffect(() => {
   const timer = setInterval(() => {
      // Código repetido
   }, 1000);
   
   return () => {
      clearInterval(timer); // Limpeza quando o componente desmontar
   };
   }, []);
   ```
      - **Aplicabilidade**: Se você tivesse um timer para buscar conselhos automaticamente



## Exemplo Prático

### 1. Estrutura de Tipos (types/advice.ts)
   ``` javascript  
   export interface Slip {
      id: number;
      advice: string;
   }
   
   export interface AdviceResponse {
      slip: Slip;
   }
   ```

### 2. Serviço de API (services/api.ts)
   ``` javascript  
   import { AdviceResponse } from '../types/advice';

   export const getRandomAdvice = async (): Promise<AdviceResponse> => {
   try {
      // Adicione um timestamp para evitar cache
      const response = await fetch(`https://api.adviceslip.com/advice?t=${Date.now()}`);
      
      if (!response.ok) {
         throw new Error('Erro ao buscar conselho');
      }
      
      return await response.json();
   } catch (error) {
      console.error('Erro na chamada da API:', error);
      throw error;
   }
   };
   ```

### 3. Componente AdviceCard (components/AdviceCard.tsx)
   ``` javascript  
   import { getRandomAdvice } from '@/services/api';
   import { Slip } from '@/types/advice';
   import React, { useState, useEffect } from 'react';
   import { 
      View, 
      Text, 
      StyleSheet, 
      TouchableOpacity, 
      ActivityIndicator 
   } from 'react-native';

   const AdviceCard = () => {
   const [advice, setAdvice] = useState<Slip | null>(null);
   const [loading, setLoading] = useState<boolean>(false);
   const [error, setError] = useState<string | null>(null);

   const fetchAdvice = async () => {
      try {
         setLoading(true);
         setError(null);
         const response = await getRandomAdvice();
         setAdvice(response.slip);
         
      } catch (err) {
         setError('Falha ao carregar conselho. Tente novamente.');
         console.error(err);
      } finally {
         setLoading(false);
      }
   };

   useEffect(() => {
      fetchAdvice();
   }, []);

   if (loading && !advice) {
      return <ActivityIndicator size="large" />;
   }

   return (
      <View style={styles.container}>
         {error ? (
         <>
            <Text style={styles.errorText}>{error}</Text>
            <TouchableOpacity onPress={fetchAdvice} style={styles.button}>
               <Text style={styles.buttonText}>Tentar novamente</Text>
            </TouchableOpacity>
         </>
         ) : (
         <>
            <Text style={styles.title}>Conselho #{advice?.id}</Text>
            <Text style={styles.adviceText}>"{advice?.advice}"</Text>
            
            <TouchableOpacity onPress={fetchAdvice} style={styles.button}>
               <Text style={styles.buttonText}>Obter novo conselho</Text>
            </TouchableOpacity>
         </>
         )}
      </View>
   );
   };

   const styles = StyleSheet.create({
   container: {
      padding: 20,
      backgroundColor: '#f8f9fa',
      borderRadius: 10,
      margin: 20,
      alignItems: 'center',
   },
   title: {
      fontSize: 18,
      fontWeight: 'bold',
      marginBottom: 10,
   },
   adviceText: {
      fontSize: 16,
      fontStyle: 'italic',
      textAlign: 'center',
      marginBottom: 20,
   },
   button: {
      backgroundColor: '#007bff',
      padding: 10,
      borderRadius: 5,
      marginTop: 10,
   },
   buttonText: {
      color: 'white',
   },
   errorText: {
      color: 'red',
      marginBottom: 10,
   },
   });

   export default AdviceCard;
   ```
