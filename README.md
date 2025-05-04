# Flutter-Firebase-App
Repositório criado para demonstrar, com Flutter e Firebase, a construção de um app completo com autenticação de usuários via Firebase Auth e banco de dados Firestore. Projeto prático e educacional para integração de backend na nuvem.

# Documentação do Código: `main.dart`

Este arquivo é o ponto de entrada principal do aplicativo Flutter. Ele configura a inicialização do Firebase, define o tema global do aplicativo e implementa a lógica de navegação com base no estado de autenticação do usuário.

---

## Importações

As bibliotecas e arquivos necessários para o funcionamento do aplicativo são importados:

- **`firebase_tutorial_app/home_page.dart`**: Página inicial do aplicativo.
- **`flutter/material.dart`**: Biblioteca principal do Flutter para construção de interfaces.
- **`firebase_core.dart`**: Biblioteca para inicializar o Firebase.
- **`firebase_options.dart`**: Configurações específicas do Firebase para a plataforma.
- **`firebase_tutorial_app/signup_page.dart`**: Página de cadastro do aplicativo.
- **`firebase_auth.dart`**: Biblioteca para autenticação com Firebase.

---

## Função `main`

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  runApp(const MyApp());
}
```

### Explicação:
- **`WidgetsFlutterBinding.ensureInitialized()`**: Garante que o Flutter esteja inicializado antes de executar código assíncrono.
- **`Firebase.initializeApp`**: Inicializa o Firebase com as configurações específicas da plataforma.
- **`runApp`**: Inicia o aplicativo Flutter com o widget raiz `MyApp`.

---

## Classe `MyApp`

A classe principal do aplicativo que define o tema e a lógica de navegação.

### Construtor

```dart
const MyApp({super.key});
```
- Define um construtor padrão para a classe `MyApp`.

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  return MaterialApp(
    title: 'Task Management',
    theme: ThemeData(
      ...
    ),
    home: StreamBuilder(
      ...
    ),
  );
}
```

- **`MaterialApp`**: Widget raiz que configura o tema e a navegação do aplicativo.
- **`title`**: Define o título do aplicativo.
- **`theme`**: Configura o tema global do aplicativo.
- **`home`**: Define a página inicial com base no estado de autenticação do usuário.

---

## Tema do Aplicativo

```dart
theme: ThemeData(
  fontFamily: 'Cera Pro',
  elevatedButtonTheme: ElevatedButtonThemeData(
    style: ElevatedButton.styleFrom(
      backgroundColor: Colors.black,
      minimumSize: const Size(double.infinity, 60),
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(15),
      ),
    ),
  ),
  inputDecorationTheme: InputDecorationTheme(
    contentPadding: const EdgeInsets.all(27),
    enabledBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.grey.shade300, width: 3),
      borderRadius: BorderRadius.circular(10),
    ),
    focusedBorder: OutlineInputBorder(
      borderSide: const BorderSide(
        width: 3,
      ),
      borderRadius: BorderRadius.circular(10),
    ),
  ),
),
```

### Explicação:
- **`fontFamily`**: Define a fonte personalizada do aplicativo.
- **`elevatedButtonTheme`**: Configura o estilo global para botões elevados:
  - Cor de fundo: `Colors.black`.
  - Tamanho mínimo: `double.infinity` de largura e `60` de altura.
  - Bordas arredondadas: `15` de raio.
- **`inputDecorationTheme`**: Configura o estilo global para campos de entrada de texto:
  - Espaçamento interno: `27`.
  - Bordas arredondadas: `10` de raio.
  - Cor e largura das bordas: `Colors.grey.shade300` e `3`.

---

## Lógica de Navegação

```dart
home: StreamBuilder(
  stream: FirebaseAuth.instance.authStateChanges(),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return const Center(child: CircularProgressIndicator());
    }
    if (snapshot.data != null) {
      return const MyHomePage();
    }
    return const SignUpPage();
  },
),
```

### Explicação:
- **`StreamBuilder`**: Observa mudanças no estado de autenticação do Firebase.
  - **`FirebaseAuth.instance.authStateChanges()`**: Stream que emite eventos quando o estado de autenticação do usuário muda.
- **Lógica de navegação**:
  - Se o estado da conexão for `ConnectionState.waiting`, exibe um indicador de carregamento (`CircularProgressIndicator`).
  - Se o usuário estiver autenticado (`snapshot.data != null`), redireciona para a página inicial (`MyHomePage`).
  - Caso contrário, redireciona para a página de cadastro (`SignUpPage`).

---

## Resumo

Este arquivo configura:
1. A inicialização do Firebase.
2. O tema global do aplicativo.
3. A lógica de navegação com base no estado de autenticação do usuário.

Ele é o ponto de entrada principal do aplicativo Flutter e utiliza o Firebase para autenticação e gerenciamento de estado.

# Documentação do Código: `home_page.dart`

Este arquivo define a página inicial do aplicativo, onde as tarefas do usuário são exibidas. Ele utiliza o Firebase Firestore para buscar as tarefas e exibe uma lista interativa com opções para adicionar e excluir tarefas.

---

## Importações

As bibliotecas e arquivos necessários para o funcionamento da página são importados:

- **`flutter/cupertino.dart`** e **`flutter/material.dart`**: Bibliotecas principais do Flutter para construção de interfaces.
- **`firebase_tutorial_app/add_new_task.dart`**: Página para adicionar novas tarefas.
- **`firebase_tutorial_app/utils.dart`**: Utilitários personalizados, como funções auxiliares.
- **`firebase_tutorial_app/widgets/date_selector.dart`**: Widget para seleção de datas.
- **`firebase_tutorial_app/widgets/task_card.dart`**: Widget para exibição de tarefas.
- **`cloud_firestore.dart`**: Biblioteca para integração com o Firebase Firestore.
- **`firebase_auth.dart`**: Biblioteca para autenticação com Firebase.

---

## Classe `MyHomePage`

A classe principal que representa a página inicial do aplicativo.

### Construtor

```dart
const MyHomePage({super.key});
```

- Define um construtor padrão para a classe `MyHomePage`.

---

## Classe `_MyHomePageState`

A classe de estado que gerencia a lógica e a interface da página inicial.

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      ...
    ),
    body: Center(
      ...
    ),
  );
}
```

- **`Scaffold`**: Estrutura principal da página, contendo a barra de aplicativos e o corpo da interface.

---

### Barra de Aplicativos (`AppBar`)

```dart
appBar: AppBar(
  title: const Text('My Tasks'),
  actions: [
    IconButton(
      onPressed: () {
        Navigator.push(
          context,
          MaterialPageRoute(builder: (context) => const AddNewTask()),
        );
      },
      icon: const Icon(CupertinoIcons.add),
    ),
  ],
),
```

- **Título**: Exibe o texto "My Tasks".
- **Botão de Adicionar**:
  - Ícone: Um botão com o ícone de adição (`CupertinoIcons.add`).
  - Ação: Navega para a página de adicionar nova tarefa (`AddNewTask`).

---

### Corpo da Página (`body`)

```dart
body: Center(
  child: Column(
    children: [
      const DateSelector(),
      StreamBuilder(
        ...
      ),
    ],
  ),
),
```

- **`DateSelector`**: Widget para seleção de datas.
- **`StreamBuilder`**: Observa as mudanças no Firestore e atualiza a lista de tarefas em tempo real.

---

### StreamBuilder

```dart
StreamBuilder(
  stream: FirebaseFirestore.instance
      .collection('tasks')
      .where(
        'creatorId',
        isEqualTo: FirebaseAuth.instance.currentUser!.uid,
      )
      .snapshots(),
  builder: (context, snapshot) {
    ...
  },
),
```

- **Stream**: Obtém as tarefas do Firestore onde o `creatorId` corresponde ao ID do usuário autenticado.
- **Builder**:
  - Exibe um indicador de carregamento enquanto os dados estão sendo buscados.
  - Exibe uma mensagem "No tasks found" se não houver tarefas.
  - Exibe uma lista de tarefas se os dados estiverem disponíveis.

---

### Lista de Tarefas (`ListView.builder`)

```dart
ListView.builder(
  itemCount: snapshot.data!.docs.length,
  itemBuilder: (context, index) {
    return Dismissible(
      ...
    );
  },
),
```

- **`itemCount`**: Número de tarefas retornadas pelo Firestore.
- **`itemBuilder`**: Constrói cada item da lista como um widget `Dismissible`.

---

### Widget `Dismissible`

```dart
Dismissible(
  key: ValueKey(index),
  onDismissed: (direction) async {
    if (direction == DismissDirection.endToStart ||
        direction == DismissDirection.startToEnd) {
      await FirebaseFirestore.instance
          .collection('tasks')
          .doc(snapshot.data!.docs[index].id)
          .delete();
    }
  },
  child: Row(
    children: [
      Expanded(
        child: TaskCard(
          ...
        ),
      ),
      Container(
        ...
      ),
      const Padding(
        padding: EdgeInsets.all(12.0),
        child: Text(
          '10:00AM',
          style: TextStyle(fontSize: 17),
        ),
      ),
    ],
  ),
),
```

- **`Dismissible`**: Permite que o usuário deslize para excluir uma tarefa.
  - **`onDismissed`**: Remove a tarefa do Firestore quando deslizada.
- **`Row`**: Exibe os detalhes da tarefa em uma linha:
  - **`TaskCard`**: Widget personalizado que exibe o título, descrição e data da tarefa.
  - **`Container`**: Exibe a imagem associada à tarefa (se disponível) ou um espaço vazio.
  - **`Text`**: Exibe um horário fixo ("10:00AM") como exemplo.

---

## Widgets Personalizados

### `TaskCard`

```dart
TaskCard(
  color: hexToColor(
    snapshot.data!.docs[index].data()['color'],
  ),
  headerText: snapshot.data!.docs[index].data()['title'],
  descriptionText: snapshot.data!.docs[index].data()['description'],
  scheduledDate: snapshot.data!.docs[index].data()['date'].toString(),
),
```

- **`color`**: Cor da tarefa, convertida de hexadecimal.
- **`headerText`**: Título da tarefa.
- **`descriptionText`**: Descrição da tarefa.
- **`scheduledDate`**: Data agendada da tarefa.

---

### `Container` (Imagem)

```dart
Container(
  height: 50,
  width: 50,
  decoration: BoxDecoration(
    color: strengthenColor(
      const Color.fromRGBO(246, 222, 194, 1),
      0.69,
    ),
    image: snapshot.data!.docs[index].data()['imageUrl'] == null
        ? null
        : DecorationImage(
            image: NetworkImage(
              snapshot.data!.docs[index].data()['imageUrl'],
            ),
            fit: BoxFit.cover,
          ),
    shape: BoxShape.circle,
  ),
),
```

- **Imagem**: Exibe a imagem associada à tarefa (se disponível) em um formato circular.
- **Cor de Fundo**: Uma cor de base personalizada com opacidade ajustada.

# Documentação do Código: `signup_page.dart`

Este arquivo implementa a página de cadastro do aplicativo, permitindo que novos usuários criem uma conta utilizando email e senha. Ele utiliza o Firebase Authentication para gerenciar o processo de registro.

---

## Importações

As bibliotecas e arquivos necessários para o funcionamento da página são importados:

- **`flutter/material.dart`**: Biblioteca principal do Flutter para construção de interfaces.
- **`firebase_tutorial_app/login_page.dart`**: Página de login para redirecionar usuários que já possuem uma conta.
- **`firebase_auth.dart`**: Biblioteca para autenticação com Firebase.

---

## Classe `SignUpPage`

A classe principal que representa a página de cadastro.

### Construtor

```dart
const SignUpPage({super.key});
```

- Define um construtor padrão para a classe `SignUpPage`.

### Método `route`

```dart
static route() => MaterialPageRoute(builder: (context) => const SignUpPage());
```

- Retorna uma rota para navegar até a página de cadastro.

---

## Classe `_SignUpPageState`

A classe de estado que gerencia a lógica e a interface da página de cadastro.

### Propriedades

- **`emailController`**: Controlador para o campo de entrada de email.
- **`passwordController`**: Controlador para o campo de entrada de senha.
- **`formKey`**: Chave global para gerenciar o estado do formulário.

---

### Método `dispose`

```dart
@override
void dispose() {
  emailController.dispose();
  passwordController.dispose();
  super.dispose();
}
```

- Garante que os controladores de texto sejam descartados corretamente para evitar vazamentos de memória.

---

### Método `createUserWithEmailAndPassword`

```dart
Future<void> createUserWithEmailAndPassword() async {
  try {
    final userCredential = await FirebaseAuth.instance
        .createUserWithEmailAndPassword(
          email: emailController.text.trim(),
          password: passwordController.text.trim(),
        );
  } on FirebaseAuthException catch (e) {
    print(e.message);
  }
}
```

- **Descrição**: Cria uma nova conta de usuário no Firebase Authentication utilizando email e senha.
- **Tratamento de Erros**: Captura exceções específicas do Firebase e exibe a mensagem de erro no console.

---

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    body: Padding(
      ...
    ),
  );
}
```

- **`Scaffold`**: Estrutura principal da página, contendo o corpo da interface.

---

## Interface do Usuário

### Estrutura Geral

```dart
body: Padding(
  padding: const EdgeInsets.all(15.0),
  child: Form(
    key: formKey,
    child: Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        ...
      ],
    ),
  ),
),
```

- **`Padding`**: Adiciona espaçamento ao redor do formulário.
- **`Form`**: Envolve os campos de entrada e o botão de cadastro, permitindo validação.
- **`Column`**: Organiza os widgets verticalmente.

---

### Widgets da Interface

#### Título

```dart
const Text(
  'Sign Up.',
  style: TextStyle(fontSize: 50, fontWeight: FontWeight.bold),
),
```

- Exibe o título "Sign Up." com estilo destacado.

---

#### Campo de Email

```dart
TextFormField(
  controller: emailController,
  decoration: const InputDecoration(hintText: 'Email'),
),
```

- Campo de entrada para o email do usuário.
- Utiliza o controlador `emailController`.

---

#### Campo de Senha

```dart
TextFormField(
  controller: passwordController,
  decoration: const InputDecoration(hintText: 'Password'),
  obscureText: true,
),
```

- Campo de entrada para a senha do usuário.
- Utiliza o controlador `passwordController`.
- **`obscureText: true`**: Oculta o texto digitado para proteger a senha.

---

#### Botão de Cadastro

```dart
ElevatedButton(
  onPressed: () async {
    await createUserWithEmailAndPassword();
  },
  child: const Text(
    'SIGN UP',
    style: TextStyle(fontSize: 16, color: Colors.white),
  ),
),
```

- Botão que chama o método `createUserWithEmailAndPassword` ao ser pressionado.
- Exibe o texto "SIGN UP" com estilo personalizado.

---

#### Link para Login

```dart
GestureDetector(
  onTap: () {
    Navigator.push(context, LoginPage.route());
  },
  child: RichText(
    text: TextSpan(
      text: 'Already have an account? ',
      style: Theme.of(context).textTheme.titleMedium,
      children: [
        TextSpan(
          text: 'Sign In',
          style: Theme.of(context).textTheme.titleMedium
              ?.copyWith(fontWeight: FontWeight.bold),
        ),
      ],
    ),
  ),
),
```

- **Descrição**: Exibe um texto interativo que redireciona o usuário para a página de login.
- **`GestureDetector`**: Detecta toques no texto.
- **`Navigator.push`**: Navega para a página de login utilizando a rota definida em `LoginPage.route()`.

---

## Resumo

Este arquivo implementa a página de cadastro do aplicativo, com as seguintes funcionalidades:

1. **Cadastro de Usuário**: Permite que novos usuários criem uma conta utilizando email e senha.
2. **Validação de Formulário**: Utiliza um formulário para gerenciar os campos de entrada.
3. **Redirecionamento para Login**: Inclui um link para redirecionar usuários que já possuem uma conta para a página de login.
4. **Integração com Firebase**: Utiliza o Firebase Authentication para gerenciar o processo de registro.

A interface é simples e intuitiva, garantindo uma experiência de usuário fluida.

# Documentação do Código: `login_page.dart`

Este arquivo implementa a página de login do aplicativo, permitindo que usuários existentes façam login utilizando email e senha. Ele utiliza o Firebase Authentication para gerenciar o processo de autenticação.

---

## Importações

As bibliotecas e arquivos necessários para o funcionamento da página são importados:

- **`firebase_auth.dart`**: Biblioteca para autenticação com Firebase.
- **`flutter/material.dart`**: Biblioteca principal do Flutter para construção de interfaces.
- **`firebase_tutorial_app/signup_page.dart`**: Página de cadastro para redirecionar usuários que ainda não possuem uma conta.

---

## Classe `LoginPage`

A classe principal que representa a página de login.

### Construtor

```dart
const LoginPage({super.key});
```

- Define um construtor padrão para a classe `LoginPage`.

### Método `route`

```dart
static route() => MaterialPageRoute(builder: (context) => const LoginPage());
```

- Retorna uma rota para navegar até a página de login.

---

## Classe `_LoginPageState`

A classe de estado que gerencia a lógica e a interface da página de login.

### Propriedades

- **`emailController`**: Controlador para o campo de entrada de email.
- **`passwordController`**: Controlador para o campo de entrada de senha.
- **`formKey`**: Chave global para gerenciar o estado do formulário.

---

### Método `dispose`

```dart
@override
void dispose() {
  emailController.dispose();
  passwordController.dispose();
  super.dispose();
}
```

- Garante que os controladores de texto sejam descartados corretamente para evitar vazamentos de memória.

---

### Método `loginUserWithEmailAndPassword`

```dart
Future<void> loginUserWithEmailAndPassword() async {
  try {
    final userCredential = await FirebaseAuth.instance
        .signInWithEmailAndPassword(
          email: emailController.text.trim(),
          password: passwordController.text.trim(),
        );
    print(userCredential);
  } on FirebaseAuthException catch (e) {
    print(e.message);
  }
}
```

- **Descrição**: Faz login do usuário no Firebase Authentication utilizando email e senha.
- **Tratamento de Erros**: Captura exceções específicas do Firebase e exibe a mensagem de erro no console.

---

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(),
    body: Padding(
      ...
    ),
  );
}
```

- **`Scaffold`**: Estrutura principal da página, contendo a barra de aplicativos e o corpo da interface.

---

## Interface do Usuário

### Estrutura Geral

```dart
body: Padding(
  padding: const EdgeInsets.all(15.0),
  child: Form(
    key: formKey,
    child: Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        ...
      ],
    ),
  ),
),
```

- **`Padding`**: Adiciona espaçamento ao redor do formulário.
- **`Form`**: Envolve os campos de entrada e o botão de login, permitindo validação.
- **`Column`**: Organiza os widgets verticalmente.

---

### Widgets da Interface

#### Título

```dart
const Text(
  'Sign In.',
  style: TextStyle(fontSize: 50, fontWeight: FontWeight.bold),
),
```

- Exibe o título "Sign In." com estilo destacado.

---

#### Campo de Email

```dart
TextFormField(
  controller: emailController,
  decoration: const InputDecoration(hintText: 'Email'),
),
```

- Campo de entrada para o email do usuário.
- Utiliza o controlador `emailController`.

---

#### Campo de Senha

```dart
TextFormField(
  controller: passwordController,
  decoration: const InputDecoration(hintText: 'Password'),
  obscureText: true,
),
```

- Campo de entrada para a senha do usuário.
- Utiliza o controlador `passwordController`.
- **`obscureText: true`**: Oculta o texto digitado para proteger a senha.

---

#### Botão de Login

```dart
ElevatedButton(
  onPressed: () async {
    await loginUserWithEmailAndPassword();
  },
  child: const Text(
    'SIGN IN',
    style: TextStyle(fontSize: 16, color: Colors.white),
  ),
),
```

- Botão que chama o método `loginUserWithEmailAndPassword` ao ser pressionado.
- Exibe o texto "SIGN IN" com estilo personalizado.

---

#### Link para Cadastro

```dart
GestureDetector(
  onTap: () {
    Navigator.push(context, SignUpPage.route());
  },
  child: RichText(
    text: TextSpan(
      text: 'Don\'t have an account? ',
      style: Theme.of(context).textTheme.titleMedium,
      children: [
        TextSpan(
          text: 'Sign Up',
          style: Theme.of(context).textTheme.titleMedium
              ?.copyWith(fontWeight: FontWeight.bold),
        ),
      ],
    ),
  ),
),
```

- **Descrição**: Exibe um texto interativo que redireciona o usuário para a página de cadastro.
- **`GestureDetector`**: Detecta toques no texto.
- **`Navigator.push`**: Navega para a página de cadastro utilizando a rota definida em `SignUpPage.route()`.

---

## Resumo

Este arquivo implementa a página de login do aplicativo, com as seguintes funcionalidades:

1. **Login de Usuário**: Permite que usuários existentes façam login utilizando email e senha.
2. **Validação de Formulário**: Utiliza um formulário para gerenciar os campos de entrada.
3. **Redirecionamento para Cadastro**: Inclui um link para redirecionar usuários que ainda não possuem uma conta para a página de cadastro.
4. **Integração com Firebase**: Utiliza o Firebase Authentication para gerenciar o processo de autenticação.

A interface é simples e intuitiva, garantindo uma experiência de usuário fluida.

# Documentação do Código: `add_new_task.dart`

Este arquivo implementa a página para adicionar novas tarefas no aplicativo. Ele permite que o usuário insira informações como título, descrição, data, cor e uma imagem associada à tarefa. Os dados são enviados para o Firebase Firestore e a imagem é armazenada no Firebase Storage.

---

## Importações

As bibliotecas e arquivos necessários para o funcionamento da página são importados:

- **`dart:io`**: Para manipulação de arquivos (ex.: imagens).
- **`firebase_auth.dart`**: Biblioteca para autenticação com Firebase.
- **`flex_color_picker.dart`**: Biblioteca para seleção de cores.
- **`flutter/material.dart`**: Biblioteca principal do Flutter para construção de interfaces.
- **`intl.dart`**: Para formatação de datas.
- **`cloud_firestore.dart`**: Biblioteca para integração com o Firebase Firestore.
- **`firebase_storage.dart`**: Biblioteca para armazenamento de arquivos no Firebase Storage.
- **`dotted_border.dart`**: Biblioteca para criar bordas pontilhadas.
- **`uuid.dart`**: Para gerar identificadores únicos.
- **`firebase_tutorial_app/utils.dart`**: Utilitários personalizados, como funções auxiliares.

---

## Classe `AddNewTask`

A classe principal que representa a página de adicionar nova tarefa.

### Construtor

```dart
const AddNewTask({super.key});
```

- Define um construtor padrão para a classe `AddNewTask`.

---

## Classe `_AddNewTaskState`

A classe de estado que gerencia a lógica e a interface da página de adicionar nova tarefa.

### Propriedades

- **`titleController`**: Controlador para o campo de entrada do título.
- **`descriptionController`**: Controlador para o campo de entrada da descrição.
- **`selectedDate`**: Data selecionada pelo usuário (inicialmente definida como a data atual).
- **`_selectedColor`**: Cor selecionada pelo usuário (inicialmente azul).
- **`file`**: Arquivo de imagem selecionado pelo usuário.

---

### Método `dispose`

```dart
@override
void dispose() {
  titleController.dispose();
  descriptionController.dispose();
  super.dispose();
}
```

- Garante que os controladores de texto sejam descartados corretamente para evitar vazamentos de memória.

---

### Método `uploadImageToStorage`

```dart
Future<String> uploadImageToStorage(uuid) async {
  try {
    final imagesRef = FirebaseStorage.instance
        .ref('firebase_tutorial_app_images')
        .child('task')
        .child(uuid);
    final uploadTask = imagesRef.putFile(file!);
    final taskSnapshot = await uploadTask;
    final imageUrl = await taskSnapshot.ref.getDownloadURL();
    return imageUrl;
  } catch (e) {
    print('Error uploading image: $e');
    return '';
  }
}
```

- **Descrição**: Faz o upload da imagem selecionada para o Firebase Storage e retorna a URL da imagem.
- **Tratamento de Erros**: Retorna uma string vazia em caso de falha.

---

### Método `uploadTaskToDb`

```dart
Future<void> uploadTaskToDb() async {
  try {
    final id = const Uuid().v4();
    final imageUrl = await uploadImageToStorage(id);
    if (imageUrl == '') {
      throw Exception(
        'No file selected. Please select an image before submitting.',
      );
    }
    await FirebaseFirestore.instance.collection('tasks').doc(id).set({
      'title': titleController.text.trim(),
      'description': descriptionController.text.trim(),
      'date': selectedDate,
      'postedAt': FieldValue.serverTimestamp(),
      'color': rgbToHex(_selectedColor),
      'creatorId': FirebaseAuth.instance.currentUser!.uid,
      'imageUrl': imageUrl,
    });
  } catch (e) {
    print(e);
  }
}
```

- **Descrição**: Envia os dados da tarefa para o Firebase Firestore.
- **Validação**: Lança uma exceção se nenhuma imagem for selecionada.
- **Campos Enviados**:
  - `title`: Título da tarefa.
  - `description`: Descrição da tarefa.
  - `date`: Data selecionada.
  - `postedAt`: Timestamp do servidor.
  - `color`: Cor selecionada (convertida para hexadecimal).
  - `creatorId`: ID do usuário autenticado.
  - `imageUrl`: URL da imagem armazenada no Firebase Storage.

---

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      ...
    ),
    body: SingleChildScrollView(
      ...
    ),
  );
}
```

- **`Scaffold`**: Estrutura principal da página, contendo a barra de aplicativos e o corpo da interface.

---

## Interface do Usuário

### Barra de Aplicativos (`AppBar`)

```dart
appBar: AppBar(
  title: const Text('Add New Task'),
  actions: [
    GestureDetector(
      onTap: () async {
        final selDate = await showDatePicker(
          context: context,
          firstDate: DateTime.now(),
          lastDate: DateTime.now().add(const Duration(days: 90)),
        );
        if (selDate != null) {
          setState(() {
            selectedDate = selDate;
          });
        }
      },
      child: Padding(
        padding: const EdgeInsets.all(8.0),
        child: Text(DateFormat('MM-d-y').format(selectedDate)),
      ),
    ),
  ],
),
```

- **Título**: Exibe o texto "Add New Task".
- **Seleção de Data**: Permite que o usuário selecione uma data utilizando o `showDatePicker`.

---

### Corpo da Página

#### Seleção de Imagem

```dart
GestureDetector(
  onTap: () async {
    final image = await selectImage();
    setState(() {
      file = image;
    });
  },
  child: DottedBorder(
    ...
    child: Container(
      ...
      child: file != null
          ? Image.file(file!)
          : const Center(
              child: Icon(Icons.camera_alt_outlined, size: 40),
            ),
    ),
  ),
),
```

- **Descrição**: Permite que o usuário selecione uma imagem para a tarefa.
- **Borda Pontilhada**: Exibe uma borda estilizada ao redor do contêiner.
- **Ícone de Câmera**: Exibido se nenhuma imagem for selecionada.

---

#### Campos de Entrada

(*continuação não fornecida, mas você pode seguir o mesmo padrão para os campos de título, descrição, seleção de cor e botão de submissão.*)

- **Título**:
dart
  TextFormField(
    controller: titleController,
    decoration: const InputDecoration(hintText: 'Title'),
  ),
  
- **Descrição**:
dart
  TextFormField(
    controller: descriptionController,
    decoration: const InputDecoration(hintText: 'Description'),
    maxLines: 3,
  ),
  
---

#### Seleção de Cor
dart
ColorPicker(
  pickersEnabled: const {ColorPickerType.wheel: true},
  color: Colors.blue,
  onColorChanged: (Color color) {
    setState(() {
      _selectedColor = color;
    });
  },
  heading: const Text('Select color'),
  subheading: const Text('Select a different shade'),
),
- **Descrição**: Permite que o usuário selecione uma cor para a tarefa utilizando um seletor de cores.

---

#### Botão de Envio
dart
ElevatedButton(
  onPressed: () async {
    await uploadTaskToDb();
  },
  child: const Text(
    'SUBMIT',
    style: TextStyle(fontSize: 16, color: Colors.white),
  ),
),
- **Descrição**: Envia os dados da tarefa para o Firebase Firestore ao ser pressionado.

---

## Resumo

Este arquivo implementa a página de adicionar nova tarefa, com as seguintes funcionalidades:

1. **Seleção de Imagem**: Permite que o usuário selecione uma imagem para a tarefa.
2. **Campos de Entrada**: Inclui campos para título e descrição.
3. **Seleção de Data e Cor**: Permite que o usuário escolha uma data e uma cor para a tarefa.
4. **Integração com Firebase**:
   - Armazena a imagem no Firebase Storage.
   - Salva os dados da tarefa no Firebase Firestore.

A interface é intuitiva e responsiva, garantindo uma experiência de usuário fluida.

# Documentação do Código: `utils.dart`

Este arquivo contém funções utilitárias que são usadas em várias partes do aplicativo. Ele inclui funções para manipulação de cores, conversão de cores, e seleção de imagens da galeria do dispositivo.

---

## Importações

As bibliotecas necessárias para o funcionamento das funções utilitárias são importadas:

- **`dart:io`**: Para manipulação de arquivos.
- **`dart:ui`**: Para manipulação de cores.
- **`image_picker.dart`**: Biblioteca para selecionar imagens da galeria ou câmera.

---

## Funções

### `strengthenColor`

```dart
Color strengthenColor(Color color, double factor) {
  int r = (color.red * factor).clamp(0, 255).toInt();
  int g = (color.green * factor).clamp(0, 255).toInt();
  int b = (color.blue * factor).clamp(0, 255).toInt();
  return Color.fromARGB(color.alpha, r, g, b);
}
```

- **Descrição**: Ajusta a intensidade de uma cor multiplicando seus componentes RGB por um fator.
- **Parâmetros**:
  - `color`: A cor original.
  - `factor`: O fator pelo qual os componentes RGB serão multiplicados.
- **Retorno**: Uma nova cor com os componentes ajustados.

---

### `rgbToHex`

```dart
String rgbToHex(Color color) {
  return '${color.red.toRadixString(16).padLeft(2, '0')}${color.green.toRadixString(16).padLeft(2, '0')}${color.blue.toRadixString(16).padLeft(2, '0')}';
}
```

- **Descrição**: Converte uma cor RGB para o formato hexadecimal.
- **Parâmetros**:
  - `color`: A cor a ser convertida.
- **Retorno**: Uma string representando a cor no formato hexadecimal.

---

### `hexToColor`

```dart
Color hexToColor(String hex) {
  return Color(int.parse(hex, radix: 16) + 0xFF000000);
}
```

- **Descrição**: Converte uma string hexadecimal para uma cor RGB.
- **Parâmetros**:
  - `hex`: A string hexadecimal representando a cor.
- **Retorno**: Um objeto `Color` correspondente à string hexadecimal.

---

### `selectImage`

```dart
Future<File?> selectImage() async {
  final imagePicker = ImagePicker();
  XFile? file = await imagePicker.pickImage(source: ImageSource.gallery);
  if (file != null) {
    return File(file.path);
  }
  return null;
}
```

- **Descrição**: Abre a galeria do dispositivo para o usuário selecionar uma imagem.
- **Parâmetros**: Nenhum.
- **Retorno**: Um objeto `File` representando a imagem selecionada ou `null` se nenhuma imagem for selecionada.

---

## Resumo

Este arquivo fornece utilitários essenciais para:

1. **Manipulação de Cores**:
   - Ajustar a intensidade de cores.
   - Converter cores entre os formatos RGB e hexadecimal.
2. **Seleção de Imagens**:
   - Permitir que o usuário selecione uma imagem da galeria do dispositivo.

Essas funções são reutilizáveis e ajudam a simplificar a lógica em outras partes do aplicativo.

# Documentação do Código: `task_card.dart`

Este arquivo implementa o widget `TaskCard`, que é usado para exibir informações de uma tarefa em um cartão estilizado. Ele é altamente reutilizável e pode ser personalizado com diferentes cores, textos e datas.

---

## Importações

- **`flutter/material.dart`**: Biblioteca principal do Flutter para construção de interfaces.

---

## Classe `TaskCard`

A classe `TaskCard` é um widget sem estado (`StatelessWidget`) que exibe os detalhes de uma tarefa, como título, descrição, data e cor.

### Construtor

```dart
const TaskCard({
  super.key,
  required this.color,
  required this.headerText,
  required this.descriptionText,
  required this.scheduledDate,
});
```

- **Parâmetros**:
  - `color`: A cor de fundo do cartão.
  - `headerText`: O título da tarefa.
  - `descriptionText`: A descrição da tarefa.
  - `scheduledDate`: A data agendada da tarefa.

---

### Propriedades

- **`color`**: Define a cor de fundo do cartão.
- **`headerText`**: O texto que será exibido como título da tarefa.
- **`descriptionText`**: O texto que será exibido como descrição da tarefa.
- **`scheduledDate`**: A data agendada da tarefa, exibida no rodapé do cartão.

---

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  return Container(
    margin: const EdgeInsets.symmetric(horizontal: 20, vertical: 10),
    padding: const EdgeInsets.symmetric(vertical: 20.0).copyWith(left: 15),
    decoration: BoxDecoration(
      color: color,
      borderRadius: const BorderRadius.all(Radius.circular(15)),
    ),
    child: Align(
      alignment: Alignment.centerLeft,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text(
            headerText,
            style: const TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
          ),
          Padding(
            padding: const EdgeInsets.only(right: 20, bottom: 25),
            child: Text(
              descriptionText,
              style: const TextStyle(fontSize: 14),
              maxLines: 4,
              overflow: TextOverflow.ellipsis,
            ),
          ),
          Text(scheduledDate, style: const TextStyle(fontSize: 17)),
        ],
      ),
    ),
  );
}
```

- **Descrição**: Constrói o layout do cartão de tarefa.
- **Estrutura**:
  - **`Container`**:
    - Define o layout principal do cartão.
    - Aplica margens, preenchimento e uma cor de fundo.
    - Adiciona bordas arredondadas com `BorderRadius.circular(15)`.
  - **`Column`**:
    - Organiza os elementos do cartão verticalmente.
    - Contém o título, descrição e data da tarefa.
  - **`Text`**:
    - Exibe o título (`headerText`) com estilo em negrito e tamanho 20.
    - Exibe a descrição (`descriptionText`) com tamanho 14, limitado a 4 linhas e com `TextOverflow.ellipsis` para cortar o texto excedente.
    - Exibe a data (`scheduledDate`) com tamanho 17.

---

## Resumo

O widget `TaskCard` é usado para exibir informações de uma tarefa de forma estilizada. Ele oferece as seguintes funcionalidades:

1. **Título**: Exibe o título da tarefa em destaque.
2. **Descrição**: Exibe a descrição da tarefa com limite de 4 linhas.
3. **Data**: Exibe a data agendada da tarefa no rodapé.
4. **Personalização**: Permite definir a cor de fundo do cartão.

Este widget é ideal para ser usado em listas de tarefas ou em interfaces que exibem informações organizadas de forma visualmente atraente.

# Documentação do Código: `date_selector.dart`

Este arquivo implementa o widget `DateSelector`, que permite ao usuário navegar entre semanas e selecionar uma data específica. Ele exibe os dias da semana atual ou de semanas anteriores/posteriores, destacando a data selecionada.

---

## Importações

- **`flutter/material.dart`**: Biblioteca principal do Flutter para construção de interfaces.
- **`intl/intl.dart`**: Biblioteca para formatação de datas.

---

## Classe `DateSelector`

A classe `DateSelector` é um widget com estado (`StatefulWidget`) que gerencia a seleção de datas e a navegação entre semanas.

### Construtor

```dart
const DateSelector({super.key});
```

- **Descrição**: Construtor padrão da classe `DateSelector`.

---

## Classe `_DateSelectorState`

A classe de estado que gerencia a lógica e a interface do widget `DateSelector`.

### Propriedades

- **`selectedDate`**: A data atualmente selecionada pelo usuário (inicialmente definida como a data atual).
- **`weekOffset`**: Um inteiro que representa o deslocamento da semana atual (0 para a semana atual, -1 para a semana anterior, 1 para a próxima semana, etc.).

---

### Método `generateWeekDates`

```dart
List<DateTime> generateWeekDates(int weekOffset) {
  final today = DateTime.now();
  DateTime startOfWeek = today.subtract(Duration(days: today.weekday - 1));
  startOfWeek = startOfWeek.add(Duration(days: weekOffset * 7));
  return List.generate(7, (index) => startOfWeek.add(Duration(days: index)));
}
```

- **Descrição**: Gera uma lista de datas para a semana correspondente ao deslocamento (`weekOffset`).
- **Parâmetros**:
  - `weekOffset`: O deslocamento da semana em relação à semana atual.
- **Retorno**: Uma lista de 7 objetos `DateTime`, representando os dias da semana.

---

### Método `build`

```dart
@override
Widget build(BuildContext context) {
  List<DateTime> weekDates = generateWeekDates(weekOffset);
  String monthName = DateFormat('MMMM').format(weekDates.first);

  return Column(
    children: [
      Padding(
        padding: const EdgeInsets.symmetric(horizontal: 16.0).copyWith(bottom: 10.0),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            IconButton(
              icon: const Icon(Icons.arrow_back_ios),
              onPressed: () {
                setState(() {
                  weekOffset--;
                });
              },
            ),
            Text(
              monthName,
              style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            IconButton(
              icon: const Icon(Icons.arrow_forward_ios),
              onPressed: () {
                setState(() {
                  weekOffset++;
                });
              },
            ),
          ],
        ),
      ),
      Padding(
        padding: const EdgeInsets.symmetric(horizontal: 16.0),
        child: SizedBox(
          height: 80,
          child: ListView.builder(
            scrollDirection: Axis.horizontal,
            itemCount: weekDates.length,
            itemBuilder: (context, index) {
              DateTime date = weekDates[index];
              bool isSelected = DateFormat('d').format(selectedDate) == DateFormat('d').format(date) &&
                  selectedDate.month == date.month &&
                  selectedDate.year == date.year;

              return GestureDetector(
                onTap: () {
                  setState(() {
                    selectedDate = date;
                  });
                },
                child: Container(
                  width: 70,
                  margin: const EdgeInsets.only(right: 8),
                  decoration: BoxDecoration(
                    color: isSelected ? Colors.deepOrangeAccent : Colors.transparent,
                    borderRadius: BorderRadius.circular(10),
                    border: Border.all(
                      color: isSelected ? Colors.deepOrangeAccent : Colors.grey.shade300,
                      width: 2,
                    ),
                  ),
                  child: Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: [
                      Text(
                        DateFormat('d').format(date),
                        style: TextStyle(
                          color: isSelected ? Colors.white : Colors.black87,
                          fontSize: 22,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                      const SizedBox(height: 3),
                      Text(
                        DateFormat('E').format(date),
                        style: TextStyle(
                          color: isSelected ? Colors.white : Colors.black87,
                          fontSize: 16,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                    ],
                  ),
                ),
              );
            },
          ),
        ),
      ),
    ],
  );
}
```

- **Descrição**: Constrói a interface do widget `DateSelector`.
- **Estrutura**:
  - **`Row`**:
    - Contém botões para navegar entre semanas (`IconButton`) e o nome do mês atual.
  - **`ListView.builder`**:
    - Exibe os dias da semana em um formato horizontal.
    - Cada dia é representado por um `Container` que destaca a data selecionada.

---

## Resumo

O widget `DateSelector` oferece as seguintes funcionalidades:

1. **Navegação entre Semanas**:
   - Permite ao usuário navegar para semanas anteriores ou posteriores usando botões de seta.
2. **Seleção de Data**:
   - Permite ao usuário selecionar uma data específica dentro da semana exibida.
   - Destaca a data selecionada com uma cor de fundo personalizada.
3. **Exibição de Datas**:
   - Exibe os dias da semana em um formato horizontal, com o dia do mês e o nome abreviado do dia da semana.

Este widget é ideal para aplicativos que precisam de uma interface intuitiva para seleção de datas, como gerenciadores de tarefas ou calendários.