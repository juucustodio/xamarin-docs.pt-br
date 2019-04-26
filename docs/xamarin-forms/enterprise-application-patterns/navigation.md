---
title: Navegação de aplicativo empresarial
description: Este capítulo explica como o aplicativo móvel do eShopOnContainers executa a navegação de model first de modo de exibição de modelos de exibição.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d306b0c1c0d08129671e27b96911ec771acb658e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298915"
---
# <a name="enterprise-app-navigation"></a>Navegação de aplicativo empresarial

Xamarin. Forms inclui suporte para navegação de página, que geralmente resulta da interação do usuário com a interface do usuário ou do próprio aplicativo como resultado das alterações de estado interno controlado por lógica. No entanto, a navegação pode ser complexa para implementar em aplicativos que usam o padrão Model-View-ViewModel (MVVM), como os seguintes desafios que devem ser atendidos:

-   Como identificar o modo de exibição para ser navegado, usando uma abordagem que não introduz acoplamentos rígidos e as dependências entre os modos de exibição.
-   Como coordenar o processo pelo qual o modo de exibição para ser navegado para é instanciado e inicializado. Ao usar o MVVM, a exibição e o modelo de exibição precisam ser instanciado e associado uns aos outros por meio do contexto de associação do modo de exibição. Quando um aplicativo estiver usando um contêiner de injeção de dependência, a instanciação de modos de exibição e modelos de exibição pode exigir um mecanismo de construção específico.
-   Se deseja executar primeiro a exibição de navegação ou exibir o primeiro modelo de navegação. Com o primeiro modo de exibição de navegação, a página para navegar até refere-se ao nome do tipo de exibição. Durante a navegação, a exibição especificada é instanciada, junto com seu modelo de exibição correspondente e outros serviços dependentes. Uma abordagem alternativa é usar a navegação do modo de exibição model first, onde a página para navegar até refere-se ao nome do tipo de modelo de exibição.
-   Como a claramente separadas o comportamento de navegação do aplicativo entre os modos de exibição e modelos de exibição. O padrão MVVM fornece uma separação entre a interface do usuário do aplicativo e a apresentação e lógica de negócios. No entanto, o comportamento de navegação de um aplicativo geralmente abrangerá as partes da interface do usuário e apresentações do aplicativo. O usuário geralmente será iniciar a navegação de um modo de exibição e o modo de exibição será substituído em resultado da navegação. No entanto, a navegação geralmente também pode precisar ser iniciado ou coordenada de dentro do modelo de exibição.
-   Como passar parâmetros durante a navegação para fins de inicialização. Por exemplo, se o usuário navega para uma exibição para atualizar os detalhes do pedido, terá os dados do pedido a serem passados para o modo de exibição para que ele possa exibir os dados corretos.
-   Como a navegação de coordenada, para garantir que certas regras de negócio são obedecidas. Por exemplo, os usuários podem ser solicitados antes de navegar para fora de um modo de exibição para que eles podem corrigir todos os dados inválidos ou ser solicitados a enviar ou descartar qualquer alteração de dados que foram feitas no modo de exibição.

Este capítulo aborda a esses desafios, apresentando um `NavigationService` classe que é usado para executar a navegação de página do primeiro modelo de exibição.

> [!NOTE]
> O `NavigationService` usado pelo aplicativo foi projetado somente para executar a navegação hierárquica entre instâncias do ContentPage. Usando o serviço para navegar entre os outros tipos de página pode resultar em comportamento inesperado.

## <a name="navigating-between-pages"></a>Navegar entre páginas

Lógica de navegação pode residir no code-behind do modo de exibição, ou em dados vinculados modelo de exibição. Ao colocar a lógica de navegação em um modo de exibição pode ser a abordagem mais simples, não é facilmente testável por meio de testes de unidade. Colocar a lógica de navegação no modo de exibição classes de modelo significa que a lógica pode ser exercida por meio de testes de unidade. Além disso, o modelo de exibição, em seguida, pode implementar a lógica para controlar a navegação para assegurar que determinadas regras de negócio são aplicadas. Por exemplo, um aplicativo pode não permitir que o usuário navegar para fora de uma página sem primeiro garantir que os dados inseridos são válidos.

Um `NavigationService` classe é geralmente invocado em modelos de exibição, para promover a possibilidade de teste. No entanto, navegar para modos de exibição de modelos de exibição exigiria que os modelos de exibição para modos de exibição de referência e especialmente a modos de exibição que o modelo de exibição do Active Directory não está associado, que não é recomendado. Portanto, o `NavigationService` apresentada aqui Especifica o tipo de modelo de exibição como o destino para navegar para.

O aplicativo móvel de eShopOnContainers usa a `NavigationService` classe para fornecer uma exibição model first navegação. Essa classe implementa o `INavigationService` interface, que é mostrado no exemplo de código a seguir:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Essa interface Especifica que uma classe de implementação deve fornecer os seguintes métodos:

|Método|Finalidade|
|--- |--- |
|`InitializeAsync`|Executa a navegação para uma das duas páginas quando o aplicativo é iniciado.|
|`NavigateToAsync`|Executa a navegação hierárquica para uma página especificada.|
|`NavigateToAsync(parameter)`|Executa a navegação hierárquica para uma página especificada, passando um parâmetro.|
|`RemoveLastFromBackStackAsync`|Remove a página anterior da pilha de navegação.|
|`RemoveBackStackAsync`|Remove todas as páginas anteriores da pilha de navegação.|

Além disso, o `INavigationService` interface Especifica que uma classe de implementação deve fornecer um `PreviousPageViewModel` propriedade. Essa propriedade retorna o tipo de modelo de exibição associado à página anterior na pilha de navegação.

> [!NOTE]
> Uma `INavigationService` interface seria normalmente também especificar um `GoBackAsync` método, que é usado para retornar programaticamente à página anterior na pilha de navegação. No entanto, esse método está ausente do aplicativo móvel do eShopOnContainers, porque ele não é necessário.

### <a name="creating-the-navigationservice-instance"></a>Criação da instância de NavigationService

O `NavigationService` de classe que implementa o `INavigationService` interface, é registrado como um singleton com o contêiner de injeção de dependência do Autofac, conforme demonstrado no exemplo de código a seguir:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

O `INavigationService` interface é resolvido no `ViewModelBase` construtor de classe, conforme demonstrado no exemplo de código a seguir:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Isso retorna uma referência para o `NavigationService` objeto que é armazenado no contêiner de injeção de dependência do Autofac, que é criado pelo `InitNavigation` método no `App` classe. Para obter mais informações, consulte [navegar quando o aplicativo é iniciado](#navigating_when_the_app_is_launched).

O `ViewModelBase` classe armazena o `NavigationService` instância em um `NavigationService` propriedade do tipo `INavigationService`. Portanto, todos os classes de modelo, que derivam de exibição a `ViewModelBase` classe, pode usar o `NavigationService` propriedade para acessar os métodos especificados pelo `INavigationService` interface. Isso evita a sobrecarga de injetar o `NavigationService` objeto do contêiner de injeção de dependência Autofac em cada classe de modelo de exibição.

### <a name="handling-navigation-requests"></a>Tratamento de solicitações de navegação

Xamarin. Forms fornece o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe, que implementa uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas de frente e para trás, conforme desejado. Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Em vez de usar o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe diretamente, a disposição do aplicativo eShopOnContainers o `NavigationPage` classe o `CustomNavigationView` de classe, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

A finalidade dessa disposição é a facilidade de estilo a [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância dentro do arquivo XAML para a classe.

Navegação é executada dentro de classes de modelo de exibição, invocando um do `NavigateToAsync` métodos, especificando o tipo de modelo de exibição para a página que está sendo navegado a, conforme demonstrado no exemplo de código a seguir:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

O seguinte exemplo de código mostra a `NavigateToAsync` métodos fornecidos pelo `NavigationService` classe:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Cada método permite que qualquer classe de modelo de exibição que deriva de `ViewModelBase` classe para executar a navegação hierárquica, invocando o `InternalNavigateToAsync` método. Além disso, o segundo `NavigateToAsync` método permite que os dados de navegação seja especificado como um argumento que é passado para o modelo de exibição está navegando, em que ele normalmente é usado para executar a inicialização. Para obter mais informações, consulte [passando parâmetros durante a navegação](#passing_parameters_during_navigation).

O `InternalNavigateToAsync` método executa a solicitação de navegação e é mostrado no exemplo de código a seguir:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

O `InternalNavigateToAsync` método executa a navegação para um modelo de exibição pelo primeiro chamar o `CreatePage` método. Esse método localiza a exibição que corresponde ao tipo de modelo de exibição especificada e cria e retorna uma instância desse tipo de exibição. Localizar o modo de exibição que corresponde ao tipo de modelo de exibição usa uma abordagem baseada em convenção, que pressupõe que:

-   Exibições estão no mesmo assembly como tipos de modelo de exibição.
-   Modos de exibição estão em um. Namespace de filho de modos de exibição.
-   Modelos de exibição estão em um. Namespace de filho de ViewModels.
-   Correspondem nomes de exibição para exibir nomes de modelo, com "Modelo" removido.

Quando uma exibição é instanciada, ela é associada a seu modelo de exibição correspondente. Para obter mais informações sobre como isso ocorre, consulte [criando automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Se o modo de exibição que está sendo criado é uma `LoginView`, ele é encapsulado dentro de uma nova instância da `CustomNavigationView` classe e atribuído ao [ `Application.Current.MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriedade. Caso contrário, o `CustomNavigationView` instância é recuperada e fornecida que não é nulo, o [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage) método é invocado para enviar por push o modo de exibição que está sendo criado para a pilha de navegação. No entanto, se recuperada `CustomNavigationView` instância está `null`, o modo de exibição que está sendo criado é encapsulado dentro de uma nova instância das `CustomNavigationView` classe e atribuído ao `Application.Current.MainPage` propriedade. Esse mecanismo garante que durante a navegação, as páginas são adicionadas corretamente para a pilha de navegação quando ela está vazia, e quando ele contém dados.

> [!TIP]
> Considere a possibilidade de cache de páginas. Cache de página resulta no consumo de memória para as exibições que não são exibidos no momento. No entanto, sem cache de página significa que a análise de XAML e a construção da página e seu modelo de exibição ocorrerá sempre que uma nova página é navegada, que pode ter um impacto no desempenho para uma página complexa. Uma página bem projetada que não usa um número excessivo de controles, o desempenho deve ser suficiente. No entanto, o cache de página pode ajudar se tempos de carregamento de página lentos forem encontrados.

Depois que o modo de exibição é criado e navegado, o `InitializeAsync` método associada da exibição modelo de exibição é executado. Para obter mais informações, consulte [passando parâmetros durante a navegação](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navegar quando o aplicativo é iniciado

Quando o aplicativo é iniciado, o `InitNavigation` método no `App` classe é invocado. O seguinte exemplo de código mostra esse método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

O método cria uma nova `NavigationService` objeto no contêiner de injeção de dependência de Autofac e retorna uma referência a ele, antes de invocar seu `InitializeAsync` método.

> [!NOTE]
> Quando o `INavigationService` interface é resolvido com o `ViewModelBase` classe, o contêiner retornará uma referência ao `NavigationService` objeto que foi criado quando o método InitNavigation é invocado.

O seguinte exemplo de código mostra a `NavigationService` `InitializeAsync` método:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

O `MainView` é navegada se o aplicativo tem um token de acesso armazenados em cache, que é usado para autenticação. Caso contrário, o `LoginView` é navegada.

Para obter mais informações sobre o contêiner de injeção de dependência do Autofac, consulte [Introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Passando parâmetros durante a navegação

Um dos `NavigateToAsync` métodos, especificados pelo `INavigationService` de interface, os dados de navegação permite que seja especificado como um argumento que é passado para o modelo de exibição está navegando, em que ele normalmente é usado para executar a inicialização.

Por exemplo, o `ProfileViewModel` classe contém uma `OrderDetailCommand` que é executado quando o usuário seleciona um pedido no `ProfileView` página. Por sua vez, isso executa o `OrderDetailAsync` método, que é mostrado no exemplo de código a seguir:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Este método invoca a navegação para o `OrderDetailViewModel`, passando um `Order` que representa a ordem em que o usuário selecionou na instância a `ProfileView` página. Quando o `NavigationService` classe cria o `OrderDetailView`, o `OrderDetailViewModel` classe é instanciado e atribuído para o modo de exibição [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Depois de navegar até a `OrderDetailView`, o `InternalNavigateToAsync` método executa o `InitializeAsync` método da exibição associada ao modelo de exibição.

O `InitializeAsync` método é definido no `ViewModelBase` classe como um método que pode ser substituído. Esse método Especifica um `object` argumento que representa os dados a serem passados para um modelo de exibição durante uma operação de navegação. Portanto, as classes de modelo de exibição que deseja receber os dados de uma operação de navegação fornecem sua própria implementação do `InitializeAsync` método para executar a inicialização necessária. O seguinte exemplo de código mostra a `InitializeAsync` método a partir de `OrderDetailViewModel` classe:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Esse método recupera o `Order` de detalhes da instância que foi passada para o modelo de exibição durante a operação de navegação e o utiliza para recuperar o pedido completo a `OrderService` instância.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Usando comportamentos de navegação invocando

Navegação normalmente é disparada em uma exibição por uma interação do usuário. Por exemplo, o `LoginView` executa a navegação após a autenticação bem-sucedida. O exemplo de código a seguir mostra como a navegação é invocada por um comportamento:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

Em tempo de execução, o `EventToCommandBehavior` responderá a interação com o [ `WebView` ](xref:Xamarin.Forms.WebView). Quando o `WebView` navega para uma página da web, o [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating) evento será acionado, o qual será executada a `NavigateCommand` no `LoginViewModel`. Por padrão, os argumentos do evento para o evento são passados para o comando. Esses dados são convertidos conforme ele é passado entre origem e destino pelo conversor especificado na `EventArgsConverter` propriedade, que retorna o [ `Url` ](xref:Xamarin.Forms.WebNavigationEventArgs.Url) do [ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs). Portanto, quando o `NavigationCommand` é executado, a Url da página da web é passado como um parâmetro ao nome registrado `Action`.

Por sua vez, o `NavigationCommand` executa o `NavigateAsync` método, que é mostrado no exemplo de código a seguir:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Este método invoca a navegação para o `MainViewModel`, e o painel de navegação, a seguir remove o `LoginView` página da pilha de navegação.

### <a name="confirming-or-cancelling-navigation"></a>Confirmar ou cancelar a navegação

Um aplicativo pode precisar interagir com o usuário durante uma operação de navegação, para que o usuário possa confirmar ou cancelar a navegação. Isso pode ser necessário, por exemplo, quando o usuário tenta navegar antes totalmente com a conclusão de uma página de entrada de dados. Nessa situação, um aplicativo deve fornecer uma notificação que permite ao usuário navegar para fora da página, ou para cancelar a operação de navegação antes que ele ocorra. Isso pode ser obtido em uma classe de modelo de exibição usando a resposta de uma notificação para controlar ou não a navegação é invocada.

## <a name="summary"></a>Resumo

Xamarin. Forms inclui suporte para navegação de página, que geralmente resulta da interação do usuário com a interface do usuário ou do aplicativo em si, como resultado das alterações de estado interno controlado por lógica. No entanto, a navegação pode ser complexa para implementar em aplicativos que usam o padrão MVVM.

Este capítulo apresentado uma `NavigationService` classe, que é usado para executar a navegação de model first de modo de exibição de modelos de exibição. Colocar a lógica de navegação no modo de exibição classes de modelo significa que a lógica pode ser exercida por meio de testes automatizados. Além disso, o modelo de exibição, em seguida, pode implementar a lógica para controlar a navegação para assegurar que determinadas regras de negócio são aplicadas.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
