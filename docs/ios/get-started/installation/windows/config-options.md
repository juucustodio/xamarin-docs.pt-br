---
title: Configurando o Visual Studio para desenvolvimento do iOS
description: Este artigo descreve como configurar Visual Studio 2019 para desenvolvimento do Xamarin.iOS. Especificamente, ele aborda como configurar a versão instalada do Xamarin.iOS, a barra de ferramentas do iOS e o menu suspenso Plataformas da solução.
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 0f57c10ea263a3dcf882d2cf75a57105e7f7d0d4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434527"
---
# <a name="configuring-visual-studio-for-ios-development"></a>Configurando o Visual Studio para desenvolvimento do iOS

_Este artigo descreve várias opções de configuração do Xamarin.iOS para o Visual Studio._

## <a name="using-matching-xamarinios-versions"></a>Usando versões correspondentes do Xamarin.iOS

O Visual Studio 2019 ou o Visual Studio 2017 deve usar a mesma versão do Xamarin.iOS instalada no host de build do Mac. Para garantir que isso é verdadeiro:

- Se for usar o Visual Studio 2019 ou o Visual Studio 2017, selecione o canal de atualizações **Estável** no Visual Studio para Mac.

- Se for usar o Visual Studio 2019 Preview, selecione o canal de atualizações **Alfa** no Visual Studio para Mac.

> [!NOTE]
> A partir do [Visual Studio 2017 versão 15.6](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), o Visual Studio 2017 detecta automaticamente se o host de build do Mac está usando a mesma versão do Xamarin.iOS que no Windows. Se houver uma incompatibilidade de versão, o Visual Studio 2017 oferecerá a instalação remota da versão correta no host de build do Mac. Para obter mais informações, veja a seção [Provisionamento automático do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning) na guia [Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="ios-toolbar"></a>Barra de ferramentas do iOS

Quando um projeto do iOS é aberto no Visual Studio 2019 ou no Visual Studio 2017, a barra de ferramentas do iOS deve estar visível.  Por padrão, ela contém quatro botões que são úteis para o desenvolvimento do Xamarin.iOS:

![Barra de ferramentas do iOS do Visual Studio 2019](config-options-images/ios-toolbar.png)

- **Emparelhar com Mac** – a caixa de diálogo Emparelhar com Mac abre. Habilitada quando um projeto do iOS é aberto no Visual Studio 2019 ou no Visual Studio 2017.
- **Mostrar simulador de iOS** – traz a janela do Simulador de iOS para a frente no host de build do Mac. Habilitada quando um projeto do iOS é aberto no Visual Studio 2019 ou no Visual Studio 2017.
- **Log do dispositivo** – exibe uma janela que permite inspecionar os logs do dispositivo. Habilitada quando um projeto do iOS é aberto no Visual Studio 2019 ou no Visual Studio 2017.
- **Mostrar arquivo de IPA no servidor de build** – abre uma janela no host de build do Mac, mostrando o local do arquivo .ipa para o aplicativo. Habilitado após a conclusão de um build para o qual um .ipa foi criado.

Se essa barra de ferramentas não aparecer, abra o menu **Modo de Exibição** no Visual Studio 2019 ou no Visual Studio 2017 e escolha **Barras de Ferramentas > iOS**:

![Habilitando a barra de ferramentas do iOS](config-options-images/ios-toolbar-enable.png "Habilitando a barra de ferramentas do iOS")

## <a name="solution-platforms-drop-down-menu"></a>Menu suspenso Plataformas de Solução

O menu suspenso **Plataformas de Solução** permite que você escolha se o próximo build será direcionado a um dispositivo físico ou um simulador.

Para verificar se este menu suspenso está visível na barra de ferramentas Padrão:

- No Visual Studio 2019 ou no Visual Studio 2017, clique na seta para baixo na borda direita da barra de ferramentas Padrão.
- Escolher **Adicionar ou remover botões** 
- Verifique se o item **Plataformas de Solução** está selecionado:

![Habilitando o menu suspenso plataformas de soluções](config-options-images/solution-platforms-enable.png "Habilitando o menu suspenso plataformas de soluções")

Com um projeto do iOS aberto, as barras de ferramentas **Padrão** e **iOS** agora devem ser semelhantes à captura de tela a seguir:

![Barras de ferramentas standard e iOS](config-options-images/toolbars.png "Barras de ferramentas standard e iOS")