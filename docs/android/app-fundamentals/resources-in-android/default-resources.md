---
title: "Recursos padrão"
ms.topic: article
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3d9c747cdf8e43f33b9310ac1156550066b400eb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="default-resources"></a>Recursos padrão

Recursos padrão são itens que não são específicas a qualquer determinado dispositivo ou o fator de forma e, portanto, a opção padrão do sistema operacional Android se nenhum mais específico recursos podem ser encontrados. Assim, eles são o tipo mais comum de recurso para criar. Eles são organizados em subdiretórios do **recursos** diretório de acordo com seu tipo de recurso:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Arquivos de recurso padrão](default-resources-images/01-resource-files-vs.png)

Na imagem acima, o projeto tem valores padrão para recursos drawable, layouts e valores (arquivos XML que contêm valores simples).

Uma lista completa dos tipos de recursos é fornecida abaixo:

-  **Animator** &ndash; arquivos XML que descrevem as animações de propriedade.
   Animações de propriedade foram introduzidos no nível de API 11 (Android 3.0) e fornece para a animação de propriedades em um objeto. Animações de propriedade são uma maneira mais flexível e poderosa para descrever animações em qualquer tipo de objeto.

-  **anim** &ndash; arquivos XML que descrevem *interpolação* animações. Animações de interpolação são uma série de instruções de animação para realizar transformações no conteúdo de uma rotação de objeto ou de exemplo, de modo de exibição de uma imagem ou aumentando o tamanho do texto. Animações de interpolação são limitadas para exibir somente os objetos.

-  **cor** &ndash; arquivos XML que descrevem uma lista de estado de cores. Para compreender as listas de estado de cores, considere um widget de interface do usuário, como um botão.
   Pode fazer tiverem estados diferentes, como pressionado ou desabilitado e o botão pode alterar a cor com cada alteração de estado. A lista é expresso em uma lista de estado.

-  **drawable** &ndash; Drawable recursos são um conceito geral de elementos gráficos que podem ser compilados no aplicativo e, em seguida, acessados por chamadas de API ou referenciados por outros recursos do XML.
   Alguns exemplos de drawables são arquivos de bitmap (. png,. gif,. jpg), especiais bitmaps redimensionáveis conhecido como [nove Patches](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), estado lista genéricas formas definidas no XML, etc.
 
-  **layout** &ndash; arquivos XML que descrevem um layout de interface do usuário, como uma atividade ou uma linha em uma lista.

-  **menu** &ndash; arquivos XML que descrevem os menus do aplicativo, como *opções Menus*, *Menus de contexto*, e *submenus*. Para obter um exemplo de menus, consulte o [demonstração do Menu pop-up](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) ou [controles padrão](https://developer.xamarin.com/samples/mobile/StandardControls/) exemplo.

-  **bruto** &ndash; arquivos arbitrários que são salvos em sua forma bruta, binária. Esses arquivos são compilados em um aplicativo do Android em um formato binário.

-  **valores** &ndash; arquivos XML que contêm valores simples. Um arquivo XML no diretório de valores não define um único recurso, mas em vez disso, pode definir vários recursos. Por exemplo um arquivo XML pode conter uma lista de valores de cadeia de caracteres, enquanto outro arquivo XML pode conter uma lista de valores de cor.

-  **XML** &ndash; arquivos XML que são semelhantes em função para os arquivos de configuração do .NET. Esses são XML arbitrário que pode ser lido pelo aplicativo em tempo de execução


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Arquivos de recurso padrão](default-resources-images/01-resource-files-xs.png)

Na imagem acima, o projeto tem valores padrão para recursos drawable, layouts e valores (arquivos XML que contêm valores simples).

Uma lista completa dos tipos de recursos é fornecida abaixo:

-  **Animator** &ndash; arquivos XML que descrevem as animações de propriedade.
   Animações de propriedade foram introduzidos no nível de API 11 (Android 3.0) e fornece para a animação de propriedades em um objeto. Animações de propriedade são uma maneira mais flexível e poderosa para descrever animações em qualquer tipo de objeto.

-  **anim** &ndash; arquivos XML que descrevem *interpolação* animações. Animações de interpolação são uma série de instruções de animação para realizar transformações no conteúdo de uma rotação de objeto ou de exemplo, de modo de exibição de uma imagem ou aumentando o tamanho do texto. Animações de interpolação são limitadas para exibir somente os objetos.

-  **cor** &ndash; arquivos XML que descrevem uma lista de estado de cores. Para compreender as listas de estado de cores, considere um widget de interface do usuário, como um botão.
   Pode fazer tiverem estados diferentes, como pressionado ou desabilitado e o botão pode alterar a cor com cada alteração de estado. A lista é expresso em uma lista de estado.

-  **fonte** &ndash; iniciando no nível de API 26, é possível incorporar fontes como um recurso em um aplicativo do Android. 26 de biblioteca de suporte será backport fontes em nível de API 14. A incorporação de fontes permite que os aplicativos

-  **Mipmap** &ndash; Drawable recursos são um conceito geral de elementos gráficos que podem ser compilados no aplicativo e, em seguida, acessados por chamadas de API ou referenciados por outros recursos do XML.
   Alguns exemplos de drawables são arquivos de bitmap (. png,. gif,. jpg), especiais bitmaps redimensionáveis conhecido como [nove Patches](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), estado lista genéricas formas definidas no XML, etc.

-  **layout** &ndash; arquivos XML que descrevem um layout de interface do usuário, como uma atividade ou uma linha em uma lista.

-  **menu** &ndash; arquivos XML que descrevem os menus do aplicativo, como *opções Menus*, *Menus de contexto*, e *submenus*. Para obter um exemplo de menus, consulte o [demonstração do Menu pop-up](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) ou [controles padrão](https://developer.xamarin.com/samples/mobile/StandardControls/) exemplo.

-  **bruto** &ndash; arquivos arbitrários que são salvos em sua forma bruta, binária. Esses arquivos são compilados em um aplicativo do Android em um formato binário.

-  **valores** &ndash; arquivos XML que contêm valores simples. Um arquivo XML no diretório de valores não define um único recurso, mas em vez disso, pode definir vários recursos. Por exemplo um arquivo XML pode conter uma lista de valores de cadeia de caracteres, enquanto outro arquivo XML pode conter uma lista de valores de cor.

-  **XML** &ndash; arquivos XML que são semelhantes em função para os arquivos de configuração do .NET. Esses são XML arbitrário que pode ser lido pelo aplicativo em tempo de execução

-----
