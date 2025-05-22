# XAML Básico no MAUI

Este documento apresenta os fundamentos do XAML no desenvolvimento de apps com MAUI, explicando sua função, estrutura, sintaxe e como conectar a interface com a lógica da aplicação (ViewModel).

---

## 🧾 O que é XAML?

**XAML (eXtensible Application Markup Language)** é uma linguagem baseada em XML usada para definir a interface gráfica do usuário (UI) em aplicativos MAUI.

### Benefícios:

- Separação clara entre lógica e interface (MVVM).
- Suporte a visual designers.
- Facilita o reaproveitamento e manutenção de código.

---

## 🧱 Estrutura de um arquivo XAML

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MeuApp.Views.LoginPage">
    <VerticalStackLayout>
        <Label Text="Bem-vindo!" FontSize="24" />
        <Entry Placeholder="E-mail" Text="{Binding Email}" />
        <Button Text="Entrar" Command="{Binding EntrarCommand}" />
    </VerticalStackLayout>
</ContentPage>
```

### Explicação:

- `xmlns` → define os namespaces padrão do MAUI e do XAML.
- `x:Class` → associa o XAML ao code-behind.
- `VerticalStackLayout` → organiza os elementos verticalmente.
- `{Binding ...}` → conecta propriedades da ViewModel à interface.

---

## 🔗 Conectando XAML à ViewModel

### Definindo o BindingContext

No code-behind:

```csharp
public partial class LoginPage : ContentPage
{
    public LoginPage(LoginViewModel vm)
    {
        InitializeComponent();
        BindingContext = vm;
    }
}
```

Ou automaticamente via Injeção de Dependência (registrando em `MauiProgram.cs`).

---

## 🔎 Referenciando elementos no XAML

### Com `x:Name`

```xml
<Entry x:Name="EmailEntry" Placeholder="E-mail" />
```

Acessando no `.xaml.cs`:

```csharp
EmailEntry.Text = "teste@exemplo.com";
```

### Quando usar:

- Casos pontuais que não envolvem binding.
- Manipulação direta de elementos.

---

## 🧩 Bindings mais comuns

[Data Binding no .NET MAUI](https://learn.microsoft.com/dotnet/maui/fundamentals/data-binding/)

### Binding a propriedades:

```xml
<Entry Text="{Binding Email}" />
```

### Binding a comandos:

```xml
<Button Text="Entrar" Command="{Binding EntrarCommand}" />
```

### Two-way Binding (leitura e escrita):

```xml
<Entry Text="{Binding Email, Mode=TwoWay}" />
```

---

## 🎨 Estilos e Recursos

### Definindo um estilo global em `App.xaml`:

```xml
<Application.Resources>
    <Style TargetType="Label">
        <Setter Property="TextColor" Value="Black" />
        <Setter Property="FontSize" Value="16" />
    </Style>
</Application.Resources>
```

### Utilizando `StaticResource`:

```xml
<Label Text="Olá" TextColor="{StaticResource PrimaryColor}" />
```

---

## 🧠 Boas práticas

- Prefira Binding sempre que possível (em vez de `x:Name`).
- Use estilos e recursos compartilhados para consistência visual.
- Mantenha a View o mais "burra" possível — sem lógica.
- Crie `ContentViews` para seções reutilizáveis e mantenha o XAML modular.

---

Próximos tópicos relacionados: [Arquitetura MVVM](02_arquitetura_mvvm.md) | [Navegação e Ciclo de Vida](01_estrutura_geral.md)
