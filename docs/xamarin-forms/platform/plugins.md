---
title: Consumindo e criando plug-ins do xamarin. Forms
description: Este artigo explica como consumir e criar plug-ins do xamarin. Forms. Plug-ins são normalmente usadas para expor facilmente os recursos de plataforma nativo.
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2016
ms.openlocfilehash: dff9fad0da30475a0fb91c0af76a25ea50d34439
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242551"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>Consumindo e criando plug-ins do xamarin. Forms

Há muitos recursos de plataforma nativo que existem em todas as plataformas, mas tem APIs ligeiramente diferentes. Os desenvolvedores escrever plug-ins para criar uma interface de plataforma cruzada abstrata para os recursos que eles também poderão compartilhar com outras pessoas.

Esses recursos incluem: status da bateria, bússola, sensores de movimento, localização geográfica, texto em fala e muito mais. Plug-ins permitir que esses recursos para ser facilmente acessados por aplicativos xamarin. Forms.

## <a name="finding-and-adding-plugins"></a>Localizar e adicionar plug-ins

Comunidade do Xamarin criou muitos plug-ins de plataforma cruzada compatível com o xamarin. Forms - uma grande coleção pode ser encontrada em:

[**Plug-ins do Xamarin**](https://github.com/xamarin/plugins)

Para obter um guia para adicionar pacotes do NuGet ao seu projeto, consulte nosso passo a passo sobre [incluindo um pacote do NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough/).


## <a name="creating-plugins"></a>Criando plug-ins

Também é possível criar e publicar seu próprio plug-ins como pacotes do Nuget (e componentes de Xamarin). Plug-ins existentes muitos são código-fonte aberto, portanto, você pode revisar seu código para entender como eles foram writtern.

Por exemplo, a lista de plug-ins abaixo são todos os software livre, e eles correspondem aos exemplos no [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) seção:

- **Texto em fala** por James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/TextToSpeech) e [NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)
- **Status da bateria** por James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery) e [NuGet](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech/)

Esses projetos Github podem fornecer um bom ponto de partida para criar seu próprio plug-ins de plataforma cruzada, assim como estas instruções para [criar um plug-in para Xamarin](https://github.com/xamarin/plugins#create-a-plugin-for-xamarin).

### <a name="structuring-cross-platform-plugin-projects"></a>Estruturação de projetos de plug-in de plataforma cruzada

Embora não haja nenhum requisito específico para a criação de um pacote do NuGet, há algumas diretrizes para a criação de um pacote de aplicativos de plataforma cruzada.

Um plug-in de plataforma cruzada deve consistir em geral os seguintes componentes:

- PCL com uma Interface que representa a API para o plug-in
- bibliotecas com uma implementação da Interface de classe do iOS, Android e Windows.

Da leitura James Montemagno [postagem de blog](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/) que descreve o processo de criação de plug-ins para Xamarin.

É preferível para evitar referenciando xamarin. Forms diretamente de um plug-in.
Isso pode criar problemas de conflito de versão quando outros desenvolvedores tentam usar o plug-in. Em vez disso, tente criar a API para que ele pode ser usado por qualquer aplicativo Xamarin ou .NET.

### <a name="publishing-nuget-packages"></a>Publicar pacotes do NuGet

Pacotes do NuGet têm um **nuspec** arquivo, que é um arquivo xml que define quais partes do seu projeto são publicados no pacote. O **nuspec** arquivo também inclui informações sobre o pacote, como id, título e autores.

Consulte [documentação do NuGet](http://docs.nuget.org/create/creating-and-publishing-a-package) para obter mais informações sobre como criar e publicar pacotes do NuGet.


## <a name="related-links"></a>Links relacionados

- [Criando plug-ins reutilizáveis para xamarin. Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Usar & desenvolvimento plug-ins para Xamarin (vídeo)](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
