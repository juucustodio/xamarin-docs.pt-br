---
title: Configurando o Visual Studio 2017
description: Este artigo descreve várias opções de configuração do Xamarin.iOS para o Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/16/2018
ms.openlocfilehash: ee08cf7d68bd9d10026f1c15d4438077349fe367
ms.sourcegitcommit: dc6ccf87223942088ca926c0dadd5b5478c683cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-visual-studio-2017"></a>Configurando o Visual Studio 2017

_Este artigo descreve várias opções de configuração do Xamarin.iOS para o Visual Studio._

## <a name="using-matching-xamarinios-versions"></a>Usando versões correspondentes do Xamarin.iOS

O Visual Studio 2017 deve usar a mesma versão do Xamarin.iOS instalada no host de build do Mac. Para garantir que isso é verdadeiro:

 - Se usar o Visual Studio 2017, selecione o canal de atualizações **Estável** no Visual Studio para Mac.

 - Se usar o Visual Studio 2017 Preview, selecione o canal de atualizações **Alfa** no Visual Studio para Mac.

> [!NOTE]
> A partir do [Visual Studio 2017 versão 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), o Visual Studio 2017 detecta automaticamente se o host de build do Mac está usando a mesma versão do Xamarin.iOS que no Windows. Se houver uma incompatibilidade de versão, o Visual Studio 2017 oferecerá a instalação remota da versão correta no host de build do Mac. Para obter mais informações, veja a seção [Provisionamento automático do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) na guia [Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="ios-toolbar"></a>Barra de ferramentas do iOS

Quando um projeto do iOS é aberto no Visual Studio 2017, a barra de ferramentas do iOS deve estar visível.  Por padrão, ela contém quatro botões que são úteis para o desenvolvimento do Xamarin.iOS:

![Barra de ferramentas do iOS do Visual Studio 2017](config-options-images/ios-toolbar.png "Barra de ferramentas do iOS do Visual Studio 2017")

- **Emparelhar com Mac** – a caixa de diálogo Emparelhar com Mac abre. Habilitado quando um projeto do iOS é aberto no Visual Studio 2017.
- **Mostrar simulador de iOS** – traz a janela do Simulador de iOS para a frente no host de build do Mac. Habilitado quando um projeto do iOS é aberto no Visual Studio 2017.
- **Log do dispositivo** – exibe uma janela que permite inspecionar os logs do dispositivo. Habilitado quando um projeto do iOS é aberto no Visual Studio 2017.
- **Mostrar arquivo de IPA no servidor de build** – abre uma janela no host de build do Mac, mostrando o local do arquivo .ipa para o aplicativo. Habilitado após a conclusão de um build para o qual um .ipa foi criado.

Se essa barra de ferramentas não aparecer, abra o menu **Modo de Exibição** no Visual Studio 2017 e escolha **Barras de Ferramentas > iOS**:

![Habilitando a barra de ferramentas do iOS](config-options-images/ios-toolbar-enable.png "Habilitando a barra de ferramentas do iOS")

## <a name="solution-platforms-drop-down-menu"></a>Menu suspenso Plataformas de Solução

O menu suspenso **Plataformas de Solução** permite que você escolha se o próximo build será direcionado a um dispositivo físico ou um simulador.

Para verificar se este menu suspenso está visível na barra de ferramentas Padrão:

- No Visual Studio 2017, clique na seta para baixo na borda direita da barra de ferramentas Padrão.
- Escolha **Adicionar ou Remover Botões** 
- Verifique se o item **Plataformas de Solução** está selecionado:

![Habilitando o menu suspenso Plataformas de Solução](config-options-images/solution-platforms-enable.png "Habilitando o menu suspenso Plataformas de Solução")

Com um projeto do iOS aberto, as barras de ferramentas **Padrão** e **iOS** agora devem ser semelhantes à captura de tela a seguir:

![Barras de ferramentas Padrão e iOS](config-options-images/toolbars.png "Barras de ferramentas Padrão e iOS")


