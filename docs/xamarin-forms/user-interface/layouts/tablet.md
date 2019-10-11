---
title: Layout para aplicativos Tablet e desktop
description: Este artigo explica como otimizar layouts de aplicativo Xamarin. Forms para tablets, em vez de telefones.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: f91d0127d0f2ffe37e3e0ff016dee551a679ad84
ms.sourcegitcommit: e354aabfb39598e0ce11115db3e6bcebb9f68338
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273117"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Layout para aplicativos Tablet e desktop

O Xamarin. Forms dá suporte a todos os tipos de dispositivo disponíveis nas plataformas com suporte, assim, além dos telefones, os aplicativos também podem ser executados em:

- iPads
- Tablets Android,
- Tablets e computadores desktop com Windows (executando o Windows 10).

Esta página discute brevemente:

- os [tipos de dispositivo](#Device_Types)com suporte e
- como [otimizar](#optimize) layouts para tablets versus telefones.

<a name="Device_Types" />

## <a name="device-types"></a>Tipos de dispositivo

Dispositivos de tela maiores estão disponíveis para todas as plataformas com suporte no Xamarin. Forms.

### <a name="ipads-ios"></a>iPads (iOS)

O modelo Xamarin. Forms inclui automaticamente o suporte a iPad ao definir a configuração do **info. plist > dispositivos** como **Universal** (o que significa que o iPhone e o iPad têm suporte).

Para fornecer uma experiência de inicialização agradável e garantir que a resolução de tela inteira seja usada em todos os dispositivos, você deve verificar se uma [tela de inicialização específica para iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (usando um storyboard) é fornecida. Isso garante que o aplicativo seja renderizado corretamente em dispositivos iPad mini, iPad e iPad pro.

Antes do iOS 9, todos os aplicativos usavam a tela inteira no dispositivo, mas alguns iPads agora podem executar [multitarefas de tela dividida](~/ios/platform/multitasking.md).
Isso significa que seu aplicativo pode ocupar apenas uma coluna fina no lado da tela, 50% da largura da tela ou na tela inteira.

[![](tablet-images/ipad-sml.png "Exemplo de tela de divisão do iPad")](tablet-images/ipad.png#lightbox "Exemplo de tela de divisão do iPad")

A funcionalidade de tela de divisão significa que você deve projetar seu aplicativo para funcionar bem com apenas 320 pixels de largura ou até 1366 pixels de largura.

### <a name="android-tablets"></a>Tablets Android

O ecossistema do Android tem uma infinidade de tamanhos de tela com suporte, de pequenos telefones até tablets grandes. O Xamarin. Forms pode dar suporte a todos os tamanhos de tela, mas, assim como nas outras plataformas, convém ajustar a interface do usuário para dispositivos maiores.

Ao oferecer suporte a várias resoluções de tela diferentes, você pode fornecer seus recursos de imagem nativa em tamanhos diferentes para otimizar a experiência do usuário.
Examine a documentação de [recursos do Android](~/android/app-fundamentals/resources-in-android/index.md) (e, em particular, [criando recursos para diferentes tamanhos de tela](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) para obter mais informações sobre como estruturar as pastas e nomes de arquivos em seu projeto de aplicativo Android para incluir recursos de imagem otimizados no seu aplicativo.

### <a name="windows-tablets-and-desktops"></a>Tablets e áreas de trabalho do Windows

Para dar suporte a tablets e computadores desktop que executam o Windows, você precisará usar o [suporte do Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), que cria aplicativos universais que são executados no Windows 10.

Os aplicativos executados em tablets e desktops Windows podem ser redimensionados para dimensões arbitrárias, além da execução de tela inteira.

[![](tablet-images/splitscreen-sml.png "Exemplo de divisão de tela do Windows")](tablet-images/splitscreen.png#lightbox "Exemplo de divisão de tela do Windows")

<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Otimizando para Tablet e desktop

Você pode ajustar sua interface do usuário do Xamarin. Forms dependendo se um telefone ou dispositivo tablet/Desktop está sendo usado. Isso significa que você pode otimizar a experiência do usuário para dispositivos de tela larga, como tablets e computadores desktop.

### <a name="deviceidiom"></a>Device.Idiom

Você pode usar a classe [`Device`](~/xamarin-forms/platform/device.md) para alterar o comportamento do aplicativo ou da interface do usuário. Usando a enumeração `Device.Idiom`, você pode

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Essa abordagem pode ser expandida para fazer alterações significativas em layouts de página individuais ou até mesmo para renderizar páginas totalmente diferentes em telas maiores.

### <a name="leveraging-masterdetailpage"></a>Aproveitando o MasterDetailPage

O [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) é ideal para telas maiores, especialmente no iPad, em que ele usa o [`UISplitViewController`](xref:UIKit.UISplitViewController) para fornecer uma experiência de Ios nativa.

Examine [esta postagem de blog do Xamarin](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/) para ver como você pode adaptar sua interface do usuário para que os telefones usem um layout e telas maiores possam usar outro (com o `MasterDetailPage`).

## <a name="related-links"></a>Links relacionados

- [Blog do Xamarin](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [Exemplo de MyShoppe](https://github.com/jamesmontemagno/myshoppe)
