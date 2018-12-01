---
title: Indexação de aplicativo e vinculação profunda
description: Este artigo explica como usar a indexação de aplicativo e vinculação profunda para tornar o conteúdo do aplicativo xamarin. Forms pesquisáveis em dispositivos iOS e Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: f73760e2dc2310a9c1cd7a63a03ead37283a415f
ms.sourcegitcommit: 215cad17324ba3fbc23487ce66cd4e1cc74eb879
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710004"
---
# <a name="application-indexing-and-deep-linking"></a>Indexação de aplicativo e vinculação profunda

_Indexação de aplicativo permite que os aplicativos que seriam esquecidos caso contrário, depois de alguns usa para permanecer relevante por apareçam nos resultados da pesquisa. Vinculação profunda permite que os aplicativos responder a um resultado de pesquisa que contém dados de aplicativo, normalmente, navegando até uma página referenciada de um link profundo. Este artigo explica como usar a indexação de aplicativo e vinculação profunda para tornar o conteúdo do aplicativo xamarin. Forms pesquisáveis em dispositivos iOS e Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Vinculação profunda com o xamarin. Forms e o Azure, por [Xamarin University](https://university.xamarin.com/)**


Indexação de aplicativo do xamarin. Forms e vinculação profunda fornecem uma API para publicar metadados para o aplicativo indexação à medida que os usuários navegam por meio de aplicativos. Conteúdo indexado pode ser pesquisado na pesquisa de destaque, na pesquisa do Google ou em uma pesquisa na web. Tocar em um resultado de pesquisa que contém um link profundo acionarão um evento que pode ser tratado por um aplicativo e normalmente é usado para navegar até a página referenciada do link profundo.

O aplicativo de exemplo demonstra um aplicativo de lista de tarefas em que os dados são armazenados em um banco de dados SQLite local, conforme mostrado nas capturas de tela seguir:

![](deep-linking-images/screenshots.png "Aplicativo de lista de tarefas pendentes")

Cada `TodoItem` instância criada pelo usuário é indexada. Pesquisa de específico da plataforma, em seguida, pode ser usada para localizar dados indexados do aplicativo. Quando o usuário toca em um item de resultado de pesquisa para o aplicativo, o aplicativo é iniciado, o `TodoItemPage` é navegada e o `TodoItem` referenciado de profundo link será exibido.

Para obter mais informações sobre como usar um banco de dados SQLite, consulte [bancos de dados locais do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> Indexação de aplicativo do xamarin. Forms e a funcionalidade de vinculação profunda só está disponíveis nas plataformas Android e iOS e requer um mínimo de iOS 9 e API 23, respectivamente.

## <a name="setup"></a>Configuração

As seções a seguir fornecem as instruções de configuração adicional para usar esse recurso nas plataformas Android e iOS.

### <a name="ios"></a>iOS

Na plataforma iOS, certifique-se de que seu projeto da plataforma iOS define o **Entitlements. plist** como o arquivo de direitos personalizados para assinar o pacote.

Para usar Links universais do iOS:

1. Adicionar um direito de domínios associados ao seu aplicativo, com o `applinks` principais, incluindo todos os domínios de seu aplicativo dará suporte.
1. Adicione um arquivo de associação de Site do aplicativo Apple ao seu site.
1. Adicionar o `applinks` chave para o arquivo de associação de Site do aplicativo da Apple.

Para obter mais informações, consulte [permitindo que aplicativos e sites para o Link para seu conteúdo](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) no developer.apple.com.

### <a name="android"></a>Android

Na plataforma Android, há uma série de pré-requisitos que devem ser atendidos para usar a indexação de aplicativo e a funcionalidade de vinculação profunda:

1. Uma versão do seu aplicativo deve ser em tempo real no Google Play.
1. Um site complementar deve ser registrado com o aplicativo no Console do desenvolvedor do Google. Depois que o aplicativo está associado um site, URLs podem ser indexados que funcionam para o site e o aplicativo, que pode ser atendido, em seguida, nos resultados da pesquisa. Para obter mais informações, consulte [indexação de aplicativo na pesquisa Google](https://support.google.com/googleplay/android-developer/answer/6041489) no site do Google.
1. Seu aplicativo deve oferecer suporte a tentativas de URL HTTP no `MainActivity` classe, que informam ao aplicativo de indexação que tipos de esquemas de dados de URL do aplicativo pode responder. Para obter mais informações, consulte [Configurando o filtro de intenção](~/android/platform/app-linking.md#configure-intent-filter).

Depois que esses pré-requisitos forem atendidos, a seguinte configuração adicional é necessária para usar a indexação de aplicativo do xamarin. Forms e vinculação profunda na plataforma Android:

1. Instalar o [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) pacote do NuGet no projeto de aplicativo do Android.
1. No **MainActivity.cs** do arquivo, adicione uma declaração para usar o `Xamarin.Forms.Platform.Android.AppLinks` namespace.
1. No **MainActivity.cs** do arquivo, adicione uma declaração para usar o `Firebase` namespace.
1. Em um navegador da web, crie um novo projeto por meio de [Console Firebase](https://console.firebase.google.com/).
1. No Console do Firebase, adicionar o Firebase ao seu aplicativo Android e insira os dados necessários.
1. Baixar resultante **google-Services. JSON** arquivo.
1. Adicione a **google-Services. JSON** arquivo no diretório raiz do projeto do Android e defina seu **Build Action** para **GoogleServicesJson**.
1. No `MainActivity.OnCreate` substituir, adicione a seguinte linha de código abaixo `Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

Quando **google-Services. JSON** é adicionado ao projeto (e o *GoogleServicesJson** ação de compilação é definida), o processo de build extrai a chave de API e a ID do cliente e, em seguida, adiciona essas credenciais para o arquivo de manifesto gerado.

Para obter mais informações, consulte [profunda o Link de conteúdo com navegação de URL com xamarin. Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) no blog do Xamarin.

## <a name="indexing-a-page"></a>Indexação de uma página

O processo de indexação de uma página e expô-lo para pesquisa Google e destaque é da seguinte maneira:

1. Criar uma [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) que contém os metadados necessários para a página, juntamente com um link profundo para retornar à página quando o usuário seleciona o conteúdo indexado nos resultados da pesquisa de índice.
1. Registre-se a [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância para indexá-los para a pesquisa.

O exemplo de código a seguir demonstra como criar uma [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância:

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

O [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância contém uma série de propriedades cujos valores são necessários para a página de índice e criar um link profundo. O [ `Title` ](xref:Xamarin.Forms.IAppLinkEntry.Title), [ `Description` ](xref:Xamarin.Forms.IAppLinkEntry.Description), e [ `Thumbnail` ](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) propriedades são usadas para identificar o conteúdo indexado quando ele for exibido nos resultados da pesquisa. O [ `IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) estiver definida como `true` para indicar que o conteúdo indexado atualmente está sendo exibido. O [ `AppLinkUri` ](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) propriedade é uma `Uri` que contém as informações necessárias para retornar à página atual e exibir atual `TodoItem`. O exemplo a seguir mostra um exemplo `Uri` para o aplicativo de exemplo:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Isso `Uri` contém todas as informações necessárias para iniciar o `deeplinking` aplicativo, navegue até a `DeepLinking.TodoItemPage`e exibir os `TodoItem` que tem um `ID` de 2.

## <a name="registering-content-for-indexing"></a>Registrando o conteúdo para a indexação

Uma vez um [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância tiver sido criada, ele deve ser registrado para a indexação aparecem nos resultados da pesquisa. Isso é feito com o [ `RegisterLink` ](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) método, conforme demonstrado no exemplo de código a seguir:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Isso adiciona o [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância para o aplicativo [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) coleção.

> [!NOTE]
> O `RegisterLink` método também pode ser usado para atualizar o conteúdo foi indexado para uma página.

Uma vez um [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância foi registrada para indexação, ela pode aparecer nos resultados da pesquisa. Captura de tela a seguir mostra o conteúdo indexado apareçam nos resultados da pesquisa na plataforma iOS:

![](deep-linking-images/ios-search.png "Conteúdo indexado nos resultados da pesquisa no iOS")

## <a name="de-registering-indexed-content"></a>Cancelar o registro de indexação de conteúdo

O [ `DeregisterLink` ](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) método é usado para remover o conteúdo indexado de resultados da pesquisa, conforme demonstrado no exemplo de código a seguir:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Isso remove os [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância a partir do aplicativo [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) coleção.

> [!NOTE]
> No Android não é possível remover o conteúdo indexado nos resultados da pesquisa.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Respondendo a um Link profundo

Quando o conteúdo indexado for exibido nos resultados da pesquisa e é selecionado por um usuário, o `App` classe para o aplicativo receberá uma solicitação para lidar com o `Uri` contido no conteúdo indexado. Essa solicitação pode ser processada na [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) substituído, conforme demonstrado no exemplo de código a seguir:

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

O [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) método verifica se o recebido `Uri` destina-se para o aplicativo, antes da análise a `Uri` para a página para onde navegar para e o parâmetro a ser passado para a página. Uma instância da página para ser navegados é criado e o `TodoItem` representado por uma página que o parâmetro é recuperado. O [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página para onde navegar para, em seguida, é definido como o `TodoItem`. Isso garante que, quando o `TodoItemPage` é exibida pela [ `PushAsync` ](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) método, ele estará mostrando os `TodoItem` cujo `ID` está contida no link profundo.

## <a name="making-content-available-for-search-indexing"></a>Disponibilizando o conteúdo para a indexação de pesquisa

Cada vez que a página representada por um link profundo é exibida, o [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriedade pode ser definida como `true`. No iOS e Android isso faz com que o [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) instância disponível para a indexação de pesquisa e somente no iOS, também faz o `AppLinkEntry` instância disponível para entrega. Para obter mais informações sobre a entrega, consulte [Introdução à entrega](~/ios/platform/handoff.md).

O exemplo de código a seguir demonstra a configuração de [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriedade a ser `true` no [ `Page.OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituir:

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

Da mesma forma, quando a página representada por um link profundo é navegada para fora, o [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) propriedade pode ser definida como `false`. No iOS e Android, isso interrompe a [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) publicidade interrompe a instância sendo anunciada para indexação de pesquisa e sobre TI apenas, do iOS também o `AppLinkEntry` instância para entrega. Isso pode ser feito na [ `Page.OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) substituído, conforme demonstrado no exemplo de código a seguir:

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>Fornece dados para entrega

No iOS, os dados específicos do aplicativo podem ser armazenados durante a indexação de página. Isso é feito pela adição de dados para o [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) coleção, que é um `Dictionary<string, string>` para armazenar pares chave-valor que são usados na entrega. Entrega é uma maneira para que o usuário iniciar uma atividade em um dos seus dispositivos e continuar essa atividade em outro de seus dispositivos (conforme identificado pela conta do iCloud do usuário). O código a seguir mostra um exemplo de armazenar pares de chave-valor específico do aplicativo:

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Valores armazenados na [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) coleção será armazenada nos metadados para a página indexada e será restaurada quando o usuário toca em um resultado de pesquisa que contém um link profundo (ou quando a entrega é usada para exibir o conteúdo em outro conectado no dispositivo).

Além disso, os valores para as seguintes chaves podem ser especificados:

- `contentType` – um `string` que especifica o identificador de tipo de uniforme do conteúdo indexado. A convenção recomendada a ser usado para esse valor é o nome do tipo de página que contém o conteúdo indexado.
- `associatedWebPage` – um `string` que representa a página da web para visitar se o conteúdo indexado também pode ser exibido na web, ou se o aplicativo dá suporte a links profundos do Safari.
- `shouldAddToPublicIndex` – uma `string` deles `true` ou `false` que controla se deve ou não adicionar o conteúdo indexado ao índice de nuvem pública da Apple, que, em seguida, pode ser apresentada aos usuários que ainda não instalou o aplicativo em seu dispositivo iOS. No entanto, apenas porque o conteúdo tiver sido definido para indexação de público, isso não significa que ele será automaticamente adicionado ao índice de nuvem pública da Apple. Para obter mais informações, consulte [indexação de pesquisa público](~/ios/platform/search/nsuseractivity.md). Observe que essa chave deve ser definida como `false` ao adicionar dados pessoais para o [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) coleção.

> [!NOTE]
> O `KeyValues` coleção não é usada na plataforma Android.

Para obter mais informações sobre a entrega, consulte [Introdução à entrega](~/ios/platform/handoff.md).

## <a name="summary"></a>Resumo

Este artigo explicou como usar a indexação de aplicativo e vinculação profunda para tornar o conteúdo do aplicativo xamarin. Forms pesquisáveis em dispositivos iOS e Android. Indexação de aplicativo permite que os aplicativos permanecer relevante por apareçam nos resultados da pesquisa que caso contrário, seriam esquecidos sobre depois de alguns usa.

## <a name="related-links"></a>Links relacionados

- [Vinculação profunda (amostra)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [APIs de pesquisa do iOS](~/ios/platform/search/index.md)
- [Vinculação de aplicativo no Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
