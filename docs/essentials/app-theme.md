---
Título: " Xamarin.Essentials : aplicativo tema" Descrição: "este documento descreve a API de tema do aplicativo solicitada no Xamarin.Essentials , que fornece informações sobre qual estilo de tema é solicitado para o aplicativo em execução".
MS. AssetID: F6F6D496-A8A9-4B9A-AF1A-370D937E5073 autor: jamesmontemagno MS. Custom: vídeo MS. Author: Jamont MS. Date: 01/06/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Tema do aplicativo

A API **RequestedTheme** faz parte da [`AppInfo`](app-information.md) classe e fornece informações sobre qual tema é solicitado para seu aplicativo em execução pelo sistema.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Usando RequestedTheme

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obtendo informações de tema

O tema do aplicativo solicitado pode ser detectado com a seguinte API:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Isso fornecerá o tema atual solicitado pelo sistema para seu aplicativo. O valor de retorno será um dos seguintes:

* Não Especificado
* Leve
* Escuro

Não especificado será retornado quando o sistema operacional não tiver um estilo de interface de usuário específico a ser solicitado. Um exemplo disso é em dispositivos que executam versões do iOS anteriores a 13,0.


## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

O Android usa modos de configuração para especificar o tipo de tema a ser solicitado do usuário. Com base na versão do Android, ele pode ser alterado pelo usuário ou alterado quando o modo de economia de bateria está habilitado.

Você pode ler mais sobre a documentação oficial do [Android para tema escuro](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Não especificado sempre será retornado em versões do iOS com mais de 13,0


# <a name="uwp"></a>[UWP](#tab/uwp)

`RequestedTheme`A chamada deve ser chamada no thread da interface do usuário ou uma exceção será gerada.

Os aplicativos UWP respeitarão sua configuração no aplicativo UWP. XAML em **RequestedTheme**. Se estiver definido como um tema específico, Xamarin.Essentials o sempre retornará essa configuração. Para usar o tema dinâmico do sistema operacional, remova este nó do seu aplicativo e, em seguida, quando seu aplicativo for executado, ele retornará o tema definido pelo usuário nas configurações do Windows (**configurações > personalização > cores > escolha o modo de aplicativo padrão**).

Você pode ler mais sobre a [documentação do tema solicitado por UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Código-fonte de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Theme-Detection-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
