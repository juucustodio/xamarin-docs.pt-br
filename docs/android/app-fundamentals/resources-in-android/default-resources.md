---
title: Recursos padrão
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 20865b71cce16f57b84a1c54986bd84180d3e190
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013131"
---
# <a name="default-resources"></a>Recursos padrão

Recursos padrão são itens que não são específicas para qualquer dispositivo específico ou o fator forma e, portanto, são a escolha padrão pelo sistema operacional Android se não há mais específicas podem ser encontrados recursos. Como tal, eles são o tipo mais comum de recurso a ser criado. Eles estão organizados em subdiretórios dos **recursos** diretório de acordo com seu tipo de recurso:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Arquivos de recurso padrão](default-resources-images/01-resource-files-vs.png)

Na imagem acima, o projeto tem valores padrão para recursos desenháveis, layouts e valores (arquivos XML que contêm valores simples).

Uma lista completa dos tipos de recurso é fornecida abaixo:

-  **Animator** &ndash; arquivos XML que descrevem as animações de propriedade.
   As animações de propriedade foram introduzidas no nível da API 11 (Android 3.0) e fornece para a animação de propriedades em um objeto. As animações de propriedade são uma maneira mais flexível e poderosa para descrever as animações em qualquer tipo de objeto.

-  **anim** &ndash; arquivos XML que descrevem *interpolação* animações. Animações de interpolação são uma série de instruções de animação para executar transformações no conteúdo de uma rotação de objeto ou de exemplo, de modo de exibição de uma imagem ou aumentando o tamanho do texto. Animações de interpolação são limitadas para exibir apenas os objetos.

-  **cor** &ndash; arquivos XML que descrevem uma lista de estado de cores. Para compreender as listas de estado de cores, considere um widget de interface do usuário, como um botão.
   Ele pode ter diferentes estados, como pressionado ou desabilitado e o botão pode alterar a cor com cada alteração no estado. A lista é expressa em uma lista de estado.

-  **drawable** &ndash; recursos Desenháveis são um conceito geral de elementos gráficos que podem ser compilados no aplicativo e, em seguida, acessados por chamadas à API ou referenciados por outros recursos XML.
   Alguns exemplos de desenháveis são arquivos de bitmap (. png,. gif,. jpg), em especial bitmaps redimensionável, conhecido como [nove Patches](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), listas de estado, formas genéricas definidas no XML, etc.
 
-  **layout** &ndash; arquivos XML que descrevem um layout de interface do usuário, como uma atividade ou uma linha em uma lista.

-  **menu** &ndash; arquivos XML que descrevem menus do aplicativo, como *opções Menus*, *Menus de contexto*, e *submenus*. Para obter um exemplo dos menus, consulte o [demonstração do Menu pop-up](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) ou o [controles padrão](https://developer.xamarin.com/samples/mobile/StandardControls/) exemplo.

-  **brutos** &ndash; arquivos arbitrários que são salvos em sua forma bruta e binária. Esses arquivos são compilados em um aplicativo do Android em um formato binário.

-  **valores** &ndash; arquivos XML que contêm valores simples. Um arquivo XML no diretório de valores não define um único recurso, mas em vez disso, pode definir vários recursos. Por exemplo um arquivo XML pode conter uma lista de valores de cadeia de caracteres, enquanto outro arquivo XML pode conter uma lista de valores de cor.

-  **XML** &ndash; arquivos XML que têm função semelhantes para os arquivos de configuração do .NET. Esses são XML arbitrário que pode ser lido pelo aplicativo em tempo de execução.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Arquivos de recurso padrão](default-resources-images/01-resource-files-xs.png)

Na imagem acima, o projeto tem valores padrão para recursos desenháveis, layouts e valores (arquivos XML que contêm valores simples).

Uma lista completa dos tipos de recurso é fornecida abaixo:

-  **Animator** &ndash; arquivos XML que descrevem as animações de propriedade.
   As animações de propriedade foram introduzidas no nível da API 11 (Android 3.0) e fornece para a animação de propriedades em um objeto. As animações de propriedade são uma maneira mais flexível e poderosa para descrever as animações em qualquer tipo de objeto.

-  **anim** &ndash; arquivos XML que descrevem *interpolação* animações. Animações de interpolação são uma série de instruções de animação para executar transformações no conteúdo de uma rotação de objeto ou de exemplo, de modo de exibição de uma imagem ou aumentando o tamanho do texto. Animações de interpolação são limitadas para exibir apenas os objetos.

-  **cor** &ndash; arquivos XML que descrevem uma lista de estado de cores. Para compreender as listas de estado de cores, considere um widget de interface do usuário, como um botão.
   Ele pode tornar tiverem estados diferentes, como pressionado ou desabilitado, e o botão pode alterar a cor com cada alteração no estado. A lista é expressa em uma lista de estado.

-  **fonte** &ndash; a partir de 26 de nível de API, é possível incorporar fontes como um recurso em um aplicativo Android. 26 de biblioteca de suporte serão backport fontes para o nível da API 14. A incorporação de fontes permite que aplicativos carregar fontes personalizadas diretamente de layouts XML sem a necessidade de importá-los como ativos antes do uso.

-  **Mipmap** &ndash; recursos Desenháveis são um conceito geral de elementos gráficos que podem ser compilados no aplicativo e, em seguida, acessados por chamadas à API ou referenciados por outros recursos XML.
   Alguns exemplos de desenháveis são arquivos de bitmap (. png,. gif,. jpg), em especial bitmaps redimensionável, conhecido como [nove Patches](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), listas de estado, formas genéricas definidas no XML, etc.

-  **layout** &ndash; arquivos XML que descrevem um layout de interface do usuário, como uma atividade ou uma linha em uma lista.

-  **menu** &ndash; arquivos XML que descrevem menus do aplicativo, como *opções Menus*, *Menus de contexto*, e *submenus*. Para obter um exemplo dos menus, consulte o [demonstração do Menu pop-up](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) ou o [controles padrão](https://developer.xamarin.com/samples/mobile/StandardControls/) exemplo.

-  **brutos** &ndash; arquivos arbitrários que são salvos em sua forma bruta e binária. Esses arquivos são compilados em um aplicativo do Android em um formato binário.

-  **valores** &ndash; arquivos XML que contêm valores simples. Um arquivo XML no diretório de valores não define um único recurso, mas em vez disso, pode definir vários recursos. Por exemplo um arquivo XML pode conter uma lista de valores de cadeia de caracteres, enquanto outro arquivo XML pode conter uma lista de valores de cor.

-  **XML** &ndash; arquivos XML que têm função semelhantes para os arquivos de configuração do .NET. Esses são XML arbitrário que pode ser lido pelo aplicativo em tempo de execução

-----
