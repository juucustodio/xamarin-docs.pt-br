---
title: Trabalhando com tamanhos de tela
ms.topic: article
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 2bb42a862ae8d9fb4c94a044542700dbc324f35b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-screen-sizes"></a>Trabalhando com tamanhos de tela

Dispositivos com Android desgaste podem ter um retangular ou uma exibição round, que também pode ter tamanhos diferentes.

![Exibe as capturas de tela de desgaste retangular e round](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Identifica o tipo de tela

A biblioteca de suporte de desgaste fornece alguns controles que ajudam a detectam e adaptar-se às formas de tela diferente, como `WatchViewStub` e `BoxInsetLayout`.

Lembre-se de que alguns dos outros controles da biblioteca de suporte (como `GridViewPager`) *automaticamente* detectar se a forma de tela e não devem ser adicionados como filhos dos controles descritos abaixo.

### <a name="watchviewstub"></a>WatchViewStub

Consulte o [WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/) exemplo para detectar o tipo de tela e exibir um layout diferente para cada tipo.

O arquivo de layout principal contém um `android.support.wearable.view.WatchViewStub` que faz referência a layouts diferentes para telas retangulares e round usando o `app:rectLayout` e `app:roundLayout` atributos:

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

Em vez de criar layouts diferentes para cada tipo de tela, você também pode criar uma exibição única que se adapta a telas retangulares ou round.

Isso [exemplo Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) mostra como usar o `BoxInsetLayout` para usar o mesmo layout retangular e round telas.


## <a name="wear-ui-designer"></a>Use o Designer de interface do usuário

O Designer Android Xamarin dá suporte a telas de retangulares e round:

![Selecionar a tela do Android desgaste quadrado no Designer Xamarin Android](screen-sizes-images/design-screen-type.png)

A superfície de design em estilo retangular é mostrada aqui:

![Superfície de design em estilo retangular](screen-sizes-images/design-rect.png) 

A superfície de design em estilo round é mostrada aqui:

![Superfície de design no estilo round](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Usar o simulador

O **Gerenciador de emulador do Google** contém definições de dispositivo para os dois tipos de tela. Você pode criar retangulares e round emuladores para testar seu aplicativo.

![Usar definições de dispositivo mostradas no Gerenciador de emulador do Google](screen-sizes-images/emulator-devices.png)

O emulador será renderizado como este para uma tela retangular:

![Emulador de renderização de uma tela retangular](screen-sizes-images/recipe-2.png) 

Ele processará assim para uma tela de arredondamento:

![Emulador de renderização de uma tela de arredondamento](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Vídeo

[Aplicativos de tela inteira para Android desgaste](https://www.youtube.com/watch?v=naf_WbtFAlY) de [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

