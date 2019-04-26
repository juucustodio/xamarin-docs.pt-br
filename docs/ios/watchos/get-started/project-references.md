---
title: Referências de projeto no Xamarin do watchOS
description: Este documento descreve a relação entre um aplicativo iOS, um aplicativo de inspeção e uma extensão de aplicativo de inspeção. Ele aborda as referências de projeto e pacote de identificadores.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c900ab714fed2bb1e02367ba39ad3c5a0a76121e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408295"
---
# <a name="watchos-project-references-in-xamarin"></a>Referências de projeto no Xamarin do watchOS

_Explicação da relação entre o aplicativo iOS, o aplicativo de inspeção e a extensão de inspeção._

São os três projetos em uma solução watchOS *configuradas automaticamente* para fazer referência uns aos outros de forma específica para aplicativos do watchOS 3 ser compilado e empacotado corretamente. Essas referências de projeto e configurações do identificador de pacote são descritas abaixo para referência.

## <a name="project-references"></a>Referências de Projeto

Exiba as referências clicando duas vezes em nós de referências para cada projeto:

- **aplicativo de iPhone** referências **aplicativo Watch**

![](project-references-images/catalog-reference1.png "Aplicativo de inspeção faz referência a aplicativo do iPhone")

- **Assista ao aplicativo** referências **extensão de aplicativo de inspeção**

![](project-references-images/catalog-reference2.png "Aplicativo de inspeção faz referência a aplicativo do iPhone")


 - O **extensão de aplicativo de inspeção** não faz referência a qualquer um dos outros projetos

![](project-references-images/catalog-reference3.png "Assista a que extensão do aplicativo não faz referência a outros projetos")



## <a name="bundle-identifiers"></a>Identificadores de pacote

Você também precisará certificar-se sua **identificadores de pacote** estão corretos.
Todos os três projetos devem ter o *mesmo* prefixo do identificador, com os projetos de dois inspeção tendo predefinidos extensões do `watchkitextension` e `watchkitapp`, da seguinte maneira (para o **WatchKitCatalog** exemplo):

 - Projeto do xamarin. IOS unificado- `com.xamarin.WatchKitCatalog`

 - Projeto de extensão WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`

 - Projeto de aplicativo Watch- `com.xamarin.WatchKitCatalog.watchkitapp`

Também verifique se esses **Info. plist** configurações estão corretas:

 - O projeto de aplicativo Watch `WKCompanionAppBundleIdentifier` corresponda à ID de pacote do aplicativo recipiente/pai (ie. aquele que é executado no iPhone);

 - O projeto de extensão do Kit de inspeção **ID do pacote de WKApp** corresponde à ID de pacote. do projeto de aplicativo de inspeção

Você pode editar os identificadores clicando duas vezes sobre o **Info. plist** arquivo em cada projeto.

Esta captura de tela é o **extensão de inspeção** arquivo Info. plist, mostrando as **aplicativo de inspeção** identificador também:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "Esta captura de tela é o arquivo de info. plist da extensão de inspeção")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "Esta captura de tela é o arquivo de info. plist da extensão de inspeção")

-----

Esta captura de tela é o **aplicativo de inspeção** arquivo Info. plist.
Atual **SO Watch** versão é 8.2, portanto, o **destino de implantação** para o aplicativo Watch deve ser **8.2**. Observe que, se você tiver 6.3 Xcode instalado, esse valor pode ser definido para 8.3 – você deve alterá-lo 8.2.

![](project-references-images/infoplist-watchapp.png "O arquivo Info. plist do watch")

O destino de implantação para o aplicativo Watch pode ser diferente da extensão de inspeção e aplicativo do iOS.

