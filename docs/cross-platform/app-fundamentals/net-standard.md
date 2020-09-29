---
title: Usar bibliotecas de .NET Standard para compartilhar código
description: Este documento descreve como usar .NET Standard bibliotecas para compartilhar código. Ele discute como criar uma biblioteca de .NET Standard, editar suas configurações e usá-la em um aplicativo.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: 6c2f0ef1d34b60e712dd713d4fff3fe5f9856926
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458025"
---
# <a name="net-standard-library-code-sharing"></a>Compartilhamento de código de biblioteca .NET Standard

As bibliotecas de .NET Standard têm uma API uniforme para todas as plataformas .NET, incluindo o Xamarin e o .NET Core. Crie uma única biblioteca de .NET Standard e use-a de qualquer tempo de execução que ofereça suporte à plataforma .NET Standard. Consulte [este gráfico](/dotnet/standard/net-standard#net-implementation-support) para obter detalhes das plataformas com suporte.

Embora .NET Standard versões 1,0 a 1,6 forneçam subconjuntos incrementalmente maiores do .NET Framework, .NET Standard 2,0 fornece o melhor nível de suporte para aplicativos Xamarin e para portar bibliotecas de classes portáteis existentes.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

Esta seção explica como criar e usar uma biblioteca de .NET Standard usando Visual Studio para Mac.

### <a name="creating-a-net-standard-library"></a>Criando uma biblioteca de .NET Standard

Você pode adicionar uma biblioteca de .NET Standard à sua solução com estas etapas:

1. Na caixa de diálogo **Adicionar novo projeto** , selecione a categoria **.NET Core** e, em seguida, selecione **.net Standard biblioteca**:

    ![Criar uma biblioteca de .NET Standard](net-standard-images/vsm01-m157.png "Criando uma nova biblioteca de .NET Standard")

2. Na próxima tela, escolha o Framework de destino- **.NET Standard 2,0** é recomendado:

    [![Escolha .NET Standard 2,0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. Na tela final, digite o nome do projeto e clique em **criar**.

4. O projeto de biblioteca .NET Standard aparecerá conforme mostrado na Gerenciador de Soluções. O nó de dependências indicará que a biblioteca usa o [netstandard. library](https://www.nuget.org/packages/NETStandard.Library/).

    ![O nó de dependências na solução indica .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Editando .NET Standard configurações da biblioteca

As configurações da biblioteca .NET Standard podem ser exibidas e alteradas clicando com o botão direito do mouse no projeto e selecionando `Options` conforme mostrado nesta captura de tela:

![Editar .NET Standard estrutura de destino em opções de projeto](net-standard-images/vsm03-m157.png "Editar a versão do .NET Standard Framework de destino em opções de projeto")

Dentro de você, você pode alterar sua versão do `netstandard` alterando o `Target Framework` valor da lista suspensa.

**Além disso:** Você pode editar o `.csproj` diretamente para alterar esse valor.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Esta seção explica como criar e usar uma biblioteca de .NET Standard usando o Visual Studio.

### <a name="creating-a-net-standard-library"></a>Como criar uma biblioteca .NET Standard

Adicionar uma biblioteca de .NET Standard à sua solução é bastante direto.

1. Na caixa de diálogo **novo projeto** , selecione a categoria **.net Standard** e, em seguida, selecione **biblioteca de classes (.net Standard)**.

    ![Criando uma nova biblioteca de classes de .NET Standard](net-standard-images/vs01-w157.png "Criar nova biblioteca de classes de .NET Standard")

2. O projeto de biblioteca .NET Standard aparecerá conforme mostrado na Gerenciador de Soluções. O nó de dependências indicará que a biblioteca usa o [netstandard. library](https://www.nuget.org/packages/NETStandard.Library/).

    ![Netstandard. library na pasta do projeto](net-standard-images/vs02-w157.png ".NET Standard projeto na solução")

### <a name="editing-net-standard-library-settings"></a>Editando .NET Standard configurações da biblioteca

As configurações da biblioteca .NET Standard podem ser exibidas e alteradas clicando-se com o botão direito do mouse no projeto e selecionando **Propriedades** , conforme mostrado nesta captura de tela:

![Editar estruturas de destino do .NET Standard nas propriedades do projeto](net-standard-images/vs03-w157.png "Referencie uma biblioteca de .NET Standard da mesma maneira que outros projetos")

**Além disso:** Você pode editar o `.csproj` diretamente para editar o `TargetFramework` elemento e alterar qual versão é direcionada (por exemplo, `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Usando um projeto de biblioteca .NET Standard

Depois que uma biblioteca de .NET Standard tiver sido criada, você poderá adicionar uma referência a ela de qualquer aplicativo compatível ou projeto de biblioteca da mesma maneira que normalmente adiciona referências. No Visual Studio, clique com o botão direito do mouse no nó referências e escolha **Adicionar referência...** em seguida, alterne para a guia **projetos > solução** , conforme mostrado:

![Referenciando uma biblioteca de .NET Standard](net-standard-images/vs04.png "No Visual Studio, clique com o botão direito do mouse no nó referências e escolha Adicionar referência... em seguida, alterne para a guia projetos de solução conforme mostrado")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET Standard e Xamarin. Forms para o desenvolvedor do .NET (vídeo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Links Relacionados

* [.Net Standard](/dotnet/standard/net-standard) -informações detalhadas e comparação com o PCL.