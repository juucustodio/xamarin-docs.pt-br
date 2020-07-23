---
title: Gerenciamento de configuração
description: Este capítulo explica como o aplicativo móvel eShopOnContainers implementa o gerenciamento de configuração para fornecer configurações de aplicativo e configurações de usuário.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6def31ebfe2a619f1d5db1e0fba071494afbcd3e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931957"
---
# <a name="configuration-management"></a>Gerenciamento de configuração

As configurações permitem a separação de dados que configuram o comportamento de um aplicativo do código, permitindo que o comportamento seja alterado sem recriar o aplicativo. Há dois tipos de configurações: configurações de aplicativo e configurações de usuário.

As configurações do aplicativo são dados que um aplicativo cria e gerencia. Ele pode incluir dados como pontos de extremidade de serviço Web fixos, chaves de API e estado de tempo de execução. As configurações do aplicativo são ligadas à existência do aplicativo e só são significativas para esse aplicativo.

As configurações de usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não exigem um reajuste frequente. Por exemplo, um aplicativo pode permitir que o usuário especifique para onde recuperar dados e como exibi-los na tela.

Xamarin.Formsinclui um dicionário persistente que pode ser usado para armazenar dados de configurações. Esse dicionário pode ser acessado usando a [`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties) propriedade, e todos os dados colocados nele são salvos quando o aplicativo entra em um estado de suspensão e é restaurado quando o aplicativo é retomado ou iniciado novamente. Além disso, a [`Application`](xref:Xamarin.Forms.Application) classe também tem um [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) método que permite que um aplicativo tenha suas configurações salvas quando necessário. Para obter mais informações sobre esse dicionário, consulte o [dicionário de propriedades](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary).

Uma desvantagem de armazenar dados usando o Xamarin.Forms dicionário persistente é que ele não é vinculado aos dados com facilidade. Portanto, o aplicativo móvel eShopOnContainers usa a biblioteca XAM. plugins. Settings, disponível no [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Essa biblioteca fornece uma abordagem consistente, de tipo seguro e de plataforma cruzada para persistir e recuperar configurações de aplicativo e de usuário, ao usar o gerenciamento de configurações nativas fornecido por cada plataforma. Além disso, é simples usar a vinculação de dados para acessar os dados de configurações expostos pela biblioteca.

> [!NOTE]
> Embora a biblioteca XAM. plugin. Settings possa armazenar as configurações de aplicativo e de usuário, não faz distinção entre as duas.

## <a name="creating-a-settings-class"></a>Criando uma classe de configurações

Ao usar a biblioteca XAM. plugins. Settings, é necessário criar uma única classe estática que conterá as configurações do aplicativo e do usuário exigidas pelo aplicativo. O exemplo de código a seguir mostra a classe Settings no aplicativo móvel eShopOnContainers:

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

As configurações podem ser lidas e gravadas por meio da `ISettings` API, que é fornecida pela biblioteca XAM. plugins. Settings. Essa biblioteca fornece um singleton que pode ser usado para acessar a API, `CrossSettings.Current` e a classe de configurações de um aplicativo deve expor esse singleton por meio de uma `ISettings` propriedade.

> [!NOTE]
> O uso de diretivas para os namespaces plug-in. Settings e plugin. Settings. Abstractions deve ser adicionado a uma classe que requer acesso aos tipos de biblioteca XAM. plugins. Settings.

## <a name="adding-a-setting"></a>Adicionando uma configuração

Cada configuração consiste em uma chave, um valor padrão e uma propriedade. O exemplo de código a seguir mostra todos os três itens para uma configuração de usuário que representa a URL base para o serviços online ao qual o aplicativo móvel eShopOnContainers se conecta:

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

A chave é sempre uma cadeia de caracteres const que define o nome da chave, com o valor padrão para a configuração ser um valor somente leitura estático do tipo necessário. Fornecer um valor padrão garante que um valor válido estará disponível se uma configuração de desdefinição for recuperada.

A `UrlBase` propriedade estática usa dois métodos da `ISettings` API para ler ou gravar o valor da configuração. O `ISettings.GetValueOrDefault` método é usado para recuperar o valor de uma configuração do armazenamento específico da plataforma. Se nenhum valor for definido para a configuração, seu valor padrão será recuperado em vez disso. Da mesma forma, o `ISettings.AddOrUpdateValue` método é usado para manter o valor de uma configuração para o armazenamento específico da plataforma.

Em vez disso, definir um valor padrão dentro da `Settings` classe, a `UrlBaseDefault` cadeia de caracteres obtém seu valor da `GlobalSetting` classe. O exemplo de código a seguir mostra a `BaseEndpoint` propriedade e o `UpdateEndpoint` método nesta classe:

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

Cada vez que a `BaseEndpoint` propriedade é definida, o `UpdateEndpoint` método é chamado. Esse método atualiza uma série de propriedades, todas baseadas na `UrlBase` configuração de usuário fornecida pela `Settings` classe que representam pontos de extremidade diferentes aos quais o aplicativo móvel eShopOnContainers se conecta.

## <a name="data-binding-to-user-settings"></a>Vinculação de dados às configurações do usuário

No aplicativo móvel eShopOnContainers, o `SettingsView` expõe duas configurações de usuário. Essas configurações permitem a configuração de se o aplicativo deve recuperar dados de microserviços que são implantados como contêineres do Docker ou se o aplicativo deve recuperar dados de serviços fictícios que não exigem uma conexão com a Internet. Ao optar por recuperar dados de microservices em contêineres, uma URL de ponto de extremidade base para os microserviços deve ser especificada. A Figura 7-1 mostra `SettingsView` quando o usuário optou por recuperar dados de microservices em contêineres.

![Configurações de usuário expostas pelo aplicativo móvel eShopOnContainers](configuration-management-images/settings-endpoint.png)

**Figura 7-1**: configurações de usuário expostas pelo aplicativo móvel eShopOnContainers

A ligação de dados pode ser usada para recuperar e definir as configurações expostas pela `Settings` classe. Isso é obtido por controles na associação de modo de exibição para exibir propriedades de modelo que, por sua vez, acessam Propriedades na `Settings` classe, e gerando uma notificação de alteração de propriedade se o valor das configurações tiver sido alterado. Para obter informações sobre como o aplicativo móvel eShopOnContainers constrói modelos de exibição e os associa a exibições, consulte [criando automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator).

O exemplo de código a seguir mostra o [`Entry`](xref:Xamarin.Forms.Entry) controle do `SettingsView` que permite ao usuário inserir uma URL de ponto de extremidade base para os microserviços em contêineres:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Esse [`Entry`](xref:Xamarin.Forms.Entry) controle é associado à `Endpoint` propriedade da `SettingsViewModel` classe, usando uma associação bidirecional. O exemplo de código a seguir mostra a Propriedade Endpoint:

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

Quando a `Endpoint` propriedade é definida `UpdateEndpoint` , o método é chamado, desde que o valor fornecido seja válido e a notificação de alteração de propriedade seja gerada. O seguinte exemplo de código mostra o método `UpdateEndpoint`:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Esse método atualiza a `UrlBase` Propriedade na `Settings` classe com o valor de URL do ponto de extremidade base inserido pelo usuário, o que faz com que ele seja persistido no armazenamento específico da plataforma.

Quando o `SettingsView` é navegado para, o `InitializeAsync` método na `SettingsViewModel` classe é executado. O seguinte exemplo de código mostra esse método:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

O método define a `Endpoint` propriedade como o valor da `UrlBase` Propriedade na `Settings` classe. O acesso à `UrlBase` propriedade faz com que a biblioteca XAM. plugins. Settings recupere o valor das configurações do armazenamento específico da plataforma. Para obter informações sobre como o `InitializeAsync` método é invocado, consulte [passando parâmetros durante a navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing-parameters-during-navigation).

Esse mecanismo garante que sempre que um usuário navegar para o SettingsView, as configurações de usuário serão recuperadas do armazenamento específico da plataforma e apresentadas por meio da vinculação de dados. Em seguida, se o usuário alterar os valores das configurações, a vinculação de dados garantirá que eles sejam persistidos imediatamente no armazenamento específico da plataforma.

## <a name="summary"></a>Resumo

As configurações permitem a separação de dados que configuram o comportamento de um aplicativo do código, permitindo que o comportamento seja alterado sem recriar o aplicativo. As configurações do aplicativo são dados que um aplicativo cria e gerencia, e as configurações de usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não exigem um reajuste frequente.

A biblioteca XAM. plugins. Settings fornece uma abordagem consistente, de tipo seguro e de plataforma cruzada para persistir e recuperar as configurações do aplicativo e do usuário, e a vinculação de dados pode ser usada para acessar as configurações criadas com a biblioteca.

## <a name="related-links"></a>Links Relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
