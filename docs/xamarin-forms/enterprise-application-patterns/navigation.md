---
Título: "navegação do aplicativo empresarial" Descrição: "Este capítulo explica como o aplicativo móvel do eShopOnContainers executa o modelo de exibição-primeira navegação de modelos de exibição".
MS. Prod: xamarin MS. AssetID: 4cad57b5-7fe4-4527-a988-d9b60c9620b4 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 08/07/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="enterprise-app-navigation"></a>Navegação de aplicativo empresarial

Xamarin.Formsinclui suporte para a navegação de página, que normalmente resulta da interação do usuário com a interface do usuário ou do próprio aplicativo como resultado de alterações de estado controladas pela lógica interna. No entanto, a navegação pode ser complexa para ser implementada em aplicativos que usam o padrão Model-View-ViewModel (MVVM), pois os seguintes desafios devem ser atendidos:

- Como identificar a exibição a ser navegada, usando uma abordagem que não introduz acoplamento rígido e dependências entre exibições.
- Como coordenar o processo pelo qual a exibição a ser navegada é instanciada e inicializada. Ao usar o MVVM, o modelo de exibição e exibição precisa ser instanciado e associado entre si por meio do contexto de associação da exibição. Quando um aplicativo está usando um contêiner de injeção de dependência, a instanciação de exibições e modelos de exibição pode exigir um mecanismo de construção específico.
- Se deseja executar a navegação pela primeira vez ou exibir a navegação pelo modelo primeiro. Com a navegação da primeira exibição, a página para navegar refere-se ao nome do tipo de exibição. Durante a navegação, a exibição especificada é instanciada, juntamente com seu modelo de exibição correspondente e outros serviços dependentes. Uma abordagem alternativa é usar a navegação de modelo de exibição-primeira, em que a página para navegar refere-se ao nome do tipo de modelo de exibição.
- Como separar com clareza o comportamento de navegação do aplicativo nas exibições e nos modelos de exibição. O padrão MVVM fornece uma separação entre a interface do usuário do aplicativo e sua apresentação e lógica de negócios. No entanto, o comportamento de navegação de um aplicativo muitas vezes incluirá as partes da interface do usuário e das apresentações do aplicativo. O usuário geralmente iniciará a navegação de uma exibição e a exibição será substituída como resultado da navegação. No entanto, a navegação geralmente também precisa ser iniciada ou coordenada no modelo de exibição.
- Como passar parâmetros durante a navegação para fins de inicialização. Por exemplo, se o usuário navegar para uma exibição para atualizar detalhes do pedido, os dados do pedido precisarão ser passados para a exibição para que ele possa exibir os dados corretos.
- Como fazer a navegação coordenada, para garantir que certas regras de negócio sejam obedecedas. Por exemplo, os usuários podem ser solicitados antes de sair de uma exibição para que possam corrigir dados inválidos ou ser solicitados a enviar ou descartar quaisquer alterações de dados feitas na exibição.

Este capítulo aborda esses desafios apresentando uma `NavigationService` classe que é usada para executar o modelo de exibição-primeira navegação de página.

> [!NOTE]
> O `NavigationService` usado pelo aplicativo é projetado apenas para executar navegação hierárquica entre instâncias de Contentpage. O uso do serviço para navegar entre outros tipos de página pode resultar em um comportamento inesperado.

## <a name="navigating-between-pages"></a>Navegando entre páginas

A lógica de navegação pode residir no code-behind de uma exibição ou em um modelo de exibição de associação de dados. Embora colocar a lógica de navegação em uma exibição possa ser a abordagem mais simples, ele não é facilmente testado por meio de testes de unidade. Colocar a lógica de navegação em classes de modelo de exibição significa que a lógica pode ser exercitada por meio de testes de unidade. Além disso, o modelo de exibição pode então implementar a lógica para controlar a navegação a fim de garantir que determinadas regras de negócio sejam impostas. Por exemplo, um aplicativo pode não permitir que o usuário navegue para longe de uma página sem primeiro garantir que os dados inseridos sejam válidos.

`NavigationService`Normalmente, uma classe é invocada de modelos de exibição para promover a capacidade de teste. No entanto, navegar para exibições de modelos de exibição exigiria que os modelos de exibição referenciem exibições e particularmente exibições às quais o modelo de exibição ativo não está associado, o que não é recomendado. Portanto, o `NavigationService` apresentado aqui especifica o tipo de modelo de exibição como o destino para navegar até.

O aplicativo móvel eShopOnContainers usa a `NavigationService` classe para fornecer o modelo de exibição-primeira navegação. Essa classe implementa a `INavigationService` interface, que é mostrada no exemplo de código a seguir:

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

Essa interface especifica que uma classe de implementação deve fornecer os seguintes métodos:

|Método|Finalidade|
|--- |--- |
|`InitializeAsync`|Executa a navegação em uma de duas páginas quando o aplicativo é iniciado.|
|`NavigateToAsync`|Executa a navegação hierárquica em uma página especificada.|
|`NavigateToAsync(parameter)`|Executa a navegação hierárquica em uma página especificada, passando um parâmetro.|
|`RemoveLastFromBackStackAsync`|Remove a página anterior da pilha de navegação.|
|`RemoveBackStackAsync`|Remove todas as páginas anteriores da pilha de navegação.|

Além disso, a `INavigationService` interface especifica que uma classe de implementação deve fornecer uma `PreviousPageViewModel` propriedade. Essa propriedade retorna o tipo de modelo de exibição associado à página anterior na pilha de navegação.

> [!NOTE]
> `INavigationService`Normalmente, uma interface também especifica um `GoBackAsync` método, que é usado para retornar de forma programática para a página anterior na pilha de navegação. No entanto, esse método está faltando no aplicativo móvel eShopOnContainers porque ele não é necessário.

### <a name="creating-the-navigationservice-instance"></a>Criando a instância NavigationService

A `NavigationService` classe, que implementa a `INavigationService` interface, é registrada como um singleton com o contêiner de injeção de dependência Autofac, conforme demonstrado no exemplo de código a seguir:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

A `INavigationService` interface é resolvida no `ViewModelBase` Construtor da classe, conforme demonstrado no exemplo de código a seguir:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Isso retorna uma referência ao `NavigationService` objeto armazenado no contêiner de injeção de dependência Autofac, que é criado pelo `InitNavigation` método na `App` classe. Para obter mais informações, consulte [navegando quando o aplicativo é iniciado](#navigating-when-the-app-is-launched).

A `ViewModelBase` classe armazena a `NavigationService` instância em uma `NavigationService` propriedade, do tipo `INavigationService` . Portanto, todas as classes de modelo de exibição, que derivam da `ViewModelBase` classe, podem usar a `NavigationService` propriedade para acessar os métodos especificados pela `INavigationService` interface. Isso evita a sobrecarga de injetar o `NavigationService` objeto do contêiner de injeção de dependência Autofac em cada classe de modelo de exibição.

### <a name="handling-navigation-requests"></a>Tratamento de solicitações de navegação

Xamarin.Formsfornece a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe, que implementa uma experiência de navegação hierárquica na qual o usuário é capaz de navegar pelas páginas, encaminhamentos e para trás, conforme desejado. Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Em vez de usar a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe diretamente, o aplicativo eShopOnContainers encapsula a `NavigationPage` classe na `CustomNavigationView` classe, conforme mostrado no exemplo de código a seguir:

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

A finalidade desse encapsulamento é a facilidade de estilizar a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância dentro do arquivo XAML para a classe.

A navegação é realizada dentro das classes de modelo de exibição invocando um dos `NavigateToAsync` métodos, especificando o tipo de modelo de exibição para a página que está sendo navegada, conforme demonstrado no exemplo de código a seguir:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

O exemplo de código a seguir mostra os `NavigateToAsync` métodos fornecidos pela `NavigationService` classe:

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

Cada método permite que qualquer classe de modelo de exibição derive da `ViewModelBase` classe para executar a navegação hierárquica invocando o `InternalNavigateToAsync` método. Além disso, o segundo `NavigateToAsync` método permite que os dados de navegação sejam especificados como um argumento passado para o modelo de exibição que está sendo navegado, onde é normalmente usado para executar a inicialização. Para obter mais informações, consulte [passando parâmetros durante a navegação](#passing-parameters-during-navigation).

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

O `InternalNavigateToAsync` método executa a navegação em um modelo de exibição chamando primeiro o `CreatePage` método. Esse método localiza a exibição que corresponde ao tipo de modelo de exibição especificado e cria e retorna uma instância desse tipo de exibição. Localizar a exibição que corresponde ao tipo de modelo de exibição usa uma abordagem baseada em Convenção, o que pressupõe que:

- As exibições estão no mesmo assembly que os tipos de modelo de exibição.
- Os modos de exibição estão em um. Exibe o namespace filho.
- Os modelos de exibição estão em um. Namespace filho de ViewModels.
- Os nomes de exibição correspondem à exibição de nomes de modelo, com o "modelo" removido.

Quando uma exibição é instanciada, ela é associada ao seu modelo de exibição correspondente. Para obter mais informações sobre como isso ocorre, consulte [criando automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

Se o modo de exibição que está sendo criado for um `LoginView` , ele será encapsulado dentro de uma nova instância da `CustomNavigationView` classe e atribuído à [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade. Caso contrário, a `CustomNavigationView` instância será recuperada e desde que não seja nula, o [`PushAsync`](xref:Xamarin.Forms.NavigationPage) método será invocado para enviar por push a exibição que está sendo criada na pilha de navegação. No entanto, se a instância recuperada `CustomNavigationView` for `null` , a exibição que está sendo criada será encapsulada dentro de uma nova instância da `CustomNavigationView` classe e atribuída à `Application.Current.MainPage` propriedade. Esse mecanismo garante que, durante a navegação, as páginas sejam adicionadas corretamente à pilha de navegação quando estiverem vazias e quando contiverem dados.

> [!TIP]
> Considere armazenar páginas em cache. O cache de página resulta no consumo de memória para exibições que não são exibidas no momento. No entanto, sem o cache de página, isso significa que a análise e a construção de XAML da página e seu modelo de exibição ocorrerão sempre que uma nova página for navegada, o que pode afetar o desempenho de uma página complexa. Para uma página bem projetada que não usa um número excessivo de controles, o desempenho deve ser suficiente. No entanto, o cache de página pode ajudar se forem encontrados tempos de carregamento de página lentos.

Depois que a exibição é criada e navegada, o `InitializeAsync` método do modelo de exibição associado do modo de exibição é executado. Para obter mais informações, consulte [passando parâmetros durante a navegação](#passing-parameters-during-navigation).

### <a name="navigating-when-the-app-is-launched"></a>Navegando quando o aplicativo é iniciado

Quando o aplicativo é iniciado, o `InitNavigation` método na `App` classe é invocado. O seguinte exemplo de código mostra esse método:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

O método cria um novo `NavigationService` objeto no contêiner de injeção de dependência Autofac e retorna uma referência a ele, antes de invocar seu `InitializeAsync` método.

> [!NOTE]
> Quando a `INavigationService` interface é resolvida pela `ViewModelBase` classe, o contêiner retorna uma referência ao `NavigationService` objeto que foi criado quando o método InitNavigation é invocado.

O seguinte exemplo de código mostra o método `NavigationService` `InitializeAsync`:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

O `MainView` é navegado para se o aplicativo tiver um token de acesso em cache, que é usado para autenticação. Caso contrário, o `LoginView` será navegado para.

Para obter mais informações sobre o contêiner de injeção de dependência Autofac, consulte [introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection).

### <a name="passing-parameters-during-navigation"></a>Passando parâmetros durante a navegação

Um dos `NavigateToAsync` métodos, especificado pela `INavigationService` interface, permite que os dados de navegação sejam especificados como um argumento que é passado para o modelo de exibição que está sendo navegado, onde é normalmente usado para executar a inicialização.

Por exemplo, a `ProfileViewModel` classe contém um `OrderDetailCommand` que é executado quando o usuário seleciona um pedido na `ProfileView` página. Por sua vez, isso executa o `OrderDetailAsync` método, que é mostrado no exemplo de código a seguir:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Esse método invoca a navegação para o `OrderDetailViewModel` , passando uma `Order` instância que representa a ordem que o usuário selecionou na `ProfileView` página. Quando a `NavigationService` classe cria o `OrderDetailView` , a `OrderDetailViewModel` classe é instanciada e atribuída à exibição [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Depois de navegar até o `OrderDetailView` , o `InternalNavigateToAsync` método executa o `InitializeAsync` método do modelo de exibição associado do modo de exibição.

O `InitializeAsync` método é definido na `ViewModelBase` classe como um método que pode ser substituído. Esse método especifica um `object` argumento que representa os dados a serem passados para um modelo de exibição durante uma operação de navegação. Portanto, as classes de modelo de exibição que desejam receber dados de uma operação de navegação fornecem sua própria implementação do `InitializeAsync` método para executar a inicialização necessária. O exemplo de código a seguir mostra o `InitializeAsync` método da `OrderDetailViewModel` classe:

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

Esse método recupera a `Order` instância que foi passada para o modelo de exibição durante a operação de navegação e a usa para recuperar os detalhes completos do pedido da `OrderService` instância.

### <a name="invoking-navigation-using-behaviors"></a>Invocando navegação usando comportamentos

A navegação é geralmente disparada de uma exibição por uma interação do usuário. Por exemplo, o `LoginView` executa a navegação após a autenticação bem-sucedida. O exemplo de código a seguir mostra como a navegação é invocada por um comportamento:

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

Em tempo de execução, o `EventToCommandBehavior` responderá à interação com o [`WebView`](xref:Xamarin.Forms.WebView) . Quando o `WebView` navega para uma página da Web, o [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) evento será acionado, o que executará o `NavigateCommand` no `LoginViewModel` . Por padrão, os argumentos de evento para o evento são passados para o comando. Esses dados são convertidos à medida que são passados entre a origem e o destino pelo conversor especificado na `EventArgsConverter` propriedade, que retorna o [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) do [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) . Portanto, quando o `NavigationCommand` é executado, a URL da página da Web é passada como um parâmetro para o registrado `Action` .

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

Esse método invoca a navegação para o `MainViewModel` e a navegação a seguir remove a `LoginView` página da pilha de navegação.

### <a name="confirming-or-cancelling-navigation"></a>Confirmando ou cancelando a navegação

Um aplicativo pode precisar interagir com o usuário durante uma operação de navegação, para que o usuário possa confirmar ou cancelar a navegação. Isso pode ser necessário, por exemplo, quando o usuário tenta navegar antes de concluir completamente uma página de entrada de dados. Nessa situação, um aplicativo deve fornecer uma notificação que permita que o usuário navegue para fora da página ou cancele a operação de navegação antes que ela ocorra. Isso pode ser obtido em uma classe de modelo de exibição usando a resposta de uma notificação para controlar se a navegação é invocada ou não.

## <a name="summary"></a>Resumo

Xamarin.Formsinclui suporte para a navegação de página, que normalmente resulta da interação do usuário com a interface do usuário ou do próprio aplicativo, como resultado de alterações de estado controladas pela lógica interna. No entanto, a navegação pode ser complexa para ser implementada em aplicativos que usam o padrão MVVM.

Este capítulo apresentou uma `NavigationService` classe, que é usada para executar o modelo de exibição-primeira navegação de modelos de exibição. Colocar a lógica de navegação em classes de modelo de exibição significa que a lógica pode ser exercitada por meio de testes automatizados. Além disso, o modelo de exibição pode então implementar a lógica para controlar a navegação a fim de garantir que determinadas regras de negócio sejam impostas.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
