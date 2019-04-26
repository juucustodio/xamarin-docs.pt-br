---
title: Usar bibliotecas padrão do .NET para compartilhar código
description: Este documento descreve como usar bibliotecas padrão do .NET para compartilhar código. Ele aborda a criação de uma biblioteca .NET Standard, edição de suas configurações e usá-lo em um aplicativo.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: d07b248b36feee909db9c863eb17f1a900f58e60
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61191264"
---
# <a name="net-standard-library-code-sharing"></a>O compartilhamento de código do .NET standard Library

Bibliotecas do .NET standard tem uma API uniforme para todas as plataformas do .NET, incluindo o Xamarin e .NET Core. Criar uma única biblioteca .NET Standard e usá-lo em qualquer tempo de execução que suporta a plataforma do .NET Standard. Consulte a [este gráfico](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support) para obter detalhes de plataformas com suporte.

Enquanto as versões do .NET Standard 1.0 a 1.6 fornecem maiores incrementalmente subconjuntos do .NET Framework, o .NET Standard 2.0 fornece o melhor nível de suporte para aplicativos Xamarin e para portabilidade de bibliotecas de classes portátil existente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

Esta seção explica como criar e usar uma biblioteca .NET Standard usando o Visual Studio para Mac.

### <a name="creating-a-net-standard-library"></a>Criando uma biblioteca .NET Standard

Você pode adicionar uma biblioteca .NET Standard à sua solução com estas etapas:

1. No **adicionar novo projeto** caixa de diálogo, selecione o **.NET Core** categoria e selecione **.NET Standard Library**:

    ![Criar uma biblioteca .NET Standard](net-standard-images/vsm01-m157.png "criando um novo .NET Standard library")

2. Na próxima tela, escolha a estrutura de destino - **.NET Standard 2.0** é recomendável:

    [![Choose .NET Standard 2.0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. Na tela final, digite o nome do projeto e clique em **criar**.

4. O projeto de biblioteca do .NET Standard será exibida conforme mostrado no Gerenciador de soluções. O nó dependências indicará que a biblioteca usa o [Netstandard](https://www.nuget.org/packages/NETStandard.Library/).

    ![Nó de dependências na solução indica .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Editar configurações de biblioteca .NET Standard

As configurações de biblioteca .NET Standard podem ser exibidas e alteradas clicando com botão direito no projeto e selecionando `Options` conforme mostrado nesta captura de tela:

![Editar padrão do .NET framework de destino nas opções do projeto](net-standard-images/vsm03-m157.png "editar a versão do .NET Standard Framework de destino nas opções do projeto")

Em que você pode alterar sua versão do `netstandard` alterando o `Target Framework` valor da lista suspensa.

**Além disso:** Você pode editar o `.csproj` diretamente para alterar esse valor.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Esta seção explica como criar e usar uma biblioteca .NET Standard usando o Visual Studio.

### <a name="creating-a-net-standard-library"></a>Criando uma biblioteca .NET Standard

Adicionar uma biblioteca .NET Standard à sua solução é bastante simples.

1. No **novo projeto** caixa de diálogo, selecione o **.NET Standard** categoria e selecione **biblioteca de classes (.NET Standard)**.

    ![Criar uma nova biblioteca classes .NET Standard](net-standard-images/vs01-w157.png "nova biblioteca de classes .NET Standard criar")

2. O projeto de biblioteca do .NET Standard será exibida conforme mostrado no Gerenciador de soluções. O nó dependências indicará que a biblioteca usa o [Netstandard](https://www.nuget.org/packages/NETStandard.Library/).

    ![Netstandard. Library na pasta do projeto](net-standard-images/vs02-w157.png "projeto .NET Standard na solução")

### <a name="editing-net-standard-library-settings"></a>Editar configurações de biblioteca .NET Standard

As configurações de biblioteca .NET Standard podem ser exibidas e alteradas clicando com botão direito no projeto e selecionando **propriedades** conforme mostrado nesta captura de tela:

![Editar .NET frameworks de destino padrão nas propriedades do projeto](net-standard-images/vs03-w157.png "fazer referência a uma biblioteca .NET Standard da mesma maneira que outros projetos")

**Além disso:** Você pode editar a `.csproj` diretamente para editar o `TargetFramework` elemento e alteração de qual versão é o destino (por exemplo. `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>Usando um projeto de biblioteca do .NET Standard

Quando uma biblioteca .NET Standard tiver sido criada, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio, clique com botão direito no nó referências e escolha **adicionar referência...**  , em seguida, alterne para o **projetos > solução** guia conforme mostrado:

![Fazendo referência a uma biblioteca .NET Standard](net-standard-images/vs04.png "no Visual Studio, clique com botão direito no nó referências e escolha Adicionar referência... e alterne para a guia de projetos da solução, conforme mostrado")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>.NET standard e xamarin. Forms para o desenvolvedor de .NET (vídeo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>Links relacionados

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -informações detalhadas e comparação de PCL.
