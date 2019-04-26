---
title: Layout para aplicativos de Tablet e área de trabalho
description: Este artigo explica como otimizar os layouts de aplicativos xamarin. Forms para tablets, em vez de telefones.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 9d1f54fa4753ba2ef44ba9b8b48a84a3ca932c4b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369884"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Layout para aplicativos de Tablet e área de trabalho

Xamarin. Forms oferece suporte a todos os tipos de dispositivo disponíveis em plataformas com suporte, além de telefones, os aplicativos também podem executar em:

* iPads,
* Tablets Android,
* Tablets Windows e computadores desktop (executando o Windows 10).

Esta página aborda resumidamente:

* com suporte [tipos de dispositivo](#Device_Types), e
* como [otimizar](#optimize) layouts para tablets e telefones.

<a name="Device_Types" />

## <a name="device-types"></a>Tipos de dispositivo

Dispositivos de tela maiores estão disponíveis para todas as plataformas com suporte pelo xamarin. Forms.

### <a name="ipads-ios"></a>iPads (iOS)

O modelo do xamarin. Forms inclui automaticamente suporte a iPad, configurando o **Info. plist > dispositivos** definir como **Universal** (o que significa que há suporte para iPhone e iPad).

Para fornecer uma experiência de inicialização agradável e verifique se a resolução de tela inteira é usada em todos os dispositivos, você deve verificar se um [tela de inicialização específicas do iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (usando um storyboard) é fornecido. Isso garante que o aplicativo é renderizado corretamente em dispositivos de iPad mini, iPad e iPad Pro.

Antes do iOS 9 todos os aplicativos ocupou toda a tela no dispositivo, mas agora pode executar alguns iPads [dividir a tela multitarefa](~/ios/platform/multitasking.md).
Isso significa que seu aplicativo pode levar apenas uma coluna slim na lateral da tela, 50% da largura da tela, ou a tela inteira.

[![](tablet-images/ipad-sml.png "Dividir o exemplo de tela do iPad")](tablet-images/ipad.png#lightbox "dividir o exemplo de tela do iPad")

O Split screen funcionalidade significa que você deve projetar seu aplicativo funcione bem com até 320 pixels de largura ou de 1366 tanto quanto pixels de largura.

### <a name="android-tablets"></a>Tablets Android

O ecossistema do Android tem uma grande variedade de tamanhos de tela com suporte, de telefones pequeno até grandes tablets. Xamarin. Forms pode dar suporte a todos os tamanhos de tela, mas como com outras plataformas você talvez queira ajustar sua interface do usuário para dispositivos maiores.

Ao dar suporte a muitos diferentes resoluções de tela, você pode fornecer os recursos de imagem nativa em tamanhos diferentes para otimizar a experiência do usuário.
Examine os [recursos do Android](~/android/app-fundamentals/resources-in-android/index.md) documentação (e, em particular [criar recursos para abranger diferentes tamanhos de tela](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) para obter mais informações sobre como estruturar as pastas e os nomes de arquivo em seu aplicativo Android projeto para incluir recursos de imagem otimizada em seu aplicativo.

### <a name="windows-tablets-and-desktops"></a>Desktops e Tablets Windows

Para dar suporte a tablets e computadores desktop que executam o Windows, você precisará usar [suporte do Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), que compila aplicativos universais que são executados no Windows 10.

Aplicativos que são executados em desktops e tablets Windows podem ser redimensionados para dimensões arbitrárias além para tela inteira em execução.

[![](tablet-images/splitscreen-sml.png "Exemplo de tela de divisão do Windows")](tablet-images/splitscreen.png#lightbox "Windows dividir a tela de exemplo")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Otimizando para Tablet e área de trabalho

Você pode ajustar sua interface do usuário do xamarin. Forms, dependendo se um telefone ou dispositivo de tablet/área de trabalho está sendo usado. Isso significa que você pode otimizar a experiência do usuário para dispositivos de tela grande, como tablets e computadores desktop.


### <a name="deviceidiom"></a>Device.Idiom

Você pode usar o [ `Device` ](~/xamarin-forms/platform/device.md) classe para alterar o comportamento da interface do usuário ou aplicativo. Usando o `Device.Idiom` enumeração, você pode

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Essa abordagem pode ser expandida para fazer alterações significativas aos layouts de página individual, ou até mesmo para renderizar páginas inteiramente diferentes em telas maiores.

### <a name="leveraging-masterdetailpage"></a>Aproveitando MasterDetailPage

O [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) é ideal para telas maiores, especialmente no iPad em que ele usa o [ `UISplitViewController` ](xref:UIKit.UISplitViewController) para fornecer uma experiência do iOS nativo.

Revisão [esta postagem de blog do Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) para ver como você pode adaptar sua interface do usuário para que os telefones usam um layout e telas maiores podem usar outro (com o `MasterDetailPage`).



## <a name="related-links"></a>Links relacionados

- [Blog do Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Exemplo de MyShoppe](https://github.com/jamesmontemagno/myshoppe)
