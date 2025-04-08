#  `Navegação`
## Navegação no React Native  

- [Repositório](https://github.com/fatec-registro-yuri-villanova/react-native-navegacao)

---

## 1. Configuração Inicial
1.1. Criar novo projeto
   ``` bash
   npx create-expo-app NavegacaoApp --template expo-template-blank-typescript
   cd NavegacaoApp
   ```

1.2. Instalar dependências
   ``` bash
   npm install @react-navigation/native @react-navigation/native-stack
   ```

## 2. Estrutura do Projeto
   ```   
   /src
      /screens
         Home.tsx
         Profile.tsx
         Settings.tsx
      /types
         navigationTypes.ts
      /components
         CustomButton.tsx
   App.tsx
   ```

## 3. Configurando Tipos
`types/navigationTypes.ts`
   ``` javascript  
   export type RootStackParamList = {
      Home: undefined;
      Profile: { name: string };
      Settings: undefined;
   };

   declare global {
      namespace ReactNavigation {
         interface RootParamList extends RootStackParamList {}
      }
   }
   ```

## 4. Criando Componentes de Tela
###`screens/Home.tsx`
   ``` javascript  
   import React from 'react';
   import { View, Text, TextInput } from 'react-native';
   import { NativeStackScreenProps } from '@react-navigation/native-stack';
   import { RootStackParamList } from '../types/navigationTypes';
   import { CustomButton } from '../components/CustomButton';

   type Props = NativeStackScreenProps<RootStackParamList, 'Home'>;

   export function HomeScreen({ navigation }: Props) {
   const [name, setName] = React.useState('');

   return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
         <Text>Home Screen</Text>
         <TextInput
         placeholder="Digite seu nome"
         value={name}
         onChangeText={setName}
         style={{ borderWidth: 1, padding: 10, margin: 10, width: 200 }}
         />
         <CustomButton
         title="Ir para Perfil"
         onPress={() => navigation.navigate('Profile', { name })}
         />
         <CustomButton
         title="Abrir Configurações"
         onPress={() => navigation.navigate('Settings')}
         />
      </View>
   );
   }
   ```

## 5. Configurando o Navegador
###`App.tsx`
   ``` javascript  
   import { NavigationContainer } from '@react-navigation/native';
   import { createNativeStackNavigator } from '@react-navigation/native-stack';
   import { HomeScreen } from './src/screens/Home';
   import { RootStackParamList } from './src/types/navigationTypes';
   import { ProfileScreen } from './src/screens/Profile';
   import { SettingsScreen } from './src/screens/Settings';

   const Stack = createNativeStackNavigator<RootStackParamList>();

   export default function App() {
   return (
      <NavigationContainer>
         <Stack.Navigator initialRouteName="Home">
         <Stack.Screen
            name="Home"
            component={HomeScreen}
            options={{ title: 'Página Inicial' }}
         />
         <Stack.Screen
            name="Profile"
            component={ProfileScreen}
            options={({ route }) => ({ title: "Perfil"})}
            /*  options={({ route }) => ({ title: route.params.name })} */
         />
         <Stack.Screen
            name="Settings"
            component={SettingsScreen}
            options={{
               headerStyle: {
               backgroundColor: '#f4511e',
               },
               headerTintColor: '#fff',
            }}
         />
         </Stack.Navigator>
      </NavigationContainer>
   );
   }
   ```

## 6. Métodos de Navegação
6.1. Navegação Básica
   ``` javascript
   // Navegar para nova tela
   navigation.navigate('Profile', { name: 'Ana' });

   // Voltar
   navigation.goBack();

   // Substituir tela atual
   navigation.replace('Settings');

   // Voltar para primeira tela na pilha
   navigation.popToTop();
   ```

6.2. Navegação com Push
   ``` javascript
   // Adiciona nova tela à pilha (mesma tela pode ser aberta múltiplas vezes)
   navigation.push('Profile', { name: 'Segunda Tela' });
   ```

## 7. Passando Parâmetros
7.1. Enviando parâmetros
   ``` javascript
   // Navegar para nova tela
   navigation.navigate('Profile', { name: 'Ana' });

   // Voltar
   navigation.goBack();

   // Substituir tela atual
   navigation.replace('Settings');

   // Voltar para primeira tela na pilha
   navigation.popToTop();
   ```

7.2. Recebendo parâmetros
###`Profile.tsx`
   ``` javascript
   import { NativeStackScreenProps } from "@react-navigation/native-stack";
   import { RootStackParamList } from "../types/navigationTypes";
   import { Text, View } from "react-native";

   type ProfileProps = NativeStackScreenProps<RootStackParamList, 'Profile'>;

   export function ProfileScreen({ route }: ProfileProps) {
   return (
      <View>
         <Text>Nome: {route.params.name}</Text>
      </View>
   );
   }
   ```

## 8. Componente de Botão Customizado
###`components/CustomButton.tsx`
   ``` javascript  
   import { TouchableOpacity, Text, StyleSheet } from 'react-native';

   type Props = {
   title: string;
   onPress: () => void;
   };

   export function CustomButton({ title, onPress }: Props) {
   return (
      <TouchableOpacity style={styles.button} onPress={onPress}>
         <Text style={styles.text}>{title}</Text>
      </TouchableOpacity>
   );
   }

   const styles = StyleSheet.create({
   button: {
      backgroundColor: '#007AFF',
      padding: 15,
      borderRadius: 10,
      margin: 10,
      minWidth: 200,
      alignItems: 'center',
   },
   text: {
      color: 'white',
      fontSize: 16,
   },
   });
   ```


