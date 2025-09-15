# Gerenciamento de Estado

## State Hosting (Estado Elevado)

O estado fica no componente **pai** e é passado para os filhos via props.

```javascript
// Estado no componente pai
function TelaCarrinho() {
  const [itens, setItens] = useState([]);
  const [total, setTotal] = useState(0);

  const adicionarItem = (item) => {
    setItens([...itens, item]);
    setTotal(total + item.preco);
  };

  return (
    <View>
      <ListaItens itens={itens} />
      <BotaoAdicionar onAdicionar={adicionarItem} />
      <Total valor={total} />
    </View>
  );
}

function ListaItens({ itens }) {
  return (
    <View>
      {itens.map(item => <Text key={item.id}>{item.nome}</Text>)}
    </View>
  );
}

function BotaoAdicionar({ onAdicionar }) {
  return (
    <Button 
      title="Adicionar Item" 
      onPress={() => onAdicionar({ id: 1, nome: 'Produto', preco: 10 })}
    />
  );
}

function Total({ valor }) {
  return <Text>Total: R$ {valor}</Text>;
}
```

**Quando usar:** Quando múltiplos componentes irmãos precisam compartilhar o mesmo estado.

## Persistent State (Estado Isolado)

O estado fica **isolado** dentro do próprio componente.

```javascript
// Estado isolado em cada componente
function Contador({ titulo }) {
  const [count, setCount] = useState(0); // Estado próprio

  return (
    <View>
      <Text>{titulo}</Text>
      <Text>{count}</Text>
      <Button title="+" onPress={() => setCount(count + 1)} />
      <Button title="-" onPress={() => setCount(count - 1)} />
    </View>
  );
}

function InputBusca({ onBuscar }) {
  const [termo, setTermo] = useState(''); // Estado próprio
  const [loading, setLoading] = useState(false);

  const buscar = () => {
    setLoading(true);
    onBuscar(termo);
    setTimeout(() => setLoading(false), 1000);
  };

  return (
    <View>
      <TextInput 
        value={termo}
        onChangeText={setTermo}
        placeholder="Buscar..."
      />
      <Button 
        title={loading ? "Buscando..." : "Buscar"}
        onPress={buscar}
        disabled={loading}
      />
    </View>
  );
}

// Uso dos componentes isolados
function TelaExemplo() {
  return (
    <View>
      <Contador titulo="Contador A" />
      <Contador titulo="Contador B" />
      <InputBusca onBuscar={(termo) => console.log(termo)} />
    </View>
  );
}
```

**Quando usar:** Quando o componente é independente e reutilizável, sem necessidade de compartilhar estado.

## Resumo

- **State Hosting**: Estado no pai → compartilhado entre filhos
- **Persistent State**: Estado no próprio componente → isolado e independente