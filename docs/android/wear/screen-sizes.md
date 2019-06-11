---
title: Trabalhando com tamanhos de tela no sistema operacional do xamarin. Android e o desgaste
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a9b71fb069a428d9bec03481c986f4deb4c904ea
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827737"
---
# <a name="working-with-screen-sizes"></a>Trabalhando com tamanhos de tela

Dispositivos com Android Wear podem ter um retangular ou uma exibição redonda, que também pode ter tamanhos diferentes.

![Exibe capturas de tela de desgaste retangular e round](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Identificando o tipo de tela

A biblioteca de suporte de desgaste fornece alguns controles que ajudam você a detectam e se adaptar às formas de tela diferentes, tais como `WatchViewStub` e `BoxInsetLayout`.

Lembre-se de que alguns dos outros controles da biblioteca de suporte (como `GridViewPager`) *automaticamente* detectar de forma de tela em si e não devem ser adicionados como filhos dos controles descrito abaixo.

### <a name="watchviewstub"></a>WatchViewStub

Consulte a [WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/) exemplo para ver como detectar o tipo de tela e exibir um layout diferente para cada tipo.

O arquivo de layout principal contém um `android.support.wearable.view.WatchViewStub` que faz referência a diferentes layouts para telas retangulares e redondos usando o `app:rectLayout` e `app:roundLayout` atributos:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

A solução contém layouts diferentes para cada estilo que será selecionada no tempo de execução:

![Arquivos mostrados em recursos/layout](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

Em vez de criar layouts diferentes para cada tipo de tela, você também pode criar uma única exibição que se adapta às telas retangulares ou redondas.

Isso [exemplo Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) mostra como usar o `BoxInsetLayout` para usar o mesmo layout em telas retangulares e redondas.


## <a name="wear-ui-designer"></a>Use o Designer de interface do usuário

O Designer do Android Xamarin dá suporte a telas retangulares e redondas:

![Selecionar a tela do Android Wear quadrado no Designer do Android Xamarin](screen-sizes-images/design-screen-type.png)

A superfície de design em estilo retangular é mostrada aqui:

![Superfície de design no estilo retangular](screen-sizes-images/design-rect.png) 

A superfície de design no estilo de arredondamento é mostrada aqui:

![Superfície de design no estilo de arredondamento](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Simulador de desgaste

O **Google Emulator Manager** contém definições de dispositivo para os dois tipos de tela. Você pode criar retangulares e redondos emuladores para testar seu aplicativo.

![Wear definições de dispositivo mostradas no Gerenciador de emulador do Google](screen-sizes-images/emulator-devices.png)

O emulador será renderizado da seguinte forma para uma tela retangular:

![Emulador de renderização de uma tela retangular](screen-sizes-images/recipe-2.png) 

Ele será renderizado da seguinte forma para uma tela arredondada:

![Emulador de renderização de uma tela redonda](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vídeo

[Aplicativos de tela inteira para o Android Wear](https://www.youtube.com/watch?v=naf_WbtFAlY) partir [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

