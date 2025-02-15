import React, { useState, useEffect } from 'react';
import { View, Text, TextInput, Button, Alert, StyleSheet, FlatList } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

// Tela de Cadastro (Cadastro de Usuários)
const CadastroScreen = ({ navigation, route }) => {
  const [name, setName] = useState('');
  const [avatar, setAvatar] = useState('');
  const [email, setEmail] = useState('');

  const users = route.params?.users || [];

  const handleSave = () => {
    if (!name || !avatar || !email) {
      Alert.alert('Erro', 'Todos os campos precisam ser preenchidos.');
      return;
    }

    // Criando um novo usuário (POST)
    const newUser = { id: Math.random().toString(), name, avatar, email };

    // Navegar para a tela de lista de usuários com o novo usuário
    navigation.navigate('UserList', { newUser, users });
  };

  const handleCancel = () => {
    Alert.alert('Cadastro Cancelado', 'Cadastro cancelado com sucesso.', [
      { text: 'OK', onPress: () => navigation.goBack() },
    ]);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.header}>Cadastro de Usuário</Text>

      <TextInput
        style={styles.input}
        placeholder="Nome Completo"
        value={name}
        onChangeText={setName}
      />
      <TextInput
        style={styles.input}
        placeholder="Avatar"
        value={avatar}
        onChangeText={setAvatar}
      />
      <TextInput
        style={styles.input}
        placeholder="E-mail"
        value={email}
        onChangeText={setEmail}
        keyboardType="email-address"
      />

      <Button title="Salvar" onPress={handleSave} color="#4CAF50" />
      
      <Button title="Cancelar" onPress={handleCancel} color="#F44336" />
    </View>
  );
};

// Tela de Lista de Usuários (Visualizar todos os usuários)
const UserListScreen = ({ route, navigation }) => {
  const [users, setUsers] = useState(route.params?.users || []);

  useEffect(() => {
    if (route.params?.newUser) {
      setUsers((prevUsers) => [...prevUsers, route.params.newUser]); // Adicionando o novo usuário sem apagar os existentes
    }
  }, [route.params?.newUser]);

  const handleDelete = (id) => {
    setUsers(users.filter(user => user.id !== id));
    Alert.alert('Usuário excluído', 'O usuário foi removido com sucesso.');
  };

  const handleEdit = (user) => {
    navigation.navigate('Cadastro', { user, users });
  };

  const handleViewAllUsers = () => {
    navigation.navigate('UserDetails', { users });
  };

  return (
    <View style={styles.container}>
      <Text style={styles.header}>Lista de Usuários</Text>

      {/* Lista de Usuários */}
      <FlatList
        data={users}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.userItem}>
            <Text style={styles.userText}>{item.name}</Text>
            <Text style={styles.userText}>{item.avatar}</Text>
            <Text style={styles.userText}>{item.email}</Text>

            <Button
              title="Editar"
              onPress={() => handleEdit(item)}
              color="#FF9800"
            />
            <Button
              title="Excluir"
              onPress={() => handleDelete(item.id)}
              color="#F44336"
            />
          </View>
        )}
      />

      {/* Botão para visualizar todos os usuários cadastrados */}
      <Button
        title="Visualizar Todos os Usuários"
        onPress={handleViewAllUsers}
        color="#2196F3"
      />

      {/* Botão para adicionar um novo cliente */}
      <Button
        title="Adicionar Novo Cliente"
        onPress={() => navigation.navigate('Cadastro', { users })}
        color="#673AB7"
      />
    </View>
  );
};

// Tela de Visualização de Todos os Usuários Criados
const UserDetailsScreen = ({ route }) => {
  const { users } = route.params;

  return (
    <View style={styles.container}>
      <Text style={styles.header}>Todos os Usuários Criados</Text>

      {/* Exibindo todos os usuários cadastrados */}
      <FlatList
        data={users}
        keyExtractor={(item) => item.id}
        renderItem={({ item }) => (
          <View style={styles.userItem}>
            <Text style={styles.userText}>Nome: {item.name}</Text>
            <Text style={styles.userText}>Avatar: {item.avatar}</Text>
            <Text style={styles.userText}>E-mail: {item.email}</Text>
          </View>
        )}
      />
    </View>
  );
};

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="UserList">
        <Stack.Screen name="Cadastro" component={CadastroScreen} />
        <Stack.Screen name="UserList" component={UserListScreen} />
        <Stack.Screen name="UserDetails" component={UserDetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    padding: 20,
    backgroundColor: '#E3F2FD', // Cor de fundo suave
  },
  header: {
    fontSize: 26,
    textAlign: 'center',
    marginBottom: 20,
    color: '#673AB7', // Cor de destaque
  },
  input: {
    borderWidth: 1,
    borderColor: '#4CAF50', // Cor de borda verde
    padding: 10,
    marginBottom: 15,
    borderRadius: 5,
    backgroundColor: '#fff',
  },
  userText: {
    fontSize: 16,
    marginBottom: 10,
    color: '#333', // Cor de texto escura
  },
  userItem: {
    padding: 15,
    backgroundColor: '#fff',
    marginVertical: 10,
    borderRadius: 8,
    borderWidth: 1,
    borderColor: '#DDD',
  },
});

export default App;

