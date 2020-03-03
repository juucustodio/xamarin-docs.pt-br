---
title: 'Xamarin.Essentials: Tema do aplicativo'
description: Este documento descreve a API de Tema do aplicativo solicitado no Xamarin.Essentials, que fornece informações sobre qual estilo de tema é solicitado para o aplicativo em execução.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 39f650a73f03888e50c6259c1052e24dbea0a4f5
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555664"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: Tema do aplicativo

A API **RequestedTheme** faz parte da classe [AppInfo](/app-information.md) e fornece informações sobre qual tema é solicitado para o aplicativo em execução pelo sistema.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>Usar RequestedTheme

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>Obter as informações do tema

É possível detectar o tema do aplicativo solicitado com a seguinte API:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

Isso fornece o tema atual solicitado pelo sistema para o seu aplicativo. O valor de retorno será um dos seguintes:

* Não especificado
* Claro
* Escuro

Não especificado retorna quando o sistema operacional não tem um estilo específico de interface do usuário a solicitar. Um exemplo disso ocorre nos dispositivos que executam versões do iOS anteriores à 13.0.


## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

O Android usa modos de configuração para especificar o tipo de tema a solicitar do usuário. Com base na versão do Android, ele pode ser alterado pelo usuário ou quando o modo de economia de bateria é habilitado.

Leia mais a respeito na [Documentação oficial do Android para o Tema Escuro](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme).


# <a name="ios"></a>[iOS](#tab/ios)

Não especificado sempre retorna para as versões do iOS anteriores à 13.0 


# <a name="uwp"></a>[UWP](#tab/uwp)

Por padrão, seu aplicativo é executado usando o tema definido pelo usuário nas configurações do Windows (**Configurações > Personalização > Cores > Escolher o modo de aplicativo padrão**). Você pode definir a propriedade RequestedTheme do aplicativo para substituir o padrão do usuário e especificar qual tema é usado.

Leia mais sobre isso na [Documentação do tema solicitado UWP](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme).

--------------

## <a name="api"></a>API

- [Código-fonte de AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentação da API de AppInfo](xref:Xamarin.Essentials.AppInfo)
