---
title: Visual Basic.NET portátil
description: Este guia explicará como Visual Basic pode ser usado para gravar os projetos de biblioteca de classe portátil (PCL) que podem ser usados em soluções de direcionamento do xamarin. IOS e xamarin. Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e4c8c43b4df1a7bfc5436f14564c6d0164216c46
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669239"
---
# <a name="portable-visual-basicnet"></a>Visual Basic.NET portátil

Xamarin iOS e Android projetos não suportados nativamente Visual Basic; No entanto os desenvolvedores podem usar bibliotecas de classes portáteis para migrar o código existente do Visual Basic para iOS e Android, ou para gravar uma parte significativa da sua lógica de aplicativo no Visual Basic. Aplicativos xamarin. Forms podem ser criados inteiramente no Visual Basic (excluindo os renderizadores personalizados, serviços de dependência e codebehind XAML).

## <a name="requirements"></a>Requisitos

Suporte de biblioteca de classes portátil foi adicionado no xamarin. Android 4.10.1, xamarin. IOS 7.0.4 e o Xamarin Studio 4.2, que significa que todos os projetos Xamarin criados com essas ferramentas podem incorporar assemblies PCL do Visual Basic.

Para criar e compilar bibliotecas de classes portáteis do Visual Basic, você deve usar o Visual Studio no Windows (Visual Studio 2012 ou mais recente).

> [!NOTE]
> Bibliotecas do Visual Basic só podem ser criadas e compilado usando o Visual Studio. Xamarin. IOS e xamarin. Android não dão suporte a linguagem Visual Basic.
>
> Se você trabalha apenas no Visual Studio, você pode referenciar o projeto do Visual Basic de projetos xamarin. IOS e xamarin. Android.
>
> Se seus projetos do iOS e Android também devem ser carregados no Visual Studio para Mac, você deve referenciar o assembly de saída do Visual Basic PCL.


## <a name="creating-a-visual-basicnet-pcl"></a>Criando uma PCL do Visual Basic.NET

Esta seção explica como criar uma biblioteca de classes portátil do Visual Basic usando o Visual Studio.
A biblioteca pode ser referenciada em outros projetos, incluindo os aplicativos xamarin. IOS, xamarin. Android e xamarin. Forms.

### <a name="creating-a-pcl"></a>Criando uma PCL

Ao adicionar uma PCL do Visual Basic no Visual Studio, você deve escolher um perfil que descreve quais plataformas de sua biblioteca deve ser compatível com. Perfis são explicados na introdução ao documento PCL.

As etapas para criar uma PCL e escolha o seu perfil são:

1.  No **novo projeto** tela, selecione a **Visual Basic > biblioteca de classes (portátil)** opção:

    [![](images/image1-sml.png "Criar nova biblioteca portátil do Visual Basic")](images/image1.png#lightbox)

1.  Visual Studio solicitará imediatamente com o seguinte **adicionar a biblioteca de classes portátil** caixa de diálogo para que o perfil pode ser configurado. Marque as plataformas que você precisa para dar suporte e pressione **Okey**.

    [![](images/image2-sml.png "Selecione o perfil de PCL escolhendo plataformas")](images/image2.png#lightbox)

1.  O projeto de PCL do Visual Basic será exibida conforme mostrado na **Gerenciador de soluções** semelhante a esta:

    [![](images/image3-sml.png "Projeto vazio de PCL do Visual Studio")](images/image3.png#lightbox)


O PCL agora está pronto para o código do Visual Basic a ser adicionado. Projetos PCL podem ser referenciados por outros projetos (projetos de aplicativos, projetos de biblioteca e até mesmo outros projetos PCL).

### <a name="editing-the-pcl-profile"></a>Edição de perfil de PCL

O perfil de PCL (que controla quais plataformas PCL é compatível com) podem ser exibido e alterado clicando com botão direito no projeto e escolhendo **Propriedades > Biblioteca > alterar...** . A caixa de diálogo resultante é mostrada nesta captura de tela:

 [![](images/image4-sml.png "Editar perfil de PCL nas propriedades do projeto")](images/image4.png#lightbox)

Se o perfil for alterado após o código já foi adicionado à PCL, é possível que a biblioteca não serão mais compilados se o código faz referência a recursos que não fazem parte do perfil recém-selecionado.


## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir o código do Visual Basic em aplicativos do Xamarin usando o Visual Studio e bibliotecas de classes portáteis. Mesmo que o Xamarin não oferece suporte diretamente Visual Basic, Compilando o Visual Basic em uma PCL permite que o código escrito com o Visual Basic a ser incluído em aplicativos iOS e Android.

As páginas a seguir descrevem como usar PCLs do Visual Basic.NET no modo nativo ou aplicativos xamarin. Forms:

- [Criar aplicativos xamarin. IOS e xamarin. Android nativos que usam o VB](native-apps.md)
- [Criação de aplicativos xamarin. Forms com VB](xamarin-forms.md)


## <a name="related-links"></a>Links relacionados

- [TaskyPortableVB (amostra)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
