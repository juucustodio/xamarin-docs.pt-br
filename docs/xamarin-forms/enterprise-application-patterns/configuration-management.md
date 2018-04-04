---
title: Gerenciamento de configurações
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9318f86077c9cdb98e073e4816dae4fdabbfe568
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="configuration-management"></a>Gerenciamento de configurações

As configurações permitem a separação de dados que define o comportamento de um aplicativo de código, permitindo que o comportamento a ser alterado sem recriar o aplicativo. Há dois tipos de configurações: configurações do aplicativo e as configurações do usuário.

Configurações do aplicativo são dados que um aplicativo cria e gerencia. Ele pode incluir dados como pontos de extremidade de serviço web fixa, chaves de API e do estado de tempo de execução. Configurações do aplicativo estão vinculadas a existência do aplicativo e só fazem sentidas para esse aplicativo.

Configurações de usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não requerem frequente ajuste novamente. Por exemplo, um aplicativo pode permitir que o usuário especifique onde recuperar dados e como exibi-las na tela.

Xamarin. Forms inclui um dicionário persistente que pode ser usado para armazenar dados de configuração. Esse dicionário pode ser acessado usando o [ `Application.Current.Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/) propriedade e quaisquer dados que são colocados em que ele é salvo quando o aplicativo entra em um estado de suspensão e é restaurado quando o aplicativo continua ou é iniciado novamente. Além disso, o [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) classe também tem um [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) método que permite que um aplicativo para que suas configurações salvas quando necessário. Para obter mais informações sobre este dicionário, consulte [dicionário de propriedades](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

Armazenando dados com o dicionário persistente xamarin. Forms uma desvantagem é que não é facilmente os dados associados a. Portanto, o aplicativo móvel eShopOnContainers usa a biblioteca de Xam.Plugins.Settings, disponível em [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Essa biblioteca fornece uma abordagem consistente, fortemente tipado, plataforma cruzada para persistir e recuperar as configurações de aplicativo e usuário, ao usar o gerenciamento de configurações nativo fornecido por cada plataforma. Além disso, é fácil usar associação de dados para acessar os dados de configurações expostos pela biblioteca.

> [!NOTE]
> Enquanto a biblioteca Xam.Plugin.Settings pode armazenar configurações de aplicativo e usuário, ele não faz distinção entre os dois.

## <a name="creating-a-settings-class"></a>Criando uma classe de configurações

Quando usar a biblioteca de Xam.Plugins.Settings, uma única classe estática deve ser criada conterá as configurações de aplicativo e usuário exigidas pelo aplicativo. O exemplo de código a seguir mostra a classe de configurações no aplicativo móvel do eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

Configurações podem ser lidos e gravadas por meio de `ISettings` API, que é fornecida pela biblioteca Xam.Plugins.Settings. Essa biblioteca fornece um singleton que pode ser usado para acessar a API `CrossSettings.Current`, e a classe de configurações do aplicativo deve expor esse singleton por meio de um `ISettings` propriedade.

> [!NOTE]
> Usando diretivas para os namespaces Plugin.Settings e Plugin.Settings.Abstractions deve ser adicionado a uma classe que requer acesso para os tipos de biblioteca Xam.Plugins.Settings.

## <a name="adding-a-setting"></a>Adicionar uma configuração

Cada configuração consiste em uma propriedade de uma chave e um valor padrão. O exemplo de código a seguir mostra três itens para uma configuração de usuário que representa a URL base para os serviços online que o aplicativo móvel eShopOnContainers conecta-se a:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

A chave é sempre uma cadeia de caracteres constante que define o nome da chave, com o valor padrão para a configuração que está sendo um valor somente leitura estático do tipo necessário. Fornecer um valor padrão garante que um valor válido estará disponível se uma configuração unset é recuperada.

O `UrlBase` propriedade estática usa dois métodos do `ISettings` API para ler ou gravar o valor da configuração. O `ISettings.GetValueOrDefault` método é usado para recuperar o valor de uma configuração de armazenamento específica de plataforma. Se nenhum valor for definido para a configuração, o valor padrão é recuperado em vez disso. Da mesma forma, o `ISettings.AddOrUpdateValue` método é usado para manter o valor de uma configuração de armazenamento específica de plataforma.

Em vez disso, que definem um valor padrão dentro de `Settings` classe, o `UrlBaseDefault` cadeia obtém o valor da `GlobalSetting` classe. O seguinte exemplo de código mostra o `BaseEndpoint` propriedade e `UpdateEndpoint` método nesta classe:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Cada vez que o `BaseEndpoint` propriedade for definida, o `UpdateEndpoint` método é chamado. Este método atualizará uma série de propriedades, que se baseiam o `UrlBase` configuração de usuário que é fornecida pelo `Settings` classe que representam diferentes pontos de extremidade que o aplicativo móvel eShopOnContainers se conecta ao.

## <a name="data-binding-to-user-settings"></a>Associação de dados para as configurações do usuário

No aplicativo móvel do eShopOnContainers, o `SettingsView` expõe duas configurações do usuário. Estas configurações permitem que a configuração de se o aplicativo deve recuperar dados de microservices que são implantadas como contêineres do Docker, ou se o aplicativo deve recuperar dados dos serviços de simulação que não exigem uma conexão de internet. Ao escolher essa opção recuperar dados de microservices em contêineres, uma URL de ponto de extremidade de base para o microservices deve ser especificada. Figura 7-1 mostra a `SettingsView` quando o usuário escolheu recuperar dados de microservices em contêineres.

![](configuration-management-images/settings-endpoint.png "Configurações de usuário expostas pelo aplicativo móvel eShopOnContainers")

**Figura 7-1**: expostas pelo aplicativo móvel eShopOnContainers as configurações do usuário

Associação de dados pode ser usada para recuperar e definir configurações expostas pelo `Settings` classe. Isso é obtido com a associação do modo de exibição para exibir as propriedades de modelo que por sua vez acessar propriedades em controles de `Settings` classe e gerando uma propriedade alterada notificação se o valor de configurações foi alterado. Para obter informações sobre como o aplicativo móvel eShopOnContainers constrói o modo de exibição de modelos e as associa a modos de exibição, consulte [criar automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

O seguinte exemplo de código mostra o [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar do `SettingsView` que permite que o usuário insira uma URL de ponto de extremidade de base para o microservices em contêineres:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Isso [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle associa ao `Endpoint` propriedade o `SettingsViewModel` de classe, usando uma associação bidirecional. O exemplo de código a seguir mostra a propriedade de ponto de extremidade:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Quando o `Endpoint` está definida a `UpdateEndpoint` método é chamado, desde que o valor fornecido é válido, e a propriedade alterada notificação será gerada. O seguinte exemplo de código mostra o `UpdateEndpoint` método:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Este método atualizará o `UrlBase` propriedade o `Settings` classe com o valor da URL de ponto de extremidade de base inserido pelo usuário, que faz com que ele persistidos para armazenamento específico de plataforma.

Quando o `SettingsView` navegada, o `InitializeAsync` método o `SettingsViewModel` classe é executada. O exemplo de código a seguir mostra esse método:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Define o método de `Endpoint` propriedade para o valor da `UrlBase` propriedade no `Settings` classe. Acessando o `UrlBase` propriedade faz com que a biblioteca de Xam.Plugins.Settings recuperar o valor de configurações de armazenamento específica de plataforma. Para obter informações sobre como o `InitializeAsync` método é invocado, consulte [passando parâmetros durante a navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Esse mecanismo garante que sempre que um usuário navega para o SettingsView, as configurações do usuário são recuperadas do armazenamento específica de plataforma e apresentadas por meio de associação de dados. Em seguida, se o usuário altera os valores de configurações, a associação de dados garante que elas persistem imediatamente para armazenamento específico de plataforma.

## <a name="summary"></a>Resumo

As configurações permitem a separação de dados que define o comportamento de um aplicativo de código, permitindo que o comportamento a ser alterado sem recriar o aplicativo. Configurações do aplicativo são dados que um aplicativo cria e gerencia e as configurações do usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não requerem frequente ajuste novamente.

A biblioteca de Xam.Plugins.Settings fornece um consistente, fortemente tipado, abordagem de plataforma cruzada para persistir e recuperar o aplicativo e as configurações de usuário e associação de dados pode ser usada para acessar as configurações criadas com a biblioteca.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
