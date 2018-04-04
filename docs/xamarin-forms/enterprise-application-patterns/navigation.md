---
title: Navegação
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: aa2e2858e3bb8e435ec3f38bb3d5b249eaa6cba4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="navigation"></a>Navegação

Xamarin. Forms inclui suporte para a navegação de página, que geralmente resulta de interação do usuário com a interface do usuário ou do aplicativo como resultado das alterações de estado interno controlada por lógica. No entanto, navegação pode ser complexa para implementar em aplicativos que usam o padrão Model-View-ViewModel (MVVM), que devem ser atendidos os seguintes desafios:

-   Como identificar o modo de exibição para ser navegado, usando uma abordagem que não apresenta um acoplamento e as dependências entre os modos de exibição.
-   Como coordenar o processo pelo qual o modo de exibição de navegação é instanciado e inicializado. Ao usar o modelo MVVM, a exibição e o modelo de exibição precisam ser instanciado e associado a outro por meio do contexto de associação do modo de exibição. Quando um aplicativo está usando um contêiner de injeção de dependência, a instanciação de modos de exibição e modelos de exibição pode exigir um mecanismo de construção específico.
-   Se deseja executar primeiro a exibição de navegação ou exibir o primeiro modelo de navegação. Com o primeiro modo de exibição de navegação, para navegar até a página refere-se ao nome do tipo de exibição. Durante a navegação, a exibição especificada é instanciada, juntamente com seu modelo de exibição correspondente e outros serviços dependentes. Uma abordagem alternativa é usar o primeiro modelo de navegação de exibição, onde para navegar até a página se refere ao nome do tipo de modelo de exibição.
-   Como a corretamente separar o comportamento de navegação do aplicativo entre os modos de exibição e modelos de exibição. O padrão MVVM fornece uma separação entre a interface de usuário do aplicativo e a apresentação e a lógica de negócios. No entanto, o comportamento de navegação de um aplicativo será geralmente abrangem as partes da interface do usuário e apresentações do aplicativo. O usuário geralmente irá iniciar a navegação de um modo de exibição e o modo de exibição será substituído como resultado da navegação. No entanto, navegação geralmente também precisará ser iniciado ou coordenada de dentro do modelo de exibição.
-   Como passar parâmetros durante a navegação para fins de inicialização. Por exemplo, se o usuário navega para um modo de exibição para atualizar os detalhes do pedido, terá os dados da ordem a ser passado para o modo de exibição para que ele possa exibir os dados corretos.
-   A navegação de coordenar, para garantir que são obedecidas a certas regras de negócio. Por exemplo, os usuários serão solicitados antes de navegar para fora de um modo de exibição para que possa corrigir todos os dados inválidos ou ser solicitados a enviar ou descartar qualquer alteração de dados que foram feitas no modo de exibição.

Este capítulo aborda esses desafios apresentando um `NavigationService` classe que é usada para executar a navegação de página do primeiro modelo de exibição.

> [!NOTE]
> O `NavigationService` usado pelo aplicativo destina-se somente para executar a navegação hierárquica entre instâncias de ContentPage. Usando o serviço para navegar entre outros tipos de página pode resultar em comportamento inesperado.

## <a name="navigating-between-pages"></a>Navegar entre páginas

Lógica de navegação pode residir no code-behind do modo de exibição, ou em dados vinculados modelo de exibição. Ao colocar a lógica de navegação em um modo de exibição pode ser a abordagem mais simples, não é facilmente testável por meio de testes de unidade. Colocar a lógica de navegação no modo de exibição classes de modelo significa que a lógica pode ser desempenhada por meio de testes de unidade. Além disso, o modelo de exibição, em seguida, pode implementar a lógica para controlar a navegação para assegurar que certas regras de negócio são aplicadas. Por exemplo, um aplicativo pode não permitir que o usuário navegar para fora de uma página sem primeiro assegurar que os dados inseridos são válidos.

Um `NavigationService` classe é geralmente chamada de modelos de exibição, para promover a capacidade de teste. No entanto, navegando para modos de exibição de modelos de exibição requer que os modelos de exibição para modos de exibição de referência e particularmente modos de exibição do modelo de exibição ativo não associado, que não é recomendado. Portanto, o `NavigationService` apresentados aqui Especifica o tipo de modelo de exibição como o destino para navegar até.

Os usos de aplicativo móvel eShopOnContainers o `NavigationService` classe para fornecer uma navegação de primeiro do modelo de exibição. Essa classe implementa o `INavigationService` interface, que é mostrado no exemplo de código a seguir:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
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
> Um `INavigationService` interface faria normalmente também especificar um `GoBackAsync` método, que é usado para programaticamente retornar à página anterior na pilha de navegação. No entanto, esse método está ausente do aplicativo móvel eShopOnContainers porque ele não é necessário.

### <a name="creating-the-navigationservice-instance"></a>Criar a instância de NavigationService

O `NavigationService` de classe que implementa o `INavigationService` interface, é registrado como um singleton com o contêiner de injeção de dependência de Autofac, conforme demonstrado no exemplo de código a seguir:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

O `INavigationService` interface é resolvida no `ViewModelBase` construtor de classe, conforme demonstrado no exemplo de código a seguir:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Isso retorna uma referência para o `NavigationService` objeto que é armazenado no contêiner de injeção de dependência de Autofac, que é criado pelo `InitNavigation` método o `App` classe. Para obter mais informações, consulte [navegar quando o aplicativo é iniciado](#navigating_when_the_app_is_launched).

O `ViewModelBase` classe armazena o `NavigationService` instância em uma `NavigationService` a propriedade do tipo `INavigationService`. Assim, todos os exibir classes de modelo, que derivam de `ViewModelBase` classe, poderá usar o `NavigationService` propriedade para acessar os métodos especificados pelo `INavigationService` interface. Isso evita a sobrecarga de injetar o `NavigationService` objeto do contêiner de injeção de dependência de Autofac em cada classe de exibição de modelo.

### <a name="handling-navigation-requests"></a>Tratamento de solicitações de navegação

Xamarin. Forms fornece o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe que implementa uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas, frente e para trás, conforme desejado. Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Em vez de usar o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe diretamente, a disposição de aplicativo eShopOnContainers o `NavigationPage` classe no `CustomNavigationView` de classe, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

A finalidade dessa disposição é a facilidade de estilo de [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância dentro do arquivo XAML para a classe.

Navegação é executada dentro de classes de modelo de exibição, chamando uma da `NavigateToAsync` métodos, especificando o tipo de modelo de exibição para a página que está sendo navegado para, conforme demonstrado no exemplo de código a seguir:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

O seguinte exemplo de código mostra o `NavigateToAsync` métodos fornecidos pelo `NavigationService` classe:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Cada método permite que qualquer classe de modelo de exibição que deriva de `ViewModelBase` classe para executar a navegação hierárquica invocando o `InternalNavigateToAsync` método. Além disso, o segundo `NavigateToAsync` método permite que os dados de navegação ser especificado como um argumento que é passado para o modelo de exibição que está sendo navegado, onde ela é normalmente usada para executar a inicialização. Para obter mais informações, consulte [passando parâmetros durante a navegação](#passing_parameters_during_navigation).

O `InternalNavigateToAsync` método executa a solicitação de navegação e é mostrado no exemplo de código a seguir:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

O `InternalNavigateToAsync` método executa a navegação para um modelo de exibição ao primeiro chamar o `CreatePage` método. Esse método localiza a exibição que corresponde ao tipo de modelo de exibição especificada e cria e retorna uma instância deste tipo de exibição. Localizar o modo de exibição que corresponde ao tipo de modelo de exibição usa uma abordagem baseado em convenção, que assume que:

-   Modos de exibição estão no mesmo assembly como tipos de modelo de exibição.
-   Modos de exibição um. Namespace de filho de modos de exibição.
-   Exibir modelos estão em um. ViewModels filho namespace.
-   Correspondem nomes de exibição para exibir nomes de modelo, com "Modelo" removido.

Quando uma exibição é instanciada, ela está associada com o modelo de exibição correspondente. Para obter mais informações sobre como isso ocorre, consulte [criar automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Se o modo de exibição que está sendo criado for um `LoginView`, ele é encapsulado dentro de uma nova instância do `CustomNavigationView` classe e atribuído ao [ `Application.Current.MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propriedade. Caso contrário, o `CustomNavigationView` instância é recuperada e fornecida que não seja nula, o [ `PushAsync` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) método é chamado para enviar por push o modo de exibição que está sendo criado para a pilha de navegação. No entanto, se recuperada `CustomNavigationView` instância é `null`, o modo de exibição que está sendo criado é encapsulado dentro de uma nova instância do `CustomNavigationView` classe e atribuído ao `Application.Current.MainPage` propriedade. Esse mecanismo garante que, durante a navegação, páginas são adicionadas corretamente para a pilha de navegação quando ela estiver vazia, e quando ela contém dados.

> [!TIP]
> Considere a possibilidade de cache de páginas. Página cache resulta em consumo de memória por exibições que não são exibidos no momento. No entanto, sem cache de página significa que analisando o XAML e a construção da página e seu modelo de exibição irá ocorrer sempre que uma nova página é navegada, que pode ter um impacto no desempenho de uma página complexas. Para uma página bem projetada que não usa um número excessivo de controles, o desempenho deve ser suficiente. No entanto, a página de cache pode ajudar a se forem encontrados tempos de carregamento de página lento.

Depois que a exibição é criada e navegar para o `InitializeAsync` o método de modelo de exibição associada do modo de exibição é executado. Para obter mais informações, consulte [passando parâmetros durante a navegação](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Navegando ao aplicativo o é iniciado

Quando o aplicativo é iniciado, o `InitNavigation` método o `App` classe é chamada. O exemplo de código a seguir mostra esse método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

O método cria um novo `NavigationService` objeto no contêiner de injeção de dependência Autofac e retorna uma referência a ele, antes de chamar o `InitializeAsync` método.

> [!NOTE]
> Quando o `INavigationService` interface é resolvida com o `ViewModelBase` o contêiner de classe, retorna uma referência para o `NavigationService` objeto que foi criado quando o método InitNavigation é invocado.

O seguinte exemplo de código mostra o `NavigationService` `InitializeAsync` método:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

O `MainView` é direcionada se o aplicativo tiver um token de acesso armazenados em cache, que é usado para autenticação. Caso contrário, o `LoginView` para onde navegar.

Para obter mais informações sobre o contêiner de injeção de dependência de Autofac, consulte [Introdução a injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Passando parâmetros durante a navegação

Uma da `NavigateToAsync` métodos, especificados pelo `INavigationService` interface permite que dados de navegação a ser especificado como um argumento que é passado para o modelo de exibição que está sendo navegado, onde ela é normalmente usada para executar a inicialização.

Por exemplo, o `ProfileViewModel` classe contém um `OrderDetailCommand` que é executado quando o usuário seleciona um pedido no `ProfileView` página. Por sua vez, isso executa o `OrderDetailAsync` método, que é mostrado no exemplo de código a seguir:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Este método invoca a navegação para o `OrderDetailViewModel`, passando um `Order` instância que representa a ordem em que o usuário selecionou no `ProfileView` página. Quando o `NavigationService` classe cria o `OrderDetailView`, o `OrderDetailViewModel` classe é criada e atribuída para o modo de exibição [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Depois de navegar até o `OrderDetailView`, o `InternalNavigateToAsync` método executa o `InitializeAsync` método da exibição associada do modelo de exibição.

O `InitializeAsync` método está definido na `ViewModelBase` classe como um método que pode ser substituído. Esse método Especifica um `object` argumento que representa os dados a serem passados para um modelo de exibição durante uma operação de navegação. Portanto, as classes de modelo de exibição que deseja receber dados de uma operação de navegação fornecem sua própria implementação do `InitializeAsync` método para executar a inicialização exigida. O seguinte exemplo de código mostra o `InitializeAsync` método a partir de `OrderDetailViewModel` classe:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Esse método recupera o `Order` detalhes de instância que foi passada para o modelo de exibição durante a operação de navegação e o utiliza para recuperar a total ordem da `OrderService` instância.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Invocando navegação usando comportamentos

Navegação normalmente é acionada em uma exibição por uma interação do usuário. Por exemplo, o `LoginView` executa a autenticação bem-sucedida de navegação. O exemplo de código a seguir mostra como a navegação é invocada por um comportamento:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

Em tempo de execução, o `EventToCommandBehavior` responderá à interação com o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/). Quando o `WebView` navega para uma página da web, o [ `Navigating` ](https://developer.xamarin.com/api/event/Xamarin.Forms.WebView.Navigating/) evento será disparado, que executará o `NavigateCommand` no `LoginViewModel`. Por padrão, os argumentos de evento para o evento são passados para o comando. Esses dados são convertidos como ele é passado entre origem e destino pelo conversor especificado no `EventArgsConverter` propriedade, que retorna o [ `Url` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebNavigationEventArgs.Url/) do [ `WebNavigatingEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebNavigatingEventArgs/). Portanto, quando o `NavigationCommand` é executado, a Url da página da web é passado como um parâmetro ao nome registrado `Action`.

Por sua vez, o `NavigationCommand` executa o `NavigateAsync` método, que é mostrado no exemplo de código a seguir:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Este método invoca a navegação para o `MainViewModel`, e a barra de navegação, a seguir remove o `LoginView` página de pilha de navegação.

### <a name="confirming-or-cancelling-navigation"></a>Confirmar ou cancelar a navegação

Um aplicativo pode precisar interagir com o usuário durante uma operação de navegação, para que o usuário possa confirmar ou cancelar a navegação. Isso pode ser necessário, por exemplo, quando o usuário tenta navegar antes totalmente ter concluído uma página de entrada de dados. Nessa situação, um aplicativo deve fornecer uma notificação que permite ao usuário navegar para fora da página, ou para cancelar a operação de navegação antes que ele ocorra. Isso pode ser obtido de uma classe de modelo de exibição usando a resposta de uma notificação para controlar se a navegação é invocada.

## <a name="summary"></a>Resumo

Xamarin. Forms inclui suporte para a navegação de página, que geralmente resulta da interação do usuário com a interface do usuário ou do aplicativo em si, como resultado das alterações de estado interno controlada por lógica. No entanto, a navegação pode ser complexa para implementar em aplicativos que usam o padrão MVVM.

Este capítulo apresentado uma `NavigationService` classe, que é usada para executar a navegação de primeiro do modelo de exibição de modelos de exibição. Colocar a lógica de navegação no modo de exibição classes de modelo significa que a lógica pode ser desempenhada por meio de testes automatizados. Além disso, o modelo de exibição, em seguida, pode implementar a lógica para controlar a navegação para assegurar que certas regras de negócio são aplicadas.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
