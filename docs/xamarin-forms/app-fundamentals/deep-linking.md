---
title: Indexação de aplicativo e vinculação profunda
description: Este artigo explica como usar a indexação de aplicativo e a vinculação profunda para fazer com que o Xamarin.Forms tenha conteúdo pesquisável em dispositivos iOS e Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: fcd8333a0623058fceb486183ddb995e85eaf18a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76940330"
---
# <a name="application-indexing-and-deep-linking"></a>Indexação de aplicativo e vinculação profunda

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)

_A indexação de aplicativos permite que aplicativos que de outra forma seriam esquecidos após alguns usos para se manterem relevantes, aparecendo nos resultados da pesquisa. O deep linking permite que os aplicativos respondam a um resultado de pesquisa que contenha dados do aplicativo, normalmente navegando para uma página referenciada a partir de um link profundo. Este artigo explica como usar indexação de aplicativos e vinculação profunda para tornar o conteúdo do aplicativo Xamarin.Forms pesquisável em dispositivos iOS e Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Vídeo sobre a vinculação profunda com o Xamarin.Forms e o Azure**

A indexação de aplicativo e a vinculação profunda do Xamarin.Forms fornecem uma API para publicar metadados para indexação de aplicativo à medida que os usuários navegam pelos aplicativos. O conteúdo indexado pode ser pesquisado no Spotlight Search, na Pesquisa Google ou em uma pesquisa na Web. Tocar em um resultado de pesquisa que contém um link profundo acionará um evento que poderá ser tratado por um aplicativo. Isso normalmente é usado para navegar até a página referenciada do link profundo.

Este aplicativo de exemplo demonstra um aplicativo de Lista de tarefas em que os dados são armazenados em um banco de dados local do SQLite, como mostrado nas seguintes capturas de tela:

![](deep-linking-images/screenshots.png "TodoList Application")

Cada instância `TodoItem` criada pelo usuário é indexada. Então, a pesquisa específica da plataforma pode ser usada para localizar dados indexados do aplicativo. Quando o usuário toca em um item de resultado de pesquisa para o aplicativo, o aplicativo é iniciado, o `TodoItemPage` é navegada e o `TodoItem` referenciado do link profundo é exibido.

Para obter mais informações sobre como usar um banco de dados do SQLite, consulte [Bancos de Dados Locais do Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md).

> [!NOTE]
> A funcionalidade de indexação de aplicativo e vinculação profunda do Xamarin.Forms somente está disponível nas plataformas Android e iOS e requer um mínimo de iOS 9 e API 23, respectivamente.

## <a name="setup"></a>Instalação

As seções a seguir fornecem instruções de configuração adicionais para usar esse recurso nas plataformas Android e iOS.

### <a name="ios"></a>iOS

Na plataforma iOS, certifique-se de que seu projeto da plataforma iOS defina o arquivo **Entitlements.plist** como o arquivo de direitos personalizados para assinar o pacote.

Para usar Links Universais do iOS:

1. Adicione um direito de Domínios Associados ao seu aplicativo, com a chave `applinks`, incluindo todos os domínios que seu aplicativo dará suporte.
1. Adicione um arquivo de Associação de Site do Aplicativo da Apple ao seu site.
1. Adicione a chave `applinks` ao arquivo de Associação de Site do Aplicativo da Apple.

Para obter mais informações, consulte [Permitir que aplicativos e sites se vinculem ao seu conteúdo](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) em developer.apple.com.

### <a name="android"></a>Android

Na plataforma Android, há uma série de pré-requisitos que devem ser atendidos para usar a funcionalidade de indexação de aplicativo e vinculação profunda:

1. Uma versão do seu aplicativo deve estar em tempo real no Google Play.
1. Um site complementar deve ser registrado com o aplicativo no Console do Desenvolvedor do Google. Depois que o aplicativo é associado a um site, as URLs podem ser indexadas e funcionam para o site e o aplicativo, que pode ser atendido nos resultados da pesquisa. Para obter mais informações, consulte [Indexação de apps na Pesquisa Google](https://support.google.com/googleplay/android-developer/answer/6041489) no site do Google.
1. Seu aplicativo deve dar suporte a intenções de URL HTTP na classe `MainActivity`, que informa à indexação de aplicativo quais tipos de esquemas de dados de URL o aplicativo pode responder. Para obter mais informações, consulte [Configurar o Filtro de Intenção](~/android/platform/app-linking.md#configure-intent-filter).

Depois que esses pré-requisitos forem atendidos, a seguinte configuração adicional será necessária para usar a indexação de aplicativo e a vinculação profunda do Xamarin.Forms na plataforma Android:

1. Instale o pacote do NuGet do [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) no projeto de aplicativo do Android.
1. No arquivo **MainActivity.cs**, adicione uma declaração para usar o namespace `Xamarin.Forms.Platform.Android.AppLinks`.
1. No arquivo **MainActivity.cs**, adicione uma declaração para usar o namespace `Firebase`.
1. Em um navegador da Web, crie um projeto por meio do [Console do Firebase](https://console.firebase.google.com/).
1. No Console do Firebase, adicione o Firebase ao seu aplicativo Android e insira os dados necessários.
1. Baixe o arquivo **google-services.json** resultante.
1. Adicione o arquivo **google-services.json** ao diretório raiz do projeto do Android e defina sua **Ação de Build** como **GoogleServicesJson**.
1. Na substituição de `MainActivity.OnCreate`, adicione a seguinte linha de código abaixo de `Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

Quando **google-services.json** é adicionado ao projeto (e a ação de build *GoogleServicesJson** é definida), o processo de build extrai a chave de API e a ID do cliente e, em seguida, adiciona essas credenciais ao arquivo de manifesto gerado.

> [!NOTE]
> Neste artigo, os termos links de aplicativo e links profundos são frequentemente usados ​​de forma intercambiável. No entanto, no Android, estes termos têm significados separados. No Android, um link profundo é um filtro de intenção que permite aos usuários inserir diretamente uma atividade específica no aplicativo. Clicar em um link profundo pode abrir uma caixa de diálogo de desambiguidade, que permite ao usuário selecionar um dos vários aplicativos que podem manipular a URL. Um link de aplicativo do Android é um link profundo com base na URL do site, que foi verificada para pertencer ao seu site. Clicar em um link de aplicativo abre seu aplicativo se ele estiver instalado, sem abrir uma caixa de diálogo de desambiguidade.

Para obter mais informações, consulte [Conteúdo de link profundo com navegação de URL do Xamarin.Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) no blog do Xamarin.

## <a name="indexing-a-page"></a>Indexação de uma página

O processo de indexação e exposição de uma página na pesquisa do Google e do Spotlight é da seguinte maneira:

1. Crie [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) um que contenha os metadados necessários para indexar a página, juntamente com um link profundo para retornar à página quando o usuário selecionar o conteúdo indexado nos resultados da pesquisa.
1. Registre [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) a instância para indexá-lo para pesquisa.

O exemplo de código a [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) seguir demonstra como criar uma instância:

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

A [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) instância contém uma série de propriedades cujos valores são necessários para indexar a página e criar um link profundo. As [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title) [`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description)propriedades [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) e propriedades são usadas para identificar o conteúdo indexado quando ele aparece nos resultados da pesquisa. A [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriedade está `true` definida para indicar que o conteúdo indexado está sendo visualizado no momento. A [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) propriedade `Uri` é uma que contém as informações necessárias `TodoItem`para retornar à página atual e exibir a corrente . O exemplo a seguir mostra um `Uri` de exemplo para o aplicativo de exemplo:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Esse `Uri` contém todas as informações necessárias para iniciar o aplicativo `deeplinking`. Navegue até `DeepLinking.TodoItemPage`e exiba o `TodoItem` que tem um `ID` de 2.

## <a name="registering-content-for-indexing"></a>Registro do conteúdo para indexação

Uma [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) vez criada uma instância, ela deve ser registrada para indexação para aparecer nos resultados da pesquisa. Isso é realizado [`RegisterLink`](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) com o método, como demonstrado no seguinte exemplo de código:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Isso adiciona [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) a instância à [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) coleção do aplicativo.

> [!NOTE]
> O método `RegisterLink` também pode ser usado para atualizar o conteúdo que foi indexado para uma página.

Uma [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) vez que uma instância tenha sido registrada para indexação, ela pode aparecer nos resultados da pesquisa. A captura de tela a seguir mostra o conteúdo indexado que aparece nos resultados da pesquisa na plataforma iOS:

![](deep-linking-images/ios-search.png "Indexed Content in Search Results on iOS")

## <a name="de-registering-indexed-content"></a>Cancelar o registro do conteúdo indexado

O [`DeregisterLink`](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) método é usado para remover conteúdo indexado dos resultados da pesquisa, como demonstrado no exemplo de código a seguir:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Isso remove [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) a instância da [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) coleção do aplicativo.

> [!NOTE]
> No Android, não é possível remover o conteúdo indexado dos resultados da pesquisa.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Responder a um Link Profundo

Quando o conteúdo indexado for exibido nos resultados da pesquisa e for selecionado por um usuário, a classe `App` para o aplicativo receberá uma solicitação para lidar com o `Uri` contido no conteúdo indexado. Esta solicitação pode ser [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) processada na substituição, como demonstrado no exemplo de código a seguir:

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

O [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) método verifica `Uri` se o recebido é destinado à `Uri` aplicação, antes de analisar a página a ser navegada e o parâmetro a ser passado para a página. Uma instância da página que será navegada é criada e o `TodoItem` representado pelo parâmetro da página é recuperado. A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) página a ser navegada é `TodoItem`então definida como . Isso garante que, `TodoItemPage` quando o [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) método for exibido, `TodoItem` ele `ID` estará mostrando o que está contido no link profundo.

## <a name="making-content-available-for-search-indexing"></a>Disponibilizar o conteúdo disponível para indexação de pesquisa

Cada vez que a página representada por [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) um link profundo `true`é exibida, a propriedade pode ser definida como . No iOS e Android [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) isso torna a instância disponível para indexação `AppLinkEntry` de pesquisa, e apenas no iOS, ele também disponibiliza a instância para Handoff. Para obter mais informações sobre a entrega, consulte [Introdução à entrega](~/ios/platform/handoff.md).

O exemplo de código [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) a `true` seguir [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) demonstra a configuração da propriedade na substituição:

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

Da mesma forma, quando a página representada por um [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) link profundo `false`é navegada para longe, a propriedade pode ser definida como . No iOS e Android, [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) isso impede que a instância seja anunciada para indexação de `AppLinkEntry` pesquisa, e apenas no iOS, ele também pára de anunciar a instância de Handoff. Isso pode ser [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) feito na substituição, como demonstrado no exemplo de código a seguir:

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>Fornecer dados para entrega

No iOS, os dados específicos do aplicativo podem ser armazenados durante a indexação de página. Isso é conseguido adicionando [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) dados à `Dictionary<string, string>` coleção, que é um para armazenar pares de valor-chave que são usados no Handoff. A entrega é uma maneira para o usuário iniciar uma atividade em um dos seus dispositivos e continuar essa atividade em outro de seus dispositivos (conforme identificado pela conta do iCloud do usuário). O código a seguir mostra um exemplo de armazenamento de pares chave-valor específicos do aplicativo:

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Os valores [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) armazenados na coleção serão armazenados nos metadados da página indexada e serão restaurados quando o usuário tocar em um resultado de pesquisa que contenha um link profundo (ou quando o Handoff é usado para exibir o conteúdo em outro dispositivo de entrada).

Além disso, os valores para as seguintes chaves podem ser especificados:

- `contentType` – um `string` que especifica o identificador de tipo uniforme do conteúdo indexado. A convenção recomendada para uso para esse valor é o nome do tipo da página com o conteúdo indexado.
- `associatedWebPage` – um `string` que representa a página da Web a ser visitada se o conteúdo indexado também puder ser exibido na Web ou se o aplicativo der suporte a links profundos do Safari.
- `shouldAddToPublicIndex` – um `string` de `true` ou `false` que controla a necessidade de adição de conteúdo indexado ao índice de nuvem pública da Apple, que, em seguida, pode ser apresentado aos usuários que ainda não instalaram o aplicativo em seu dispositivo iOS. No entanto, o conteúdo ter sido definido para indexação pública não significa que ele será automaticamente adicionado ao índice de nuvem pública da Apple. Para obter mais informações, consulte [Indexação de pesquisa pública](~/ios/platform/search/nsuseractivity.md). Observe que esta chave `false` deve ser definida [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) ao adicionar dados pessoais à coleção.

> [!NOTE]
> A coleção `KeyValues` não é usada na plataforma Android.

Para obter mais informações sobre a entrega, consulte [Introdução à entrega](~/ios/platform/handoff.md).

## <a name="summary"></a>Resumo

Este artigo explicou como usar a indexação de aplicativo e a vinculação profunda para tornar o conteúdo do aplicativo Xamarin.Forms pesquisável em dispositivos iOS e Android. A indexação de aplicativo permite que os aplicativos que seriam esquecidos após alguns usos permaneçam relevantes fazendo com que eles apareçam nos resultados da pesquisa.

## <a name="related-links"></a>Links relacionados

- [Vinculação profunda (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)
- [APIs de pesquisa do iOS](~/ios/platform/search/index.md)
- [Vinculação de aplicativo no Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
