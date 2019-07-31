---
title: Recursos padrão
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 3be63eb8909f129f046b2959ec7963e493674914
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644220"
---
# <a name="default-resources"></a>Recursos padrão

Os recursos padrão são itens que não são específicos de nenhum dispositivo ou fator de forma específico e, portanto, são a escolha padrão pelo sistema operacional Android se não for possível encontrar recursos mais específicos. Dessa forma, eles são o tipo mais comum de recurso a ser criado. Eles são organizados em subdiretórios do diretório de **recursos** de acordo com seu tipo de recurso:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Arquivos de recurso padrão](default-resources-images/01-resource-files-vs.png)

Na imagem acima, o projeto tem valores padrão para recursos, layouts e valores desenhados (arquivos XML que contêm valores simples).

Uma lista completa de tipos de recursos é fornecida abaixo:

-  **Animator** &ndash; Arquivos XML que descrevem animações de propriedade.
   Animações de propriedade foram introduzidas no nível de API 11 (Android 3,0) e fornecem a animação de propriedades em um objeto. As animações de propriedade são uma maneira mais flexível e eficiente de descrever animações em qualquer tipo de objeto.

-  **anim** Arquivos XML que descrevem animações de interpolação. &ndash; Animações de interpolação são uma série de instruções de animação para executar transformações no conteúdo de um objeto de exibição, ou por exemplo, girar uma imagem ou aumentar o tamanho do texto. Animações de interpolação são limitadas apenas a objetos de exibição.

-  **cor** do &ndash; Arquivos XML que descrevem uma lista de estado de cores. Para entender as listas de estado de cor, considere um widget de interface do usuário, como um botão.
   Ele pode ter Estados diferentes, como pressionado ou desabilitado, e o botão pode mudar de cor com cada alteração no estado. A lista é expressa em uma lista de estado.

-  com **empate** &ndash; Os recursos desenháveis são um conceito geral para elementos gráficos que podem ser compilados no aplicativo e acessados por chamadas à API ou referenciados por outros recursos XML.
   Alguns exemplos de drawables são arquivos de bitmap (. png,. gif,. jpg), bitmaps de redimensionamento especiais conhecidos como [nove-patches](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), listas de estado, formas genéricas definidas em XML, etc.
 
-  **layout** do &ndash; Arquivos XML que descrevem um layout de interface do usuário, como uma atividade ou uma linha em uma lista.

-  **menu** Arquivos XML que descrevem menus de aplicativos, como *menus de opções*, menus de *contexto*e submenus. &ndash; Para obter um exemplo de menus, consulte a [demonstração do menu pop-up](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo) ou o exemplo de [controles padrão](https://developer.xamarin.com/samples/mobile/StandardControls/) .

-  **bruto** &ndash; Arquivos arbitrários que são salvos em seu formato binário bruto. Esses arquivos são compilados em um aplicativo Android em formato binário.

-  **valores** de &ndash; Arquivos XML que contêm valores simples. Um arquivo XML no diretório de valores não define um recurso único, mas pode definir vários recursos. Por exemplo, um arquivo XML pode conter uma lista de valores de cadeia de caracteres, enquanto outro arquivo XML pode conter uma lista de valores de cor.

-  arquivos XML XML que são semelhantes em função aos arquivos de configuração do .net. &ndash; Esses são XML arbitrários que podem ser lidos em tempo de execução pelo aplicativo.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Arquivos de recurso padrão](default-resources-images/01-resource-files-xs.png)

Na imagem acima, o projeto tem valores padrão para recursos, layouts e valores desenhados (arquivos XML que contêm valores simples).

Uma lista completa de tipos de recursos é fornecida abaixo:

-  **Animator** &ndash; Arquivos XML que descrevem animações de propriedade.
   Animações de propriedade foram introduzidas no nível de API 11 (Android 3,0) e fornecem a animação de propriedades em um objeto. As animações de propriedade são uma maneira mais flexível e eficiente de descrever animações em qualquer tipo de objeto.

-  **anim** Arquivos XML que descrevem animações de interpolação. &ndash; Animações de interpolação são uma série de instruções de animação para executar transformações no conteúdo de um objeto de exibição, ou por exemplo, girar uma imagem ou aumentar o tamanho do texto. Animações de interpolação são limitadas apenas a objetos de exibição.

-  **cor** do &ndash; Arquivos XML que descrevem uma lista de estado de cores. Para entender as listas de estado de cor, considere um widget de interface do usuário, como um botão.
   Ele pode fazer com Estados diferentes, como pressionado ou desabilitado, e o botão pode mudar de cor com cada alteração no estado. A lista é expressa em uma lista de estado.

-  **fonte** &ndash; A partir do nível de API 26, é possível inserir fontes como um recurso em um aplicativo Android. A biblioteca de suporte 26 irá backport as fontes para a API nível 14. A incorporação de fontes permite que os aplicativos carreguem fontes personalizadas diretamente de layouts XML sem a necessidade de importá-las como ativos antes do uso.

-  **mipmap** &ndash; Os recursos desenháveis são um conceito geral para elementos gráficos que podem ser compilados no aplicativo e acessados por chamadas à API ou referenciados por outros recursos XML.
   Alguns exemplos de drawables são arquivos de bitmap (. png,. gif,. jpg), bitmaps de redimensionamento especiais conhecidos como [nove-patches](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), listas de estado, formas genéricas definidas em XML, etc.

-  **layout** do &ndash; Arquivos XML que descrevem um layout de interface do usuário, como uma atividade ou uma linha em uma lista.

-  **menu** Arquivos XML que descrevem menus de aplicativos, como *menus de opções*, menus de *contexto*e submenus. &ndash; Para obter um exemplo de menus, consulte a [demonstração do menu pop-up](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo) ou o exemplo de [controles padrão](https://developer.xamarin.com/samples/mobile/StandardControls/) .

-  **bruto** &ndash; Arquivos arbitrários que são salvos em seu formato binário bruto. Esses arquivos são compilados em um aplicativo Android em formato binário.

-  **valores** de &ndash; Arquivos XML que contêm valores simples. Um arquivo XML no diretório de valores não define um recurso único, mas pode definir vários recursos. Por exemplo, um arquivo XML pode conter uma lista de valores de cadeia de caracteres, enquanto outro arquivo XML pode conter uma lista de valores de cor.

-  arquivos XML XML que são semelhantes em função aos arquivos de configuração do .net. &ndash; Esses são XML arbitrários que podem ser lidos em tempo de execução pelo aplicativo

-----
