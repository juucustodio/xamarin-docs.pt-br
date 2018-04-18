---
redirect_url: /xamarin/tools/live-player/
title: XAML Live visualização
description: Testar as alterações de código do aplicativo em tempo real em seu dispositivo iOS ou Android
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 12/21/2017
ms.openlocfilehash: c96777666d7601412c1d2e3be1c7fe23131fc9d4
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="xaml-live-previewing"></a>XAML Live visualização

Um dos benefícios do Xamarin Live Player é a capacidade de visualizar XAML páginas, fazer alterações no código no Visual Studio, e ver as alterações imediatamente em seu dispositivo. A visualização dinâmica pode ser feita em seu dispositivo iOS ou Android ou em um simulador ou emulador. Este guia demonstra como usar o recurso de visualização dinâmica para exibir telas XAML individuais.

## <a name="requirements"></a>Requisitos

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Um computador executando o Windows 7 ou superior.
2. Visual Studio 2017 versão 15.4 ou superior com o **desenvolvimento móvel com o .NET** instalada da carga de trabalho.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Um Mac com o OS X 10.11, macOS 10.12 ou superior.
2. O Visual Studio para Mac 7.2 ou posterior. Recomendamos que a versão mais recente.

-----



<a name="deploydevice" />

## <a name="deploying-to-device"></a>Implantação de dispositivo

Antes de usar o Player de Live Xamarin com seu dispositivo iOS ou Android, você precisará baixar o aplicativo Xamarin ao vivo Player e emparelhá-lo para o Visual Studio, conforme descrito no [instalar](~/tools/live-player/install.md) guia. Quando você tiver vinculado com êxito seu dispositivo para o Visual Studio, você pode começar a visualização ao vivo de sua página XAML. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra a página XAML que você deseja visualizar ao vivo no editor do Visual Studio de 2017:

    ![](live-view-images/vs-image1.png)

2. Defina a configuração de dispositivo como **depurar | iPhone** para iOS ou **depurar** para Android e selecione o dispositivo de Player ao vivo na lista:

    ![](live-view-images/vs-image2.png)

3. Para executar esta página XAML como uma exibição em tempo real em seu dispositivo, selecione **Ferramentas > Xamarin Live Player > Live executar exibição atual** na barra de menus:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a página XAML que você deseja visualizar em tempo real no Visual Studio para editor de Mac:

    ![](live-view-images/image1.png)

2. Defina a configuração de dispositivo como **depurar | iPhone** para iOS ou **depurar** para Android e selecione o dispositivo de Player ao vivo na lista:

    ![](live-view-images/image2.png)

3. Para executar esta página XAML como uma exibição em tempo real em seu dispositivo, selecione **Executar > Live executar exibição atual** na barra de menus:

    ![](live-view-images/image3.png)

-----








## <a name="deploying-to-android-emulator"></a>Implantação de emulador do Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra a página XAML que você deseja visualizar ao vivo no editor do Visual Studio de 2017:

    ![](live-view-images/vs-image1.png)

2. Defina a configuração de dispositivo como **depurar** para Android e selecione o dispositivo de Player ao vivo na lista:

    ![](live-view-images/vs-image4.png)

3. Para executar esta página XAML como uma exibição em tempo real no emulador do Android, selecione **Ferramentas > Xamarin Live Player > Live executar exibição atual** na barra de menus:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra a página XAML que você deseja visualizar em tempo real no Visual Studio para editor de Mac:

    ![](live-view-images/image7.png)

2. Defina a configuração de dispositivo como **depurar** para Android e selecione o dispositivo de Player ao vivo na lista:

    ![](live-view-images/image6.png)

3. Para executar esta página XAML como uma exibição em tempo real em seu dispositivo, selecione Executar > Live executar exibição atual na barra de menus:

    ![](live-view-images/image3.png)

-----





## <a name="deploying-to-ios-simulator"></a>Implantar em iOS simulador

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Atualmente, não há nenhum suporte para usar a visualização de XAML ao vivo no simulador iOS remota no Windows. Em vez disso, você deve [implantar em um dispositivo](#deploydevice).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra a página XAML que você deseja visualizar em tempo real no Visual Studio para editor de Mac:

    ![](live-view-images/image1.png)

2. Defina a configuração de dispositivo como **depurar | iPhoneSimulator** para iOS e selecione um simulador de iOS da lista:

    ![](live-view-images/image2.png)

3. Selecione **Executar > Live executar exibição atual** na barra de menus para iniciar o simulador e exibir a página XAML:

    ![](live-view-images/image4.png)

4. Quando o simulador foi iniciado, você pode começar a editar o XAML e visualizar aparecem ao vivo:

    ![](live-view-images/image5.png)  

-----








## <a name="related-links"></a>Links relacionados

- [Visão geral do Xamarin Player ao vivo](https://xamarin.com/live)
- [postagem do blog](https://blog.xamarin.com/live-player/)
- [Exemplos do Xamarin Player ao vivo](~/tools/live-player/samples.md)
