---
redirect_url: /xamarin/tools/live-player/
title: XAML Live visualização
description: Este documento discute como usar o Xamarin Live Player para páginas XAML de visualização de live, fazer alterações para o XAML e ver as alterações aparecem instantaneamente no dispositivo.
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
author: topgenorth
ms.author: toopge
ms.date: 08/08/2018
ms.openlocfilehash: 200d19aa0a13d0557e52cb90021190978838ed39
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251266"
---
# <a name="xaml-live-previewing"></a>XAML Live visualização

Um dos benefícios do Xamarin Live Player é a capacidade de páginas XAML de visualização de live, faça as alterações no código no Visual Studio e ver as alterações aparecem instantaneamente no seu dispositivo. A visualização dinâmica pode ser feita em seu dispositivo Android ou em um simulador ou emulador. Este guia demonstra como usar o recurso de visualização dinâmica para exibir telas individuais de XAML.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Um computador executando o Windows 7 ou superior.
2. Visual Studio 2017 versão 15.4 ou posterior com o **desenvolvimento móvel com .NET** carga de trabalho instalada.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Um Mac com o OS X 10.11, macOS 10.12 ou superior.
2. Visual Studio para Mac 7.2 ou posterior. Recomendamos que a versão mais recente.

-----

<a name="deploydevice" />

## <a name="deploying-to-device"></a>Implantação no dispositivo

Antes de usar o Xamarin Live Player com seu dispositivo Android, você precisará baixar o aplicativo Xamarin Live Player e emparelhá-lo para o Visual Studio, conforme descrito na [instalar](~/tools/live-player/install.md) guia. Depois que você tiver vinculado com êxito seu dispositivo para o Visual Studio, você pode começar a visualização dinâmica da sua página XAML. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra a página XAML que você deseja a visualização dinâmica no editor do Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Defina a configuração de dispositivo como **depurar** e selecione o dispositivo do Live Player na lista:

    ![](live-view-images/vs-image2.png)

3. Para executar esta página XAML como uma exibição ao vivo em seu dispositivo, selecione **Ferramentas > Xamarin Live Player > Live exibição atual da execução** na barra de menus:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a página XAML que você deseja a visualização dinâmica no Visual Studio para o editor do Mac:

    ![](live-view-images/image1.png)

2. Defina a configuração de dispositivo como **depurar** e selecione o dispositivo do Live Player na lista:

    ![](live-view-images/image2.png)

3. Para executar esta página XAML como uma exibição ao vivo em seu dispositivo, selecione **Executar > Live exibição atual da execução** na barra de menus:

    ![](live-view-images/image3.png)

-----

## <a name="deploying-to-android-emulator"></a>Implantação no emulador do Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra a página XAML que você deseja a visualização dinâmica no editor do Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Defina a configuração de dispositivo como **depurar** para Android e selecione o dispositivo do Live Player na lista:

    ![](live-view-images/vs-image4.png)

3. Para executar esta página XAML como uma exibição ao vivo no emulador do Android, selecione **Ferramentas > Xamarin Live Player > Live exibição atual da execução** na barra de menus:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra a página XAML que você deseja a visualização dinâmica no Visual Studio para o editor do Mac:

    ![](live-view-images/image7.png)

2. Defina a configuração de dispositivo como **depurar** para Android e selecione o dispositivo do Live Player na lista:

    ![](live-view-images/image6.png)

3. Para executar esta página XAML como uma exibição ao vivo em seu dispositivo, selecione Executar > Live exibição atual da execução da barra de menus:

    ![](live-view-images/image3.png)

-----

## <a name="related-links"></a>Links relacionados

- [Visão geral do Xamarin Live Player](https://xamarin.com/live)
- [Postagem de blog](https://blog.xamarin.com/live-player/)
- [Exemplos do Xamarin Live Player](~/tools/live-player/samples.md)
