---
title: Trabalhando com tamanhos de tela no Xamarin. Android e no sistema operacional de desgaste
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 93e6797f2b00df32b8d3ae361f40fd487b7adac3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647721"
---
# <a name="working-with-screen-sizes"></a>Trabalhando com tamanhos de tela

Os dispositivos Android com desgaste podem ter uma exibição retangular ou round, que também pode ser de tamanhos diferentes.

![Capturas de tela de exibições retangulares e de ida e volta](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Identificando tipo de tela

A biblioteca de suporte de desgaste fornece alguns controles que ajudam você a detectar e adaptar-se a diferentes `WatchViewStub` formas `BoxInsetLayout`de tela, como e.

Lembre-se de que alguns dos outros controles de biblioteca de suporte `GridViewPager`(como) detectam *automaticamente* a forma da tela e não devem ser adicionados como filhos dos controles descritos abaixo.

### <a name="watchviewstub"></a>WatchViewStub

Consulte o exemplo de [WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub) para ver como detectar o tipo de tela e exibir um layout diferente para cada tipo.

O arquivo de layout principal contém `android.support.wearable.view.WatchViewStub` um que faz referência a layouts diferentes para telas retangulares e `app:rectLayout` arredondadas usando os atributos e `app:roundLayout` :

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

A solução contém layouts diferentes para cada estilo que será selecionado em tempo de execução:

![Arquivos mostrados em recursos/layout](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

Em vez de criar layouts diferentes para cada tipo de tela, você também pode criar uma única exibição que se adapte a telas retangulares ou arredondadas.

Este [exemplo do Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) mostra como usar o `BoxInsetLayout` para usar o mesmo layout em telas retangulares e arredondadas.


## <a name="wear-ui-designer"></a>Designer de interface do usuário de desgaste

O Designer Android Xamarin dá suporte a telas retangulares e arredondadas:

![Selecionando a tela quadrada de desgaste do Android no Designer Android do Xamarin](screen-sizes-images/design-screen-type.png)

A superfície de design em estilo retangular é mostrada aqui:

![Superfície de design em estilo retangular](screen-sizes-images/design-rect.png) 

A superfície de design em estilo redondo é mostrada aqui:

![Superfície de design em estilo redondo](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Simulador de desgaste

O **Gerenciador do Google Emulator** contém definições de dispositivo para ambos os tipos de tela. Você pode criar emuladores retangulares e arredondados para testar seu aplicativo.

![Definições de dispositivo de desgaste mostradas no Gerenciador de emulador do Google](screen-sizes-images/emulator-devices.png)

O emulador será renderizado como este para uma tela retangular:

![Renderização de emulador de uma tela retangular](screen-sizes-images/recipe-2.png) 

Ele será renderizado como este para uma tela de ida e volta:

![Renderização de emulador de uma tela de ida e volta](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vídeo

[Aplicativos de tela inteira para desgaste de Android](https://www.youtube.com/watch?v=naf_WbtFAlY) do [Developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

