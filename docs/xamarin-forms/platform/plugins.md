---
title: Consumindo e criação de plug-ins do xamarin. Forms
description: Este artigo explica como consumir e criar plug-ins do xamarin. Forms. Plug-ins são normalmente usados para expor facilmente os recursos de plataforma nativa.
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/05/2018
ms.openlocfilehash: 4d121c2dfcca380e1735da1a4ca47c42d1957b8a
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854734"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>Consumindo e criação de plug-ins do xamarin. Forms

Há muitos recursos de plataforma nativa que existem em todas as plataformas, mas têm APIs ligeiramente diferentes. Uma maneira para os desenvolvedores a usar esses recursos é criando uma interface abstrata de plataforma cruzada e, em seguida, implementa essa interface em várias plataformas. O aplicativo xamarin. Forms, em seguida, acessa essas implementações de plataforma usando [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Os desenvolvedores podem compartilhar esse trabalho, escrevendo uma _plug-in_ e publicá-la para o NuGet.

> [!NOTE]
> Muitos recursos de plataforma cruzada disponíveis anteriormente somente por meio de plug-ins agora fazem parte do código-fonte aberto **[Xamarin.Essentials](~/essentials/index.md)** biblioteca. Esses recursos incluem: status da bateria, bússola, sensores de movimento, localização geográfica, texto em fala e muito mais. No futuro, **Xamarin.Essentials** será a principal fonte de recursos de plataforma cruzada para aplicativos xamarin. Forms. Embora os desenvolvedores ainda podem criar e publicar o plug-ins, considere que contribuem para o **Xamarin.Essentials**.

## <a name="finding-and-adding-plugins"></a>Localizar e adicionar plug-ins

O Xamarin community criou muitos plug-ins de plataforma cruzada compatível com o xamarin. Forms. Uma grande coleção pode ser encontrada em:

[**Plug-ins do Xamarin**](https://github.com/xamarin/XamarinComponents)

Para um guia para adicionar pacotes NuGet ao seu projeto, consulte nosso passo a passo sobre [incluindo um pacote do NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough/).

## <a name="creating-plugins"></a>Criando plug-ins

Também é possível criar e publicar seu próprio plug-ins como pacotes Nuget (e componentes do Xamarin). Plug-ins existentes muitos são código-fonte aberto para que você possa examinar seu código para entender como elas tenham sido writtern.

Por exemplo, a lista de plug-ins abaixo são software livre, e eles correspondem a alguns exemplos de [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) seção:

- **Texto em fala** James montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/TextToSpeechPlugin) e [NuGet  ](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech)
- **Status da bateria** James montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/BatteryPlugin) e [NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)

Os projetos do Github podem fornecer um bom ponto de partida para criar seu próprio plug-ins de plataforma cruzada, conforme a seguir essas instruções para [criando um plug-in para Xamarin](https://github.com/xamarin/XamarinComponents#create-a-plugin-for-xamarin).

### <a name="structuring-cross-platform-plugin-projects"></a>Estruturar projetos de plug-in de plataforma cruzada

Embora não haja nenhum requisito específico para a criação de um pacote do NuGet, há algumas diretrizes para criação de um pacote de aplicativos de plataforma cruzada.

No passado, um plug-in de plataforma cruzada geralmente consistiu nos seguintes componentes:

- PCL com uma Interface que representa a API para o plug-in
- bibliotecas com uma implementação da Interface de classe do iOS, Android e plataforma Universal do Windows (UWP).

Leitura James Montemagno [postagem de blog](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/) descrevendo o processo de criação de plug-ins para Xamarin.

Mais recentemente, plug-ins podem ser ser criados com uma única plataforma de multiplataforma. Essa abordagem é discutida em de James Montemagno [postagem de blog](https://montemagno.com/converting-xamarin-libraries-to-sdk-style-multi-targeted-projects/). Essa abordagem é usada no plug-ins de James Montemagno link acima, e também é o formato usado na **Xamarin.Essentials**.

É preferível evitar referenciar xamarin. Forms diretamente de um plug-in.
Isso pode criar problemas de conflito de versão quando outros desenvolvedores tentam usar o plug-in. Em vez disso, tente criar a API, de modo que ele pode ser usado por qualquer aplicativo Xamarin ou .NET.

### <a name="publishing-nuget-packages"></a>Publicação de pacotes do NuGet

Pacotes do NuGet têm uma **nuspec** arquivo, que é um arquivo xml que define quais partes do seu projeto são publicados no pacote. O **nuspec** arquivo também inclui informações sobre o pacote, como id, título e autores.

Ver [documentação do NuGet](/nuget/create-packages/creating-a-package.md) para obter mais informações sobre como criar e publicar pacotes do NuGet.

## <a name="related-links"></a>Links relacionados

- [Criando reutilizáveis plug-ins para xamarin. Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Usando & desenvolvendo plug-ins para Xamarin (vídeo)](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
