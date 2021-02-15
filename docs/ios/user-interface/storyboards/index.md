---
title: Criando interfaces do usuário com o Xcode
description: Saiba mais sobre a maneira recomendada de criar interfaces de usuário do iOS diretamente usando o Xcode em um Mac.
ms.prod: xamarin
ms.assetid: af9f95db-5cd6-475d-867d-f73e1574e8fc
ms.technology: xamarin-ios
author: joshspicer
ms.author: jospicer
ms.date: 10/27/2020
ms.openlocfilehash: b25245250bd9ea17034ea8f6b11388a12fc13241
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439567"
---
# <a name="designing-user-interfaces-with-xcode"></a>Criando interfaces do usuário com o Xcode

A partir do Visual Studio 2019 versão 16,8 e Visual Studio para Mac versão 8,8, a maneira recomendada para editar arquivos. Storyboard e. NIB é editando-os no Xcode Interface Builder em um Mac.

> [!NOTE]
> A partir do Visual Studio 2019 versão 16,9, não haverá nenhuma maneira com suporte para editar storyboards do iOS no Windows. Use Visual Studio para Mac e o Xcode Interface Builder para continuar criando suas interfaces de usuário do Xamarin. iOS.

Este artigo aborda soluções comuns para a criação de interfaces do usuário com o Interface Builder do Xcode.  Este artigo pode ser particularmente útil se você editou anteriormente suas interfaces do projeto com o Xamarin. iOS designer. 

Para obter uma explicação mais detalhada dos storyboards, consulte [storyboards in Xamarin. Ios](./indepth-storyboard.md).

## <a name="how-to-open-a-storyboard"></a>Como abrir um storyboard 

Abra um arquivo de interface do usuário do iOS no Visual Studio para Mac clicando com o botão direito do mouse em um arquivo de storyboard e selecionando o **Xcode Interface Builder**:

[![Selecionar Interface Builder](images/select-interface-builder.png)](images/select-interface-builder.png#lightbox)

Em seguida, você verá a janela do Xcode aberta. Todas as edições salvas aqui serão refletidas no seu projeto do Visual Studio.

[![Janela do Xcode](images/xcode.png)](images/xcode.png#lightbox)

Para obter mais informações sobre o Interface Builder do Xcode, consulte [interface Builder interno](https://developer.apple.com/xcode/interface-builder/).

## <a name="creating-a-new-control"></a>Criando um novo controle

Para criar um novo controle com o Interface Builder do Xcode, primeiro selecione o storyboard que você deseja editar. Em seguida, abra a caixa de diálogo biblioteca do Xcode (**Exibir**  >  **Mostrar biblioteca**) e arraste o controle para o storyboard.

[![Seletor de biblioteca](images/library-picker.png)](images/library-picker.png#lightbox)

Em seguida, abra o arquivo de cabeçalho do controlador de exibição correspondente.  Para um aplicativo Xamarin. iOS em branco "modo de exibição único", o storyboard padrão é chamado **Main. Storyboard**. O arquivo do controlador de exibição correspondente é chamado de **ViewController.cs** no Visual Studio com um arquivo de cabeçalho **ViewController. h** correspondente quando visualizado no Xcode.

No Interface Builder do Xcode, abra o storyboard e o arquivo de cabeçalho do controlador de exibição correspondente.  Segurando a chave de **controle** ( **^** ), arraste o controle do storyboard para o arquivo do controlador de exibição até que o Xcode solicite uma caixa de diálogo.

[![Controle de link de demonstração](images/demo-link-control.gif)](images/demo-link-control.gif#lightbox)

Conforme ilustrado acima, o código C# correspondente será gerado automaticamente no arquivo code-behind do controlador de exibição.  Agora você pode acessar esse controle em seu projeto do Xamarin. iOS.

## <a name="editing-an-existing-controls-name"></a>Editando um nome de controle existente

Para editar o nome de um controle existente do Xcode Interface Builder e refletir essa alteração de volta em seu projeto C#, navegue até o arquivo de cabeçalho do controlador de exibição apropriado, clique em e selecione **Refactor**.   

[![Controle de refatoração](images/refactor-control.png)](images/refactor-control.png#lightbox)

Seu arquivo code-behind será regenerado com o novo nome, permitindo que você acesse o controle por meio de código em Visual Studio para Mac.

## <a name="known-problems"></a>Problemas conhecidos

Esta seção aborda problemas conhecidos.

### <a name="visual-studio-could-not-communicate-with-xcode"></a>"O Visual Studio não pôde se comunicar com o Xcode"

No macOS Catalina ou superior, você pode encontrar o erro abaixo:

[![Não consegue comunicar erro](images/could-not-communicate.png)](images/could-not-communicate.png#lightbox)

Primeiro, nas preferências do sistema do Mac em **segurança & privacidade > automação**, verifique se o Visual Studio está listado e se o **Xcode** está marcado.

[![segurança do macOS](images/macos-security.png)](images/macos-security.png#lightbox)

Se o **Xcode** estiver marcado e a mensagem de erro ainda aparecer, talvez seja necessário redefinir as permissões de privacidade do Visual Studio para Mac.

Isso pode ser feito iniciando uma janela de terminal e emitindo o seguinte comando:

```bash
sudo tccutil reset All "com.microsoft.visual-studio"
```

Para garantir que a alteração acima entra em vigor, redefina o PRAM de seu Mac. Para obter instruções, consulte [Redefinir NVRAM ou Pram em seu Mac](https://support.apple.com/HT204063).
