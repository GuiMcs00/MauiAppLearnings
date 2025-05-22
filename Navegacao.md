# Navegação entre Páginas no MAUI

Este documento descreve como funciona a navegação entre páginas no .NET MAUI, com foco no uso do `Shell`, passagem de parâmetros e manipulação da pilha de navegação.

---

## 📌 Estrutura básica de navegação com Shell

O MAUI utiliza o `Shell` como componente principal de navegação. O `AppShell.xaml` define as rotas e o comportamento das páginas no app.

### Exemplo básico de `AppShell.xaml`:

```xml
<Shell xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:MeuApp.Views"
       x:Class="MeuApp.AppShell">

    <ShellContent Title="Início" ContentTemplate="{DataTemplate views:HomePage}" />
    <ShellContent Title="Perfil" ContentTemplate="{DataTemplate views:PerfilPage}" />
</Shell>
```

### Registro de rotas no `AppShell.xaml.cs`:

```csharp
Routing.RegisterRoute(nameof(DetalhesPage), typeof(DetalhesPage));
Routing.RegisterRoute("configuracoes", typeof(ConfiguracoesPage));
```

---

## 🔀 Navegar para outra página

### Por nome da rota:

```csharp
await Shell.Current.GoToAsync(nameof(DetalhesPage));
```

### Por rota declarada como string:

```csharp
await Shell.Current.GoToAsync("configuracoes");
```

---

## 🧳 Passagem de parâmetros

### Enviando parâmetros:

```csharp
await Shell.Current.GoToAsync($"{nameof(DetalhesPage)}?id=123&nome=Lucio");
```

### Recebendo com `[QueryProperty]`:

```csharp
[QueryProperty("Id", "id")]
[QueryProperty("Nome", "nome")]
public partial class DetalhesPage : ContentPage
{
    public string Id { get; set; }
    public string Nome { get; set; }
}
```

---

## 📍 Acessar e controlar a página atual

### Obter a página atual:

```csharp
var paginaAtual = Shell.Current.CurrentPage;
```

### Voltar à página anterior:

```csharp
await Shell.Current.GoToAsync("..");
```

### Redefinir a pilha e ir para a raiz:

```csharp
Shell.Current.Navigation.InsertPageBefore(new MainPage(), Shell.Current.CurrentPage);
await Shell.Current.Navigation.PopAsync();
```

---

## 🧠 Boas práticas

- Use `nameof()` sempre que possível para evitar erros de string.
- Prefira registrar as rotas no `AppShell.xaml.cs` para organização.
- Evite usar `Navigation.PushAsync()` quando estiver usando `Shell`, para manter consistência.
- Para passagem complexa de dados, prefira usar um serviço de navegação com estado temporário.

---

## 📚 Fontes recomendadas

- [Shell Navigation - .NET MAUI docs](https://learn.microsoft.com/dotnet/maui/fundamentals/shell/navigation)
