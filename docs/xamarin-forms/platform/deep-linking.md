---
title: Indexação de aplicativo e vinculação profunda
description: Este artigo demonstra como usar a indexação de aplicativo e vinculação profunda para tornar o conteúdo do aplicativo xamarin. Forms pesquisável em dispositivos iOS e Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: 9cc5177a585af1569385840ab8c370993984ca2b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242486"
---
# <a name="application-indexing-and-deep-linking"></a>Indexação de aplicativo e vinculação profunda

_Indexação de aplicativo permite que os aplicativos que seriam esquecidos caso contrário, depois de alguns usa permaneça relevante por apareçam nos resultados da pesquisa. Vinculação profunda permite que aplicativos responder a um resultado de pesquisa que contém dados de aplicativos, normalmente, navegando até uma página referenciada por um link profundo. Este artigo demonstra como usar a indexação de aplicativo e vinculação profunda para tornar o conteúdo do aplicativo xamarin. Forms pesquisável em dispositivos iOS e Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Profundidade da vinculação com xamarin. Forms e o Azure, pelo [University Xamarin](https://university.xamarin.com/)**


Indexação de aplicativo xamarin. Forms e vinculação profunda fornecem uma API para metadados de publicação para indexação de aplicativo, como os usuários navegam por meio de aplicativos. Conteúdo indexado pode ser pesquisado na pesquisa do Spotlight, pesquisa do Google ou uma pesquisa na web. Toque em um resultado de pesquisa que contém um link profundo acionarão um evento que pode ser tratado por um aplicativo e é normalmente usado para navegar até a página referenciada do link profundo.

O aplicativo de exemplo demonstra um aplicativo de lista de tarefas em que os dados são armazenados em um banco de dados local do SQLite, conforme mostrado nas capturas de tela seguir:

![](deep-linking-images/screenshots.png "Aplicativo de lista de tarefas")

Cada `TodoItem` instância criada pelo usuário que está sendo indexada. Pesquisa específicos da plataforma, em seguida, pode ser usada para localizar dados indexados do aplicativo. Quando o usuário toca em um item de resultado de pesquisa para o aplicativo, o aplicativo é iniciado, o `TodoItemPage` é direcionada e o `TodoItem` referenciado de profundas link será exibido.

Para obter mais informações sobre como usar um banco de dados SQLite, consulte [trabalhando com um banco de dados Local](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> Indexação de aplicativo xamarin. Forms e profundo vinculação funcionalidade só está disponíveis nas plataformas Android e iOS e requer iOS 9 e API 23 respectivamente.

## <a name="setup"></a>Configuração

As seções a seguir fornecem as instruções de instalação adicionais para usar esse recurso nas plataformas Android e iOS.

### <a name="ios"></a>iOS

Na plataforma iOS, não há nenhuma configuração adicional necessária para usar essa funcionalidade.

### <a name="android"></a>Android

Na plataforma Android, há um número de pré-requisitos que devem ser atendidas para usar a indexação de aplicativo e profundo vinculação funcionalidade:

1. Uma versão do seu aplicativo deve estar ativa no Google Play.
1. Um site complementar deve ser registrado em relação ao aplicativo no Console do desenvolvedor do Google. Quando o aplicativo está associado um site, URLs podem ser indexados que trabalham para o site e o aplicativo, o que pode ser usado, em seguida, nos resultados da pesquisa. Para obter mais informações, consulte [aplicativo a indexação de pesquisa do Google](https://support.google.com/googleplay/android-developer/answer/6041489) no site do Google.
1. Seu aplicativo deve oferecer suporte a tentativas de URL HTTP no `MainActivity` classe, que informam ao aplicativo de indexação que tipos de esquemas de URL de dados de aplicativo pode responder. Para obter mais informações, consulte [Configurando o filtro de intenção de](~/android/platform/app-linking.md#configure-intent-filter).

Depois que esses pré-requisitos forem atendidos, a seguinte configuração adicional é necessária para usar a indexação de aplicativo xamarin. Forms e vinculação profunda na plataforma Android:

1. Instalar o [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) pacote NuGet para o projeto de aplicativo do Android.
1. No `MainActivity.cs` do arquivo, importe o `Xamarin.Forms.Platform.Android.AppLinks` namespace.
1. No `MainActivity.OnCreate` substituir, adicione a seguinte linha de código abaixo `Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

Para obter mais informações, consulte [profunda Link de conteúdo com navegação de URL com xamarin. Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) no blog do Xamarin.

## <a name="indexing-a-page"></a>Indexação de uma página

O processo de indexação de uma página e expô-lo para pesquisa Google e destaque é o seguinte:

1. Criar um [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) que contém os metadados necessários para a página, junto com um link profundo para retornar à página quando o usuário seleciona o conteúdo indexado nos resultados da pesquisa de índice.
1. Registrar o [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância para o índice de pesquisa.

O exemplo de código a seguir demonstra como criar um [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância:

```csharp
AppLinkEntry GetAppLink (TodoItem item)
{
  var pageType = GetType ().ToString ();
  var pageLink = new AppLinkEntry {
    Title = item.Name,
    Description = item.Notes,
    AppLinkUri = new Uri (string.Format ("http://{0}/{1}?id={2}",
      App.AppName, pageType, WebUtility.UrlEncode (item.ID)), UriKind.RelativeOrAbsolute),
    IsLinkActive = true,
    Thumbnail = ImageSource.FromFile ("monkey.png")
  };

  return pageLink;
}
```

O [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância contém um número de propriedades cujos valores são necessárias para a página de índice e criar um link profundo. O [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Title/), [ `Description` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Description/), e [ `Thumbnail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Thumbnail/) propriedades são usadas para identificar o conteúdo indexado quando ele for exibido nos resultados da pesquisa. O [ `IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) está definida como `true` para indicar que o conteúdo indexado está sendo exibido no momento. O [ `AppLinkUri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.AppLinkUri/) propriedade é um `Uri` que contém as informações necessárias para retornar à página atual e exibir atual `TodoItem`. O exemplo a seguir mostra um exemplo `Uri` para o aplicativo de exemplo:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

Isso `Uri` contém todas as informações necessárias para iniciar o `deeplinking` aplicativo, navegue até o `DeepLinking.TodoItemPage`e exibir o `TodoItem` que tem um `ID` de `ec38ebd1-811e-4809-8a55-0d028fce7819`.

## <a name="registering-content-for-indexing"></a>Registrando o conteúdo para indexação

Uma vez um [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância tiver sido criada, ele deve ser registrado para a indexação apareçam nos resultados da pesquisa. Isso é feito com o [ `RegisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.RegisterLink/p/Xamarin.Forms.IAppLinkEntry/) método, conforme demonstrado no exemplo de código a seguir:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Isso adiciona o [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância para o aplicativo [ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/) coleção.

> [!NOTE]
> O `RegisterLink` método também pode ser usado para atualizar o conteúdo foi indexado para uma página.

Uma vez um [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância foi registrada para indexação, ele pode aparecer nos resultados da pesquisa. Captura de tela a seguir mostra o conteúdo indexado apareçam nos resultados da pesquisa na plataforma iOS:

![](deep-linking-images/ios-search.png "Conteúdo indexado nos resultados da pesquisa no iOS")

## <a name="de-registering-indexed-content"></a>Cancelar registro indexada conteúdo

O [ `DeregisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.DeregisterLink/p/Xamarin.Forms.IAppLinkEntry/) método é usado para remover o conteúdo indexado nos resultados da pesquisa, como demonstrado no exemplo de código a seguir:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Isso remove o [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância a partir do aplicativo [ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/) coleção.

> [!NOTE]
> No Android não é possível remover o conteúdo indexado de resultados da pesquisa.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Respondendo a um Link profundo

Quando o conteúdo indexado aparece nos resultados da pesquisa e é selecionado por um usuário, o `App` de classe para o aplicativo receberá uma solicitação para lidar com o `Uri` contido no conteúdo indexado. Essa solicitação pode ser processada no [ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/) substituir, conforme demonstrado no exemplo de código a seguir:

```csharp
public class App : Application
{
  ...

  protected override async void OnAppLinkRequestReceived (Uri uri)
  {
    string appDomain = "http://" + App.AppName.ToLowerInvariant () + "/";
    if (!uri.ToString ().ToLowerInvariant ().StartsWith (appDomain)) {
      return;
    }

    string pageUrl = uri.ToString ().Replace (appDomain, string.Empty).Trim ();
    var parts = pageUrl.Split ('?');
    string page = parts [0];
    string pageParameter = parts [1].Replace ("id=", string.Empty);

    var formsPage = Activator.CreateInstance (Type.GetType (page));
    var todoItemPage = formsPage as TodoItemPage;
    if (todoItemPage != null) {
      var todoItem = App.Database.Find (pageParameter);
      todoItemPage.BindingContext = todoItem;
      await MainPage.Navigation.PushAsync (formsPage as Page);
    }

    base.OnAppLinkRequestReceived (uri);
  }
}
```

O [ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/) método verifica se recebido `Uri` destina-se para o aplicativo, antes de analisar o `Uri` para a página de navegação e o parâmetro a ser passado para a página. Uma instância da página a ser navegados é criado e o `TodoItem` representados pela página de parâmetro é recuperado. O [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) da página para onde navegar para, em seguida, é definido como o `TodoItem`. Isso garante que, quando o `TodoItemPage` é exibido pelo [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/) método, ele ser exibida a `TodoItem` cujo `ID` está contida no link profundo.

## <a name="making-content-available-for-search-indexing"></a>Disponibilizando o conteúdo para a indexação de pesquisa

Cada vez que a página representada por um link profundo é exibida, o [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propriedade pode ser definida como `true`. No iOS e Android, isso torna o [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância disponível para indexação de pesquisa e somente iOS, também faz com que o `AppLinkEntry` instância disponível para entrega. Para obter mais informações sobre a entrega, consulte [Introdução à entrega](~/ios/platform/handoff.md).

O exemplo de código a seguir mostra a configuração de [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propriedade `true` no [ `Page.OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) substituir:

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

Da mesma forma, quando a página representada por um link profundo é navegada para fora, o [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) propriedade pode ser definida como `false`. No iOS e Android, isso interrompe o [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) instância sendo anunciada para indexação de pesquisa e, no iOS única, TI também interrompe anúncios a `AppLinkEntry` instância para entrega. Isso pode ser feito no [ `Page.OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/) substituir, conforme demonstrado no exemplo de código a seguir:

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>Fornecimento de dados da entrega

No iOS, os dados específicos do aplicativo podem ser armazenados durante a indexação de página. Isso é feito pela adição de dados para o [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) coleção, que é um `Dictionary<string, string>` para armazenar pares de chave-valor que são usados na entrega. Entrega é uma maneira para que o usuário iniciar uma atividade em um de seus dispositivos e continuar essa atividade em outro seus dispositivos (conforme identificado pela conta do iCloud do usuário). O código a seguir mostra um exemplo de armazenamento de pares de chave-valor específico do aplicativo:

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

Valores armazenados no [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) coleção será armazenada nos metadados para a página indexada e será restaurada quando o usuário toca em um resultado de pesquisa que contém um link profundo (ou quando a entrega é usada para exibir o conteúdo em outro conectado no dispositivo).

Além disso, os valores para as chaves a seguir podem ser especificados:

- `contentType` – um `string` que especifica o identificador de tipo uniforme do conteúdo indexado. A convenção recomendada a ser usado para esse valor é o nome do tipo de página que contém o conteúdo indexado.
- `associatedWebPage` – um `string` que representa a página da web para visitar se o conteúdo indexado também pode ser exibido na web, ou se o aplicativo dá suporte a links profundos do Safari.
- `shouldAddToPublicIndex` – um `string` do `true` ou `false` que controla se deve ou não adicionar o conteúdo indexado para o índice de nuvem pública da Apple, que, em seguida, pode ser apresentada aos usuários que ainda não instalou o aplicativo em seu dispositivo iOS. No entanto, apenas porque o conteúdo tiver sido definido para indexação pública, isso não significa que ele será automaticamente adicionado ao índice de nuvem pública da Apple. Para obter mais informações, consulte [indexação pública da pesquisa](~/ios/platform/search/nsuseractivity.md). Observe que essa chave deve ser definida como `false` ao adicionar dados pessoais para o [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) coleção.

> [!NOTE]
> O `KeyValues` coleção não é usada na plataforma Android.

Para obter mais informações sobre a entrega, consulte [Introdução à entrega](~/ios/platform/handoff.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar a indexação de aplicativo e vinculação profunda para tornar o conteúdo do aplicativo xamarin. Forms pesquisável em dispositivos iOS e Android. Indexação de aplicativo permite que aplicativos fique relevantes, que aparecem nos resultados da pesquisa que outra forma seriam esquecidos sobre após alguns usa.


## <a name="related-links"></a>Links relacionados

- [Vínculo profundo (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [APIs de pesquisa do iOS](~/ios/platform/search/index.md)
- [Vinculação de aplicativo no Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)
- [IAppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinkEntry/)
- [IAppLinks](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinks/)
