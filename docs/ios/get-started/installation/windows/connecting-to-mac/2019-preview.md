---
title: Conectando versões prévias do Visual Studio 2019 no Windows e no macOS
description: Este guia fornece instruções de como usar a versão prévia do Visual Studio 2019 no Windows para criar aplicativos iOS, usando a versão prévia do Visual Studio 2019 para Mac no macOS para hospedar os builds.
ms.prod: xamarin
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/04/2018
ms.openlocfilehash: 1eeb79737bd712da64ce34a6b4fe83ce2823e3eb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899359"
---
# <a name="visual-studio-2019-preview-pairing"></a>Emparelhamento da versão prévia do Visual Studio 2019

![Visualizar](~/media/shared/preview.png)

A [Versão prévia do Visual Studio para Mac 2019](https://docs.microsoft.com/visualstudio/mac/install-preview) é a primeira a ter a instalação lado a lado do Visual Studio que é totalmente compatível com o macOS. Isso traz uma nova questão para os desenvolvedores Windows que criam aplicativos iOS: quando o host de build do Mac tem tanto o Visual Studio 2017 para Mac (versão 7.x) quanto a versão prévia do Visual Studio 2019 para Mac (versão 8.0), qual deles está sendo executado como o host de build para a instalação do Windows?

_Por padrão_, a versão estável &ndash; Visual Studio 2017 for Mac (versão 7.7) &ndash; será usada pelo Visual Studio no Windows, independentemente do uso do Visual Studio 2017 ou da versão prévia do Visual Studio 2019 no Windows.

Para testar corretamente as versões prévias do Visual Studio 2019 no Windows e no macOS:

1. Instale e use a versão prévia do Visual Studio 2019 (versão 16.0) em seu computador Windows.
2. Instale a versão prévia do Visual Studio 2019 para Mac (versão 8.0) em seu Mac.
3. No **Visual Studio 2019 para Mac**:

    a. Escolha o item de menu **Visual Studio > Verificar se há atualizações**.

    b. Na janela **Atualização do Visual Studio**, selecione **Versão prévia do Xamarin** no canal de atualização. O seguinte aviso será exibido:

    > [!WARNING]
    > Experimente os builds mais recentes da versão prévia do Visual Studio 2019 para Mac com o tempo de execução Mono e os SDKs do Xamarin mais recentes. **A instalação de buids deste canal interromperá a versão Visual Studio 2017 para Mac.** Use este canal somente se você estiver criando aplicativos Xamarin em um sistema operacional do Windows usando as versões prévias do Visual Studio 2019.

    c. Pressione o botão **Trocar canal** para habilitar o host de build da versão prévia.

4. Siga as instruções para [Emparelhar com o Mac para desenvolvimento do Xamarin.iOS](index.md) e as [dicas de solução de problemas](troubleshooting.md) (se necessário).