---
title: Usar bibliotecas padrão do .NET para compartilhar código
description: Este documento descreve como usar bibliotecas padrão do .NET para compartilhar código. Ele aborda a criação de uma biblioteca .NET padrão, editar suas configurações e usá-lo em um aplicativo.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: asb3993
ms.author: amburns
ms.date: 04/12/2017
ms.openlocfilehash: 82a89309e6462462471f42c3504d109ff0722917
ms.sourcegitcommit: 5db075bdd0b62d5d1d1567c267303a6a1888c8f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806784"
---
# <a name="using-net-standard-libraries-to-share-code"></a>Usar bibliotecas padrão do .NET para compartilhar código

## <a name="net-standard"></a>.NET Standard

O .NET Standard Library é uma especificação formal de APIs do .NET que devem estar disponíveis em todos os tempos de execução do .NET. A motivação por trás do Standard Library é estabelecer maior uniformidade no ecossistema do .NET.
[ECMA 335](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md) continua a estabelecer a uniformidade de comportamento de tempo de execução do .NET, mas não há especificação semelhante para a BCL (Biblioteca de Classes Base) para implementações da biblioteca do .NET.

Você pode pensar nele como simplificado, próxima geração de [biblioteca de classes portátil](https://msdn.microsoft.com/library/gg597391.aspx).
É uma única biblioteca com uma API uniforme para todas as plataformas do .NET, incluindo o .NET Core. Basta você cria uma única biblioteca padrão do .NET e usá-lo de qualquer tempo de execução que oferece suporte à plataforma padrão .NET.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

Esta seção explica como criar e usar uma biblioteca do .NET padrão usando o Visual Studio para Mac. Consulte a seção exemplo de biblioteca padrão do .NET para uma implementação completa.

### <a name="creating-a-net-standard-library"></a>Criar uma biblioteca do .NET padrão

Adicionar uma biblioteca do .NET padrão para sua solução é bastante direto.

1. Na caixa de diálogo Adicionar novo projeto, selecione o `.NET Core` categoria e selecione `Class Library(.NET Core)`.

  **Observação:** este modelo será renomeado para `.NET Standard` em uma versão futura do Visual Studio para Mac.

  ![Criar uma biblioteca de classes do .NET Core](net-standard-images/vsm01.png "criar uma nova biblioteca de classe do .NET Core")

2. O projeto de biblioteca padrão do .NET aparecerá como mostrado no Gerenciador de soluções. O nó dependências indicará que usa a biblioteca a [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

  ![Nó de dependências na solução indica .NET padrão](net-standard-images/vsm02.png)

#### <a name="editing-net-standard-library-settings"></a>Editar configurações de biblioteca padrão do .NET

As configurações de biblioteca padrão do .NET podem ser exibidas e alteradas clicando duas vezes no projeto e selecionando `Options` conforme mostrado nesta captura de tela:

![Editar padrão do .NET framework de destino nas opções de projeto](net-standard-images/vsm03.png "editar a versão do Framework de destino padrão do .NET em Opções de projeto")

Dentro, você pode alterar sua versão do `netstandard` alterando o `Target Framework` valor de lista suspensa.

**Além disso:** você pode editar o `.csproj` diretamente para alterar esse valor.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

Esta seção explica como criar e usar uma biblioteca do .NET padrão usando o Visual Studio. Consulte a seção exemplo de biblioteca padrão do .NET para uma implementação completa.

### <a name="creating-a-net-standard-library"></a>Criar uma biblioteca do .NET padrão

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Adicionar uma biblioteca do .NET padrão para sua solução é bastante direto.

1. Na caixa de diálogo Adicionar novo projeto, selecione o `.NET Standard` categoria e selecione `Class Library(.NET Standard)`.

  ![Criar uma nova biblioteca .NET padrão classe](net-standard-images/vs01.png "nova biblioteca de classe .NET padrão criar")

2. O projeto de biblioteca padrão do .NET aparecerá como mostrado no Gerenciador de soluções. O nó dependências indicará que usa a biblioteca a [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

  ![NETStandard.Library na pasta do projeto](net-standard-images/vs02.png ".NET padrão projeto na solução")

#### <a name="editing-net-standard-library-settings"></a>Editar configurações de biblioteca padrão do .NET

As configurações de biblioteca padrão do .NET podem ser exibidas e alteradas clicando duas vezes no projeto e selecionando `Properties` conforme mostrado nesta captura de tela:

![Editar o .NET Framework de destino padrão nas propriedades do projeto](net-standard-images/vs03.png "fazem referência a uma biblioteca .NET padrão da mesma maneira que outros projetos")

Dentro, você pode alterar sua versão do `netstandard` alterando o `Target Framework` valor de lista suspensa.

**Além disso:** você pode editar o `.csproj` diretamente para alterar esse valor.

#### <a name="using-net-standard-library"></a>Usando a biblioteca padrão do .NET

Quando uma biblioteca do .NET padrão tiver sido criada, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio, clique com botão direito no nó de referências e escolha `Add Reference...` , em seguida, alterne para o `Solution : Projects` guia conforme mostrado:

![Fazendo referência a uma biblioteca do .NET padrão](net-standard-images/vs04.png "no Visual Studio, com o botão direito no nó de referências e escolha Adicionar referência... em seguida, alternar para a guia de solução de projetos, conforme mostrado")

-----

