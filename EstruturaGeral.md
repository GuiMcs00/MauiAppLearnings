# Estrutura Geral do Projeto MAUI

Este documento descreve a estrutura básica de um projeto MAUI (Multi-platform App UI), abordando os principais componentes que formam o alicerce de desenvolvimento de um aplicativo.

---

## 🗂️ Estrutura típica de pastas no projeto MAUI

```
MeuApp/
├── Views/             # Telas do app (ContentPages)
├── Components/        # ContentViews reutilizáveis
├── ViewModels/        # Lógica de apresentação
├── Models/            # Classes de dados / DTOs
├── Services/          # Comunicação com API, utilitários
├── Resources/
│   ├── Fonts/
│   ├── Images/
├── AppShell.xaml      # Define a navegação principal
├── App.xaml           # Recursos globais, estilos
└── MauiProgram.cs     # Configurações iniciais e DI
```

---

## 🧠 Arquitetura MVVM e Responsabilidades das Camadas

A arquitetura **MVVM (Model-View-ViewModel)** ajuda a separar responsabilidades e facilitar testes, reutilização e manutenção.

### 🔹 View (Views/ e Components/)

- Responsável pela **interface visual**.
- Declarada com XAML.
- Liga-se à ViewModel por meio de **Bindings**.
- Não deve conter lógica de negócio.

### 🔹 ViewModel (ViewModels/)

- Contém a lógica da interface: comandos, navegação, controle de estado.
- Propriedades são implementadas com `INotifyPropertyChanged`.
- Recebe serviços por injeção de dependência (ex: `IApiService`).

```csharp
public class LoginViewModel : BaseViewModel
{
    public string Email { get; set; }
    public ICommand EntrarCommand { get; set; }
}
```

### 🔹 Model (Models/)

- Representa os dados da aplicação (DTOs, entidades).
- Sem lógica de apresentação.
- Pode incluir validações simples ou regras de negócio puras.

```csharp
public class Usuario
{
    public string Nome { get; set; }
    public string Email { get; set; }
}
```

### 🔹 Services (Services/)

- Centralizam lógica de comunicação com APIs, persistência local, etc.
- Facilitam reutilização e testes.

---

## 📌 Outras Estruturas Fundamentais

### AppShell.xaml

- Define a **estrutura de navegação** do app.
- Pode usar rotas, `Flyout`, `TabbedPage`, etc.

```xml
<Shell>
    <ShellContent Title="Início" ContentTemplate="{DataTemplate local:HomePage}" />
</Shell>
```

### App.xaml e App.xaml.cs

- `App.xaml`: define **recursos globais**, estilos, cores, fontes.
- `App.xaml.cs`: ponto de entrada da aplicação, define a `MainPage`.

### MauiProgram.cs

- Configuração inicial da aplicação.
- Onde os serviços são registrados para injeção de dependência:

```csharp
builder.Services.AddTransient<LoginPage>();
builder.Services.AddTransient<LoginViewModel>();
```

---

## 🧩 Componentes do MAUI

### 📄 ContentPage

- Representa uma **tela completa** no aplicativo.
- Geralmente é usada como destino de navegação.
- Exemplo: tela de login, dashboard, perfil.

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MeuApp.Views.LoginPage">
    <!-- UI aqui -->
</ContentPage>
```

### 📦 ContentView

- Representa um **componente reutilizável de interface**.
- Utilizada dentro de ContentPages ou outras ContentViews.
- Ideal para seções reaproveitáveis, como cards, listas, botões customizados.

```xml
<ContentView xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MeuApp.Components.CardView">
    <!-- UI do componente -->
</ContentView>
```

### 🔗 Conexão entre os componentes

- Cada `ContentPage` tem uma `ViewModel` associada.
- O binding é feito via `BindingContext`, geralmente no code-behind ou por DI.
- As `Views` se comunicam com a lógica através de `Bindings` no XAML.
- Outros componentes comuns:

  - **CollectionView**: para exibição de listas de dados com layout flexível.
  - **Shell**: componente base que define a navegação principal do app.
  - **ImageButton / Frame / StackLayout**: elementos visuais usados para estruturar a UI.
  - **Launcher**: utilizado para abrir links externos.

---

## 🔄 Ciclo de Vida e Navegação

O ciclo de vida de uma página no MAUI define os momentos em que você pode executar lógica relacionada à exibição, inicialização ou saída da tela. A navegação entre páginas geralmente é gerenciada pelo Shell, que simplifica o roteamento e o controle da pilha de navegação.

[App lifecycle reference](https://learn.microsoft.com/en-us/dotnet/maui/fundamentals/app-lifecycle?view=net-maui-9.0)

### Principais métodos de ciclo de vida

#### `OnAppearing()`

- Método sobrescrito nas `ContentPages`.
- Executado quando a página **entra em exibição novamente**.
- Útil para atualizar dados ou iniciar escutas de eventos.

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();
    // lógica de carregamento
}
```

#### `OnDisappearing()`

- Chamado quando a página **está prestes a sair de foco**.
- Útil para limpar recursos, parar timers, ou desconectar eventos.

### Ciclo de vida na ViewModel

- Com o uso do Shell, pode-se empregar o padrão `INavigationAware` (de bibliotecas como Prism) ou acionar métodos manualmente via eventos de navegação.
- Alternativas personalizadas incluem:

  - Criar um método público como `OnNavigatedTo()` e chamá-lo ao injetar a ViewModel.

---

### Navegação com o Shell

#### Registrando rotas

No `AppShell.xaml.cs`:

```csharp
Routing.RegisterRoute(nameof(DetalhesPage), typeof(DetalhesPage));
```

#### Navegando com parâmetros:

```csharp
await Shell.Current.GoToAsync($"{nameof(DetalhesPage)}?id=123");
```

#### Recebendo parâmetros na página:

```csharp
[QueryProperty("Id", "id")]
public partial class DetalhesPage : ContentPage
{
    public string Id { get; set; }
}
```

---

### Obtendo a página ativa

Para acessar a página atual no contexto do `Shell`:

```csharp
var paginaAtual = Shell.Current.CurrentPage;
```

### Manipulando a pilha de navegação

- Para voltar à página anterior:

```csharp
await Shell.Current.GoToAsync("..")
```

- Para remover tudo e ir à tela principal:

```csharp
Shell.Current.Navigation.InsertPageBefore(new MainPage(), Shell.Current.CurrentPage);
await Shell.Current.Navigation.PopAsync();
```

Esses mecanismos permitem controle mais refinado da experiência de navegação e facilitam a transição entre estados no app, mantendo a organização e previsibilidade do fluxo de telas.

- Funções como `OnAppearing()`, `OnNavigatedTo()` são usadas para carregar dados ou atualizar a interface quando a página aparece.
- Navegação pode ser feita via `Shell.Current.GoToAsync("/rota")`.

---

## ♻️ Reutilização e escalabilidade

- Use `ContentView` para modularizar a interface e evitar duplicação.
- Organize as pastas de forma clara para facilitar manutenção.
- A arquitetura MVVM proporciona separação de responsabilidades e facilita testes.

---

Próximos tópicos relacionados: [Navegação entre páginas](04_navegacao_paginas.md) | [Injeção de Dependência](08_injecao_dependencia_servicos.md)
