---
title: Projetos de instalação do Windows
description: Adicionando novos projetos do Windows a uma solução existente do xamarin. Forms
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 0ad8dedc2e92005473f8836c3cdd590ce4cab5ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="setup-windows-projects"></a>Projetos de instalação do Windows

_Adicionando novos projetos do Windows a uma solução existente do xamarin. Forms_

Projetos mais antigos do xamarin. Forms (ou aqueles criados no Mac OS&nbsp;X) não terá esses configuração de projetos do Windows.

Isso significa que você precisará adicionar manualmente esses tipos de projeto para criar aplicativos do Windows 8.1, Windows Phone 8.1 e Windows 10 (UWP).

## <a name="add-a-windows-81-app"></a>Adicionar um aplicativo do Windows 8.1

* Se você usou o modelo PCL, [atualizar o perfil](#pcl), em seguida,
* [Adicionar um aplicativo do Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md) para formatos de tablet/área de trabalho.

## <a name="add-a-windows-phone-81-app"></a>Adicione um Windows Phone 8.1 app

* Se você usou o modelo PCL, [atualizar o perfil](#pcl), em seguida,
* [Adicione um Windows Phone 8.1 app](~/xamarin-forms/platform/windows/installation/phone.md)

## <a name="add-a-universal-windows-platform-uwp-app"></a>Adicione um Windows Universal aplicativo UWP (plataforma)

* Criando [UWP](https://msdn.microsoft.com/library/windows/apps/dn894631.aspx) aplicativos requer o Visual Studio 2015 em execução no Windows 10.
* Se você usou o modelo PCL, [atualizar o perfil](#pcl), em seguida,
* [Adicione um Windows Universal aplicativo de plataforma](~/xamarin-forms/platform/windows/installation/universal.md)

<a name="pcl" />

### <a name="update-your-pcl-profile"></a>Atualizar seu perfil PCL

Se seu aplicativo xamarin. Forms existente usou o modelo de biblioteca de classe portátil (PCL), você deve atualizar seu perfil.

1. **Clique com botão direito > propriedades** (as configurações existentes podem ser diferentes)

  ![](images/targets.png "Destinos PCL")

2. Clique no **alterar...**  botão

3. Verifique se o **Windows 8** e **Windows Phone 8.1** estão selecionadas (e **Silveright do Windows Phone** é *desmarcada*):

  ![](images/pcl.png "Opções de destino PCL")

4. Pressione **Okey** e salve as alterações.

Isso equivale a **perfil 111** se você estiver configurando o PCL no Visual Studio para Mac usando a lista suspensa.

  ![](images/pcl-xs.png "Perfil PCL 111")

**Observação:** se sua solução ainda tem um projeto do Windows Phone 8 Silverlight, o PCL deve ser definido como perfil 259. Suporte do Windows Phone 8 Silverlight está sendo substituído, portanto, é recomendável que você substitua com os tipos de projeto mostrados nesta página.
