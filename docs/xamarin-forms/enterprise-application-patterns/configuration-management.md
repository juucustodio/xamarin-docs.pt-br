---
title: Gerenciamento de configurações
description: Este capítulo explica como o aplicativo móvel do eShopOnContainers implementa o gerenciamento de configuração para fornecer configurações do aplicativo e as configurações do usuário.
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6f32d8f328232bdfc644da57bdb3201c60010063
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381889"
---
# <a name="configuration-management"></a>Gerenciamento de configurações

As configurações permitem a separação dos dados que configura o comportamento de um aplicativo do código, permitindo que o comportamento a ser alterado sem recompilar o aplicativo. Há dois tipos de configurações: configurações do aplicativo e as configurações do usuário.

Configurações do aplicativo são dados que um aplicativo cria e gerencia. Ele pode incluir dados como pontos de extremidade de serviço web fixa, as chaves de API e o estado de tempo de execução. Configurações do aplicativo estão vinculadas à existência do aplicativo e só são úteis para o aplicativo.

As configurações do usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não exigem frequente ajuste novamente. Por exemplo, um aplicativo pode permitir que o usuário especifique onde recuperar dados e como exibi-los na tela.

Xamarin. Forms inclui um dicionário persistente que pode ser usado para armazenar dados de configuração. Esse dicionário pode ser acessado usando o [ `Application.Current.Properties` ](xref:Xamarin.Forms.Application.Properties) propriedade e todos os dados que são colocados nela é salva quando o aplicativo entra em um estado de suspensão e será restaurado quando o aplicativo é retomada ou for iniciado novamente. Além disso, o [ `Application` ](xref:Xamarin.Forms.Application) classe também tem uma [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) método que permite que um aplicativo para ter suas configurações salvas quando necessário. Para obter mais informações sobre este dicionário, consulte [dicionário de propriedades](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary).

A desvantagem de armazenamento de dados usando o dicionário persistente do xamarin. Forms é que não é facilmente associado a dados. Portanto, o aplicativo móvel do eShopOnContainers usa a biblioteca Xam.Plugins.Settings, disponível em [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/). Esta biblioteca fornece uma abordagem consistente, fortemente tipada, plataforma cruzada para persistir e recuperando as configurações do aplicativo e o usuário, ao usar o gerenciamento de configurações nativo fornecido por cada plataforma. Além disso, é fácil de usar a vinculação de dados para acessar dados de configurações expostos pela biblioteca.

> [!NOTE]
> Enquanto a biblioteca Xam.Plugin.Settings pode armazenar configurações de aplicativo e o usuário, ele não faz distinção entre os dois.

## <a name="creating-a-settings-class"></a>Criar uma classe de configurações

Ao usar a biblioteca de Xam.Plugins.Settings, uma única classe estática deverá ser criada conterá as configurações de aplicativo e o usuário exigidas pelo aplicativo. O exemplo de código a seguir mostra a classe de configurações no aplicativo móvel do eShopOnContainers:

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

Configurações podem ser lida e gravadas por meio de `ISettings` API, que é fornecido pela biblioteca Xam.Plugins.Settings. Essa biblioteca fornece um singleton que pode ser usado para acessar a API `CrossSettings.Current`, e a classe de configurações do aplicativo deve expor esse singleton por meio de um `ISettings` propriedade.

> [!NOTE]
> Diretivas de uso para os namespaces Plugin.Settings e Plugin.Settings.Abstractions devem ser adicionadas a uma classe que requer acesso aos tipos de biblioteca Xam.Plugins.Settings.

## <a name="adding-a-setting"></a>Adicionando uma configuração

Cada configuração consiste em uma chave, um valor padrão e uma propriedade. O exemplo de código a seguir mostra todos os três itens para uma configuração de usuário que representa a URL base para os serviços online que o aplicativo móvel do eShopOnContainers conecta-se ao:

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

A chave é sempre uma cadeia de caracteres constante que define o nome da chave, com o valor padrão para a configuração, sendo um valor somente leitura estático do tipo necessário. Fornecer um valor padrão garante que um valor válido fica disponível se uma configuração não definida é recuperada.

O `UrlBase` propriedade estática usa dois métodos do `ISettings` API para ler ou gravar o valor da configuração. O `ISettings.GetValueOrDefault` método é usado para recuperar o valor de uma configuração de armazenamento específico da plataforma. Se nenhum valor for definido para a configuração, o valor padrão é recuperado em vez disso. Da mesma forma, o `ISettings.AddOrUpdateValue` método é usado para manter o valor de uma configuração específica da plataforma de armazenamento.

Em vez disso, que definem um valor padrão dentro de `Settings` classe, o `UrlBaseDefault` cadeia de caracteres obtém o valor da `GlobalSetting` classe. O seguinte exemplo de código mostra a `BaseEndpoint` propriedade e `UpdateEndpoint` método nesta classe:

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

Cada vez que o `BaseEndpoint` propriedade for definida, o `UpdateEndpoint` método é chamado. Esse método atualiza uma série de propriedades, que se baseiam os `UrlBase` configuração do usuário que é fornecida pelo `Settings` classe que representam diferentes pontos de extremidade que o aplicativo móvel do eShopOnContainers se conecta ao.

## <a name="data-binding-to-user-settings"></a>Associação de dados para as configurações do usuário

No aplicativo móvel do eShopOnContainers, o `SettingsView` expõe duas configurações de usuário. Essas configurações permitem que a configuração de se o aplicativo deve recuperar dados de microsserviços são implantados como contêineres do Docker, ou se o aplicativo deve recuperar dados de serviços fictícios que não exigem uma conexão de internet. Ao escolher essa opção recuperar dados de microsserviços em contêineres, uma URL de ponto de extremidade de base para os microsserviços deve ser especificada. Figura 7-1 mostra o `SettingsView` quando o usuário tiver escolhido para recuperar dados de microsserviços em contêineres.

![](configuration-management-images/settings-endpoint.png "Configurações de usuário, expostas pelo aplicativo móvel do eShopOnContainers")

**Figura 7-1**: Configurações de usuário, expostas pelo aplicativo móvel do eShopOnContainers

Associação de dados pode ser usada para recuperar e definir as configurações expostas pelo `Settings` classe. Isso é feito pelos controles na associação para exibir as propriedades de modelo que por sua vez, acessar propriedades na exibição de `Settings` classe e acionar uma propriedade alterada notificação se o valor de configurações foi alterado. Para obter informações sobre como o aplicativo móvel do eShopOnContainers constrói o modo de exibição de modelos e as associa a modos de exibição, consulte [criando automaticamente um modelo de exibição com um localizador de modelo de exibição](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

O seguinte exemplo de código mostra a [ `Entry` ](xref:Xamarin.Forms.Entry) controlar do `SettingsView` que permite que o usuário insira uma URL de ponto de extremidade de base para os microsserviços em contêineres:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

Isso [ `Entry` ](xref:Xamarin.Forms.Entry) controle está associado a `Endpoint` propriedade do `SettingsViewModel` de classe, usando uma associação bidirecional. O exemplo de código a seguir mostra a propriedade do ponto de extremidade:

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

Quando o `Endpoint` propriedade é definida a `UpdateEndpoint` método é chamado, desde que o valor fornecido é válido, e a propriedade alterada a notificação é gerada. O seguinte exemplo de código mostra o método `UpdateEndpoint`:

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

Esse método atualiza o `UrlBase` propriedade no `Settings` classe com o valor da URL base do ponto de extremidade inserido pelo usuário, o que faz com que ele persistidos para armazenamento específico da plataforma.

Quando o `SettingsView` é navegada, o `InitializeAsync` método o `SettingsViewModel` classe é executada. O seguinte exemplo de código mostra esse método:

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

Método define a `Endpoint` propriedade para o valor da `UrlBase` propriedade no `Settings` classe. Acessando o `UrlBase` propriedade faz com que a biblioteca Xam.Plugins.Settings recuperar o valor das configurações de armazenamento específico da plataforma. Para obter informações sobre como o `InitializeAsync` método é invocado, consulte [passando parâmetros durante a navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Esse mecanismo garante que sempre que um usuário navega para SettingsView, as configurações do usuário são recuperadas do armazenamento específico da plataforma e apresentadas por meio de associação de dados. Em seguida, se o usuário altera os valores das configurações, a vinculação de dados garante que eles são persistidos imediatamente para armazenamento específico da plataforma.

## <a name="summary"></a>Resumo

As configurações permitem a separação dos dados que configura o comportamento de um aplicativo do código, permitindo que o comportamento a ser alterado sem recompilar o aplicativo. Configurações do aplicativo são dados que um aplicativo cria e gerencia e configurações de usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não exigem o ajuste re frequente.

A biblioteca Xam.Plugins.Settings fornece um consistente, de tipo seguro, abordagem de plataforma cruzada para persistir e recuperar o aplicativo e as configurações de usuário e a associação de dados pode ser usada para acessar as configurações criadas com a biblioteca.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
