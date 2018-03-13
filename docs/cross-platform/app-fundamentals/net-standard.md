---
title: .NET Standard
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/12/2017
ms.openlocfilehash: f448a3ee9c018aa475775a5ac2c614f3e7ddc324
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="net-standard"></a>.NET Standard

## <a name="using-net-standard-library-projects-to-share-code"></a>Usando projetos de biblioteca padrão do .NET para compartilhar código

O .NET Standard Library é uma especificação formal de APIs do .NET que devem estar disponíveis em todos os tempos de execução do .NET. A motivação por trás do Standard Library é estabelecer maior uniformidade no ecossistema do .NET.
[ECMA 335](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md) continua a estabelecer a uniformidade de comportamento de tempo de execução do .NET, mas não há especificação semelhante para a BCL (Biblioteca de Classes Base) para implementações da biblioteca do .NET.

Você pode pensar nele como simplificado, próxima geração de [biblioteca de classes portátil](https://msdn.microsoft.com/library/gg597391.aspx).
É uma única biblioteca com uma API uniforme para todas as plataformas do .NET, incluindo o .NET Core. Basta você cria uma única biblioteca padrão do .NET e usá-lo de qualquer tempo de execução que oferece suporte à plataforma padrão .NET.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

## <a name="xamarin-studio"></a>Xamarin Studio

Projetos de biblioteca .NET padrão podem ser criados no Xamarin Studio 6.2, criando primeiro um projeto de biblioteca portátil:

[![](net-standard-images/xs01-sml.png "Criar um novo projeto de biblioteca portátil")](net-standard-images/xs01.png#lightbox)

Quando o projeto tiver sido criado, clique com botão direito e abra o **opções de projeto** janela.
No **geral** seção projeto pode ser convertido em .NET padrão e definido para usar uma versão específica do **plataforma** lista suspensa:

[![](net-standard-images/xs02-sml.png "Converter em .NET padrão em geral, opções")](net-standard-images/xs02.png#lightbox)

Você pode, em seguida, [criar um pacote do NuGet](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/existing-library.md) compartilhamento de biblioteca com outros desenvolvedores.

## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac

Esta seção explica como criar e usar uma biblioteca do .NET padrão usando o Visual Studio para Mac. Consulte a seção exemplo de biblioteca padrão do .NET para uma implementação completa.

### <a name="creating-a-net-standard-library"></a>Criar uma biblioteca do .NET padrão

Adicionar uma biblioteca do .NET padrão para sua solução é bastante direto.

1. Na caixa de diálogo Adicionar novo projeto, selecione o `.NET Core` categoria e selecione `Class Library(.NET Core)`.

  **Observação:** este modelo será renomeado para `.NET Standard` em uma versão futura do Visual Studio para Mac.

  ![Criar uma biblioteca de classes do .NET Core](net-standard-images/vsm01.png)

2. O projeto de biblioteca padrão do .NET aparecerá como mostrado no Gerenciador de soluções. O nó dependências indicará que usa a biblioteca a [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

  ![Nó de dependências na solução indica .NET padrão](net-standard-images/vsm02.png)

#### <a name="editing-net-standard-library-settings"></a>Editar configurações de biblioteca padrão do .NET

As configurações de biblioteca padrão do .NET podem ser exibidas e alteradas clicando duas vezes no projeto e selecionando `Options` conforme mostrado nesta captura de tela:

![Editar padrão do .NET framework de destino nas opções de projeto](net-standard-images/vsm03.png)

Dentro, você pode alterar sua versão do `netstandard` alterando o `Target Framework` valor de lista suspensa.

**Além disso:** você pode editar o `.csproj` diretamente para alterar esse valor.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="visual-studio-windows-walkthrough"></a>Instruções passo a passo do Visual Studio (Windows)

Esta seção explica como criar e usar uma biblioteca do .NET padrão usando o Visual Studio. Consulte a seção exemplo de biblioteca padrão do .NET para uma implementação completa.

### <a name="creating-a-net-standard-library"></a>Criar uma biblioteca do .NET padrão

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Adicionar uma biblioteca do .NET padrão para sua solução é bastante direto.

1. Na caixa de diálogo Adicionar novo projeto, selecione o `.NET Standard` categoria e selecione `Class Library(.NET Standard)`.

  ![](net-standard-images/vs01.png "Criar nova biblioteca de classe .NET padrão")

2. O projeto de biblioteca padrão do .NET aparecerá como mostrado no Gerenciador de soluções. O nó dependências indicará que usa a biblioteca a [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

  ![](net-standard-images/vs02.png "Projeto .NET padrão na solução")

#### <a name="editing-net-standard-library-settings"></a>Editar configurações de biblioteca padrão do .NET

As configurações de biblioteca padrão do .NET podem ser exibidas e alteradas clicando duas vezes no projeto e selecionando `Properties` conforme mostrado nesta captura de tela:

![](net-standard-images/vs03.png "Fazer referência a uma biblioteca .NET padrão da mesma maneira que outros projetos")

Dentro, você pode alterar sua versão do `netstandard` alterando o `Target Framework` valor de lista suspensa.

**Além disso:** você pode editar o `.csproj` diretamente para alterar esse valor.

#### <a name="using-net-standard-library"></a>Usando a biblioteca padrão do .NET

Quando uma biblioteca do .NET padrão tiver sido criada, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio, clique com botão direito no nó de referências e escolha `Add Reference...` , em seguida, alterne para o `Solution : Projects` guia conforme mostrado:

![](net-standard-images/vs04.png "No Visual Studio, com o botão direito no nó de referências e escolha Adicionar referência... em seguida, alternar para a guia de solução de projetos, conforme mostrado")

-----


## <a name="related-links"></a>Links relacionados

- [Notas de Versão](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#.NET_Standard_Support)
