---
title: 'Xamarin.Essentials: Tema do aplicativo'
description: Este documento descreve a API tema do aplicativo solicitado no Xamarin.Essentials, que fornece informações sobre qual estilo de tema é solicitado para o aplicativo em execução.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: f322855f26d7a57acc06e97e0c97ab201c3fa586
ms.sourcegitcommit: a9280318bf7bb69e4e5744ee739e76a9cba36b28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82047394"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Tema do aplicativo

A API **RequestedTheme** faz [`AppInfo`](app-information.md) parte da classe e fornece informações sobre qual tema é solicitado para o seu aplicativo em execução pelo sistema.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Usando requestedTheme

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obtenção de informações temáticas

O tema de aplicação solicitado pode ser detectado com a seguinte API:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Isso fornecerá o tema atual solicitado pelo sistema para sua aplicação. O valor de retorno será um dos seguintes:

* Não Especificado
* Leve
* Escuro

Não especificado será devolvido quando o sistema operacional não tiver um estilo específico de interface de usuário para solicitar. Um exemplo disso está nos dispositivos que executam versões do iOS com mais de 13.0.


## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

O Android usa modos de configuração para especificar o tipo de tema a ser solicitado ao usuário. Com base na versão do Android, ele pode ser alterado pelo usuário ou é alterado quando o modo de economia de bateria está ativado.

Você pode ler mais sobre a documentação oficial do [Android para O Tema Escuro](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Não especificado será sempre retornado em versões do iOS com mais de 13.0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Os aplicativos UWP respeitarão sua configuração no UWP App.xaml em **RequestedTheme**. Se for definido como um tema específico, o Xamarin.Essentials sempre retornará essa configuração. Para usar o tema dinâmico do SISTEMA OPERACIONAL remova este nó do seu aplicativo e, em seguida, quando o aplicativo é executado, ele retornará o tema definido pelo usuário nas configurações do Windows (**Configurações > Personalização > Cores > Escolha o modo de aplicativo padrão**).

Você pode ler mais sobre a [Documentação temática solicitada da UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Código-fonte de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)
