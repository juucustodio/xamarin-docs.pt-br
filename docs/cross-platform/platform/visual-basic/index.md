---
title: Visual Basic.NET portátil
description: Este guia explica como Visual Basic pode ser usado para gravar os projetos de biblioteca de classe portátil (PCL) que podem ser usados em soluções de direcionamento xamarin e xamarin.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f37ff8a2d07681ba8e4ec3ed6ad7e5bbd85e6502
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="portable-visual-basicnet"></a>Visual Basic.NET portátil

Xamarin iOS e Android projetos não suportam nativamente o Visual Basic; No entanto os desenvolvedores podem usar bibliotecas de classes portáteis para migrar o código existente do Visual Basic para iOS e Android, ou para gravar uma parte significativa da sua lógica de aplicativo no Visual Basic. Aplicativos xamarin. Forms podem ser criados inteiramente no Visual Basic (exceto renderizadores personalizados, serviços de dependência e XAML Code-behind).

## <a name="requirements"></a>Requisitos

Suporte de biblioteca de classes portátil foi adicionado no xamarin 4.10.1, xamarin 7.0.4 e Xamarin Studio 4.2, que significa que quaisquer projetos Xamarin criados com essas ferramentas podem incorporar assemblies PCL do Visual Basic.

Para criar e compilar bibliotecas de classes portáteis do Visual Basic, você deve usar o Visual Studio no Windows (Visual Studio 2012 ou mais recente).

> [!NOTE]
> Bibliotecas do Visual Basic só podem ser criadas e compilado usando o Visual Studio. Xamarin e xamarin não dão suporte a linguagem Visual Basic.
>
> Se você trabalhar exclusivamente no Visual Studio, você pode referenciar o projeto do Visual Basic de projetos xamarin e xamarin.
>
> Se seus projetos de Android e iOS também devem ser carregados no Visual Studio para Mac deve fazer referência o assembly de saída de PCL do Visual Basic.


## <a name="creating-a-visual-basicnet-pcl"></a>Criando um PCL Basic.NET Visual

Esta seção explica como criar uma biblioteca de classes portátil do Visual Basic usando o Visual Studio.
A biblioteca pode ser referenciada em outros projetos, incluindo aplicativos xamarin, xamarin e xamarin. Forms.

### <a name="creating-a-pcl"></a>Criando um PCL

Ao adicionar um PCL do Visual Basic no Visual Studio, você deve escolher um perfil que descreve quais plataformas de sua biblioteca deve ser compatível com. Perfis são explicados na introdução ao documento PCL.

As etapas para criar um PCL e escolha o seu perfil são:

1.  No **novo projeto** tela, selecione a **Visual Basic > biblioteca de classes (portátil)** opção:

    [![](images/image1-sml.png "Criar nova biblioteca portátil do Visual Basic")](images/image1.png#lightbox)

1.  O Visual Studio solicitará imediatamente com o seguinte **Adicionar biblioteca de classes portátil** caixa de diálogo para que o perfil pode ser configurado. As plataformas que você precisa para dar suporte e pressione de escala **Okey**.

    [![](images/image2-sml.png "Selecione o perfil de PCL escolhendo plataformas")](images/image2.png#lightbox)

1.  O projeto do Visual Basic PCL serão exibidos conforme mostrado no **Solution Explorer** esta aparência:

    [![](images/image3-sml.png "Projeto vazio do PCL do Visual Studio")](images/image3.png#lightbox)


O PCL agora está pronto para o código do Visual Basic a ser adicionado. Projetos PCL podem ser referenciados por outros projetos (projetos de aplicativo, projetos de biblioteca e até mesmo outros projetos PCL).

### <a name="editing-the-pcl-profile"></a>Editar o perfil PCL

O perfil de PCL (que controla quais plataformas de PCL é compatível com) podem ser exibido e alterado clicando duas vezes no projeto e escolhendo **Propriedades > Biblioteca > alterar...** . A caixa de diálogo resultante é mostrada nesta captura de tela:

 [![](images/image4-sml.png "Editar perfil PCL nas propriedades do projeto")](images/image4.png#lightbox)

Se o perfil for alterado depois que o código já foi adicionado para o PCL, é possível que a biblioteca não serão mais compilados se o código faz referência a recursos que não fazem parte do perfil selecionado recentemente.


## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir o código do Visual Basic em aplicativos Xamarin usando o Visual Studio e bibliotecas de classes portáteis. Embora Xamarin não oferece suporte diretamente Visual Basic, Compilando o Visual Basic em um PCL permite código gravado com Visual Basic para ser incluído em aplicativos iOS e Android.

As páginas a seguir descrevem como usar PCLs do Visual Basic.NET no modo nativo ou aplicativos xamarin. Forms:

- [Criando aplicativos nativos de xamarin e xamarin que usam VB](native-apps.md)
- [Criando aplicativos de xamarin. Forms com VB](xamarin-forms.md)


## <a name="related-links"></a>Links relacionados

- [TaskyPortableVB (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)