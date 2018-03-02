---
title: Layout para aplicativos de Desktop e Tablet
ms.topic: article
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 053696ebf37e73e3b121e2aa52b80b7ea1b8ed64
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Layout para aplicativos de Desktop e Tablet

Xamarin. Forms oferece suporte a todos os tipos de dispositivo disponíveis em plataformas com suporte, além de telefones, os aplicativos também podem executar em:

* iPads,
* Tablets Android,
* Tablets Windows e computadores desktop (executando o Windows 8.1 ou Windows 10).

Esta página descreve brevemente:

* com o suporte [tipos de dispositivo](#Device_Types), e
* como [otimizar](#optimize) layouts para tablets e telefones.

<a name="Device_Types" />

## <a name="device-types"></a>Tipos de dispositivo

Dispositivos de tela maiores estão disponíveis para todas as plataformas com suporte de xamarin. Forms.

### <a name="ipads-ios"></a>iPads (iOS)

O modelo de xamarin. Forms automaticamente inclui suporte iPad, configurando o **Info. plist > dispositivos** definindo como **Universal** (que significa que há suporte para iPhone e iPad).

Para fornecer uma experiência de inicialização agradável e certifique-se de que a resolução de tela inteira é usada em todos os dispositivos, você deve verificar se um [tela inicial do iPad específico](~/ios/app-fundamentals/images-icons/launch-screens.md) (usando um storyboard) é fornecido. Isso garante que o aplicativo é renderizado corretamente em dispositivos de iPad mini, iPad e iPad Pro.

Antes do iOS 9 todos os aplicativos levaram a tela inteira no dispositivo, mas agora pode executar alguns iPads [dividir tela multitarefa](~/ios/platform/multitasking.md).
Isso significa que seu aplicativo pode levar apenas uma coluna slim ao lado da tela, 50% da largura da tela, ou a tela inteira.

[ ![](tablet-images/ipad-sml.png "Exemplo de tela de divisão de iPad")](tablet-images/ipad.png "iPad exemplo de tela de divisão")

Funcionalidade de tela dividida significa que você deve projetar seu aplicativo para funcionar bem com até 320 pixels de largura ou 1366 conforme pixels de largura.

### <a name="android-tablets"></a>Tablets Android

O ecossistema do Android tem uma grande variedade de tamanhos de telas com suporte, de telefones pequenos até tablets grandes. Xamarin. Forms pode dar suporte a todos os tamanhos de tela, mas como com as outras plataformas você talvez queira ajustar sua interface de usuário para dispositivos maiores.

Ao oferecer suporte a muitos diferentes resoluções de tela, você pode fornecer os recursos de imagem nativa em tamanhos diferentes para otimizar a experiência do usuário.
Examine o [recursos Android](~/android/app-fundamentals/resources-in-android/index.md) documentação (e, em particular [criando recursos para tamanhos de tela de variável](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) para obter mais informações sobre como estruturar as pastas e os nomes de arquivo em seu aplicativo Android projeto para incluir recursos de imagem otimizado em seu aplicativo.

### <a name="windows-tablets-and-desktops"></a>Áreas de trabalho e Tablets Windows

Para dar suporte a tablets e computadores desktop que executam o Windows, você precisará usar um dos dois tipos de projeto com suporte:

* [Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md) -
  cria aplicativos especificamente para Windows 8.1 tablets e desktops.
* [Suporte do Windows UWP](~/xamarin-forms/platform/windows/installation/universal.md) -
  cria aplicativos universais que são executados no Windows 10 telefones, tablets e áreas de trabalho.

Aplicativos executados em computadores e tablets Windows podem ser redimensionados para dimensões arbitrários além para tela cheia está em execução.

[ ![](tablet-images/splitscreen-sml.png "Exemplo de tela de divisão do Windows")](tablet-images/splitscreen.png "exemplo de tela de divisão do Windows")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Otimizando para Tablet e área de trabalho

Você pode ajustar sua interface de usuário do xamarin. Forms dependendo se um telefone ou dispositivo tablet/área de trabalho está sendo usado. Isso significa que você pode otimizar a experiência do usuário para dispositivos de tela grande, como tablets e computadores desktop.


### <a name="deviceidiom"></a>Device.Idiom

Você pode usar o [ `Device` ](~/xamarin-forms/platform/device.md) classe para alterar o comportamento da sua interface de usuário ou aplicativo. Usando o `Device.Idiom` enumeração, você pode

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Essa abordagem pode ser expandida para fazer alterações significativas em layouts de página individuais ou até mesmo para renderizar páginas completamente diferentes em telas maiores.

### <a name="leveraging-masterdetailpage"></a>Leveraging MasterDetailPage

O [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) é ideal para telas maiores, especialmente no iPad em que ele usa o [ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/) para fornecer uma experiência de iOS nativo.

Revisão [esta postagem de blog de Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) para ver como você pode adaptar a interface do usuário para que os telefones usam um layout e telas maiores podem usar outro (com o `MasterDetailPage`).



## <a name="related-links"></a>Links relacionados

- [Blog do Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Exemplo de MyShoppe](https://github.com/jamesmontemagno/myshoppe)
