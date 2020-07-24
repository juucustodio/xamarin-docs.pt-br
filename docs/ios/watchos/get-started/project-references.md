---
title: Referências de projeto watchOS no Xamarin
description: Este documento descreve a relação entre um aplicativo iOS, um aplicativo de inspeção e uma extensão de aplicativo de inspeção. Ele aborda referências de projeto e identificadores de pacote.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 96873e1bff34a4ef3ed76d675ca0a2b5c03f0d72
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997235"
---
# <a name="watchos-project-references-in-xamarin"></a>Referências de projeto watchOS no Xamarin

_Explicação da relação entre o aplicativo iOS, o aplicativo de inspeção e a extensão de inspeção._

Os três projetos em uma solução watchOS são *automaticamente configurados* para referenciar uns aos outros de uma maneira específica para que os aplicativos watchOS 3 sejam compilados e agrupados corretamente. Essas referências de projeto e as configurações de identificador de pacote são descritas abaixo para referência.

## <a name="project-references"></a>Referências de Projeto

Exiba as referências clicando duas vezes nos nós de referências para cada projeto:

- **aplicativo de observação** de referências de **aplicativo do iPhone**

  ![Aplicativo de observação de referências de aplicativo do iPhone](project-references-images/catalog-reference1.png)

- **Inspecionar** referências de aplicativo **inspecionar extensão de aplicativo**

  ![Aplicativo de observação de referências de aplicativo do iPhone](project-references-images/catalog-reference2.png)

- A **extensão do aplicativo de inspeção** não faz referência a nenhum dos outros projetos

  ![A extensão do aplicativo de inspeção não faz referência a outros projetos](project-references-images/catalog-reference3.png)

## <a name="bundle-identifiers"></a>Identificadores de pacote

Você também precisa ter certeza de que seus **identificadores de pacote** estão corretos.
Todos os três projetos devem ter o *mesmo* prefixo de identificador, com os dois projetos de inspeção com extensões predefinidas de `watchkitextension` e, da `watchkitapp` seguinte maneira (para o exemplo de **WatchKitCatalog** ):

- Projeto unificado do Xamarin. iOS-`com.xamarin.WatchKitCatalog`

- Projeto de extensão WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`

- Inspecionar projeto de aplicativo-`com.xamarin.WatchKitCatalog.watchkitapp`

Verifique também se essas configurações do **info. plist** estão corretas:

- O projeto do aplicativo de inspeção `WKCompanionAppBundleIdentifier` corresponde à ID do pacote do aplicativo pai/contêiner (por ex., o que é executado no iPhone);

- A **ID do pacote WKApp** do projeto de extensão do Watch kit corresponde à ID do pacote do projeto do aplicativo de inspeção.

Você pode editar os identificadores clicando duas vezes no arquivo **info. plist** em cada projeto.

Essa captura de tela é o arquivo info. plist **da extensão de inspeção** , mostrando também o identificador **do aplicativo de inspeção** :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Esta captura de tela é o arquivo info. plist da extensão de inspeção](project-references-images/infoplist-extension.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Esta captura de tela é o arquivo info. plist da extensão de inspeção](project-references-images/infoplist-extension-vs.png)

-----

Essa captura de tela é o arquivo info. plist **do aplicativo Watch** .
A versão atual do **sistema operacional de inspeção** é 8,2, portanto, o **destino de implantação** para o aplicativo de inspeção deve ser **8,2**. Observe que, se você tiver o Xcode 6,3 instalado, esse valor poderá ser definido como 8,3-você deve alterá-lo 8,2.

![O arquivo Watch info. plist](project-references-images/infoplist-watchapp.png)

O destino de implantação para o aplicativo Watch pode ser diferente da extensão Watch e do aplicativo iOS.
