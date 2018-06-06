---
title: watchOS referências de projeto em Xamarin
description: Este documento descreve a relação entre um aplicativo iOS, um aplicativo de inspeção e uma extensão de aplicativo de inspeção. Ele aborda as referências do projeto e pacote de identificadores.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 1bd950d0929beae7133b0eb8ef6b2a69bc116f50
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791482"
---
# <a name="watchos-project-references-in-xamarin"></a>watchOS referências de projeto em Xamarin

_Explicação da relação entre o aplicativo iOS, o aplicativo de inspeção e a extensão de inspeção._

São três projetos em uma solução watchOS *configurada automaticamente* para fazer referência a si de uma maneira específica para aplicativos watchOS 3 ser criado e agrupados corretamente. Essas referências do projeto e configurações do identificador de pacote são descritas abaixo para referência.

## <a name="project-references"></a>Referências de Projeto

Exiba as referências clicando duas vezes em nós de referências para cada projeto:

- **aplicativo de iPhone** referências **aplicativo Watch**

![](project-references-images/catalog-reference1.png "aplicativo de iPhone referencia aplicativo Watch")

- **Assista a aplicativo** referências **inspeção da extensão do aplicativo**

![](project-references-images/catalog-reference2.png "aplicativo de iPhone referencia aplicativo Watch")


 - O **inspeção da extensão do aplicativo** não faz referência a qualquer um dos outros projetos

![](project-references-images/catalog-reference3.png "Assista a que extensão do aplicativo não faz referência a outros projetos")



## <a name="bundle-identifiers"></a>Identificadores de pacote

Você também precisa verificar se seu **identificadores de pacote** estão corretas.
Todos os três projetos devem ter o *mesmo* prefixo do identificador, com os projetos de duas inspecionar tendo predefinidos extensões de `watchkitextension` e `watchkitapp`, da seguinte maneira (para o **WatchKitCatalog** exemplo):

 - Xamarin unificada projeto- `com.xamarin.WatchKitCatalog`

 - Projeto de extensão WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`

 - Projeto de aplicativo de inspeção- `com.xamarin.WatchKitCatalog.watchkitapp`

Também verifique se esses **Info. plist** configurações estão corretas:

 - O projeto de aplicativo Watch `WKCompanionAppBundleIdentifier` coincide com a ID do pacote do aplicativo de contêiner/pai (ie. aquele que é executado no iPhone);

 - O projeto de extensão do Kit de inspeção **ID do pacote WKApp** coincide com a ID de pacote. do projeto de aplicativo Watch

Você pode editar os identificadores clicando duas vezes no **Info. plist** arquivo em cada projeto.

Esta captura de tela é o **da extensão de inspeção** arquivo Info. plist, mostrando o **do aplicativo Watch** identificador também:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
![](project-references-images/infoplist-extension.png "Esta captura de tela é o inspecionar arquivo da extensão. plist")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
![](project-references-images/infoplist-extension-vs.png "Esta captura de tela é o inspecionar arquivo da extensão. plist")

-----

Esta captura de tela é o **do aplicativo Watch** arquivo Info. plist.
Atual **inspecionar OS** versão é 8.2, portanto, o **destino de implantação** para o aplicativo Watch deve ser **8.2**. Observe que, se você tiver o Xcode 6.3 instalado, esse valor pode ser definido para 8.3 – você deve alterá-lo 8.2.

![](project-references-images/infoplist-watchapp.png "O arquivo Info. plist do relógio")

O destino de implantação para o aplicativo Watch pode ser diferente da extensão de inspeção e aplicativo iOS.

