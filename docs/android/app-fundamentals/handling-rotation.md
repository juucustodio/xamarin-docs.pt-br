---
title: Tratamento de rotação
description: Este tópico descreve como manipular as alterações de orientação do dispositivo no xamarin. Android. Ele aborda como trabalhar com o sistema de recurso do Android para carregar automaticamente os recursos para uma orientação de dispositivo específico, bem como manipular programaticamente a orientação muda.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 62e64be89e26e5a8412cd34221da581e99fc5e6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017286"
---
# <a name="handling-rotation"></a>Tratamento de rotação

_Este tópico descreve como manipular as alterações de orientação do dispositivo no xamarin. Android. Ele aborda como trabalhar com o sistema de recurso do Android para carregar automaticamente os recursos para uma orientação de dispositivo específico, bem como manipular programaticamente a orientação muda._


## <a name="overview"></a>Visão geral

Porque os dispositivos móveis são girados facilmente, rotação interna é um recurso padrão em sistemas operacionais móveis. O Android fornece uma estrutura sofisticada para lidar com a rotação em aplicativos, se a interface do usuário será criada declarativamente em XML ou programaticamente no código. Ao manipular automaticamente as alterações de layout declarativa em um dispositivo girado, um aplicativo pode aproveitar a total integração com o sistema de recurso do Android. Para o layout programática, as alterações devem ser tratadas manualmente. Isso permite um melhor controle em tempo de execução, mas às custas de mais de trabalho para o desenvolvedor. Um aplicativo também pode optar por recusar a reinicialização de atividade e assumir o controle manual das alterações de orientação.

Este guia examina os tópicos de orientação a seguir:

-   **Rotação de Layout declarativa** &ndash; como usar o sistema de recurso do Android para compilar aplicativos com reconhecimento de orientação, incluindo como carregar layouts e desenháveis para orientações específicas.

-   **Rotação de Layout programáticas** &ndash; adicionar controles de forma programática, bem como lidar com alterações de orientação manualmente.


## <a name="handling-rotation-declaratively-with-layouts"></a>Tratamento de rotação declarativamente com Layouts

Incluindo arquivos em pastas que seguem as convenções de nomenclatura, o Android carrega automaticamente os arquivos apropriados quando altera a orientação.
Isso inclui suporte para:

-   *Recursos de layout* &ndash; especificando quais arquivos de layout são inflados para cada orientação.

-   *Recursos desenháveis* &ndash; especificando quais desenháveis são carregados para cada orientação.


### <a name="layout-resources"></a>Recursos de layout

Por padrão, os arquivos XML Android (AXML) incluído na **recursos/layout** pasta são usados para renderizar exibições para uma atividade. Os recursos desta pasta são usados para orientação de retrato e paisagem se não há recursos de layout adicionais são fornecidos especificamente para paisagem. Considere a estrutura de projeto criada pelo modelo de projeto padrão:

[![Estrutura de modelo de projeto padrão](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Esse projeto cria uma única **Main. axml** arquivo na **recursos/layout** pasta. Quando a atividade `OnCreate` método é chamado, ele descompacta o modo de exibição definido no **Main. axml,** que declara um botão, conforme mostrado no XML abaixo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

Se o dispositivo for girado para orientação, a atividade de paisagem `OnCreate` método é chamado novamente e o mesmo **Main. axml** arquivo será inflado, conforme mostrado na captura de tela abaixo:

[![Mesma tela, mas na orientação paisagem](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Layouts de orientação específica

Além da pasta de layout (que assume como padrão para retrato e também podem ser explicitamente nomeado *layout-port* com a inclusão de uma pasta chamada `layout-land`), um aplicativo pode definir os modos de exibição que ele precisa quando em Paisagem sem nenhum código alterações.

Suponha que o **Main. axml** arquivo continha o XML a seguir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Se uma pasta chamada land layout que contém um adicional **Main. axml** arquivo é adicionado ao projeto, aumentando o layout quando em Paisagem agora resulta em Android Carregando recém-adicionada **Main. axml.** Considere a versão de paisagem do **Main. axml** arquivo que contém o código a seguir (para simplificar, este XML é semelhante para a versão de retrato padrão do código, mas usa uma cadeia de caracteres diferente no `TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

Executar esse código e girar o dispositivo de retrato para paisagem demonstra o carregamento de XML novo, conforme mostrado abaixo:

[![Retrato e paisagem capturas de tela imprimindo o modo de retrato](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>Recursos desenháveis

Durante a rotação, o Android trata recursos desenháveis da mesma forma que os recursos de layout. Nesse caso, o sistema obtém desenháveis a partir de **recursos/drawable** e **recursos/drawable-land** pastas, respectivamente.

Por exemplo, digamos que o projeto inclui uma imagem chamada Monkey.png na **recursos/drawable** pasta, onde o desenháveis é referenciado a partir um `ImageView` em XML como esta:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Vamos considerar ainda que uma versão diferente do **Monkey.png** está incluído nos **recursos/drawable-land**. Assim como com os arquivos de layout, quando o dispositivo for girado, as alterações desenháveis para a orientação fornecida, conforme mostrado abaixo:

[![Versão diferente do Monkey.png mostrados nos modos retrato e paisagem](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Tratamento de rotação programaticamente

Às vezes, podemos definir layouts no código. Isso pode ocorrer por vários motivos, incluindo limitações técnicas, preferência de desenvolvedor, etc. Quando adicionamos controles por meio de programação, um aplicativo deve considerar manualmente orientação do dispositivo, que é tratada automaticamente quando usamos recursos XML.


### <a name="adding-controls-in-code"></a>Adicionando controles no código

Para adicionar controles de forma programática, um aplicativo precisa executar as seguintes etapas:

-  Crie um layout.
-  Definir parâmetros de layout.
-  Crie controles.
-  Definir parâmetros de controle de layout.
-  Adicione controles ao layout.
-  Define o layout como a exibição de conteúdo.

Por exemplo, considere uma interface do usuário consiste em uma única `TextView` controle adicionado a um `RelativeLayout`, conforme mostrado no código a seguir.

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Esse código cria uma instância de um `RelativeLayout` classe e define seu `LayoutParameters` propriedade. O `LayoutParams` classe é a forma do Android de encapsular a como os controles são posicionados de forma reutilizável. Depois de criar uma instância de um layout, controles podem ser criados e adicionados a ele. Controles também têm `LayoutParameters`, como o `TextView` neste exemplo. Após o `TextView` é criado, adicioná-lo para o `RelativeLayout` e configuração o `RelativeLayout` como os resultados de exibição de conteúdo no aplicativo exibindo a `TextView` conforme mostrado:

[![Botão de contador de incremento mostrado nos modos retrato e paisagem](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Detectando orientação no código

Se um aplicativo tenta carregar uma interface de usuário diferente para cada orientação quando `OnCreate` é chamado (Isso ocorrerá sempre que um dispositivo é girado), ela deve detectar a orientação e, em seguida, carregar o código de interface do usuário desejado. O Android tem uma classe chamada a `WindowManager`, que pode ser usado para determinar a rotação do dispositivo atual por meio de `WindowManager.DefaultDisplay.Rotation` propriedade, conforme mostrado abaixo:

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

Esse código define o `TextView` ser posicionadas 100 pixels da parte superior esquerda da tela, animando automaticamente para o novo layout, quando girado para paisagem, conforme mostrado aqui:

[![Estado de exibição é preservado entre os modos retrato e paisagem](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Impedindo a reinicialização de atividade

Além de lidar com tudo, em `OnCreate`, um aplicativo também pode impedir que uma atividade que está sendo reiniciado quando a orientação é alterada, definindo `ConfigurationChanges` no `ActivityAttribute` da seguinte maneira:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Agora quando o dispositivo for girado, a atividade não será reiniciada. Para tratar manualmente a alteração de orientação nesse caso, uma atividade pode substituir a `OnConfigurationChanged` método e determinar a orientação do `Configuration` objeto que é passado, como a nova implementação da atividade abaixo:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

Aqui o `TextView's` parâmetros de layout são inicializados para retrato e paisagem. Variáveis de classe manter os parâmetros, juntamente com o `TextView` em si, uma vez que a atividade será não ser recriada quando alterações de orientação. O código ainda usa o `surfaceOrientartion` na `OnCreate` para definir o layout inicial para o `TextView`. Depois disso, `OnConfigurationChanged` manipula todas as alterações de layout subsequentes.

Quando executamos o aplicativo, o Android carrega as alterações de interface do usuário como rotação do dispositivo ocorre e não reinicia a atividade.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Impedindo a reinicialização de atividade para Layouts declarativas

Reinicializações de atividade causadas por rotação do dispositivo também podem ser evitadas se podemos definir o layout em XML. Por exemplo, podemos usar essa abordagem se quiser evitar uma reinicialização de atividade (por motivos de desempenho, talvez) e não precisamos carregar novos recursos para diferentes orientações.

Para fazer isso, vamos seguir o mesmo procedimento que usamos com um layout programático. Basta definir `ConfigurationChanges` no `ActivityAttribute`, como fizemos no `CodeLayoutActivity` anteriormente. Qualquer código que precisam ser executados para a alteração de orientação novamente pode ser implementada de `OnConfigurationChanged` método.


## <a name="maintaining-state-during-orientation-changes"></a>Mantendo o estado durante alterações de orientação

Se o tratamento de rotação de forma declarativa ou programaticamente, todos os aplicativos Android devem implementar as mesmas técnicas para gerenciar o estado quando a orientação do dispositivo muda. Gerenciamento de estado é importante porque o sistema reinicia uma atividade em execução quando um dispositivo Android é girado. Android faz isso para que seja fácil para carregar recursos alternativos, como layouts e desenháveis que são projetados especificamente para uma orientação específica. Quando ele for reiniciado, a atividade perde qualquer estado transitório, que ele poderá ter armazenado nas variáveis de classe local. Portanto, se uma atividade é dependente de estado, ele deve manter seu estado no nível do aplicativo. Um aplicativo precisa lidar com Salvar e restaurar qualquer estado do aplicativo que deseja preservar nas alterações de orientação.

Para obter mais informações sobre persistir o estado no Android, consulte o [ciclo de vida de atividade](~/android/app-fundamentals/activity-lifecycle/index.md) guia.


## <a name="summary"></a>Resumo

Este artigo abordou como usar recursos internos do Android para trabalhar com a rotação. Primeiro, ele explicou como usar o sistema de recurso do Android para criar aplicativos com reconhecimento de orientação. Em seguida, apresentado como adicionar controles no código, bem como lidar com alterações de orientação manualmente.



## <a name="related-links"></a>Links relacionados

- [Demonstração de rotação (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Tratando alterações de tempo de execução](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Alteração de orientação de tela rápida](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
