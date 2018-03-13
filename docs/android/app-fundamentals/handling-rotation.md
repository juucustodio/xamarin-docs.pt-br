---
title: "Tratamento de rotação"
description: "Este tópico descreve como manipular as alterações de orientação do dispositivo em xamarin. Ele aborda como trabalhar com o sistema de recursos do Android para carregar automaticamente os recursos para uma orientação de dispositivo específico, bem como manipular programaticamente a orientação é alterado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c31dbfeea3134de95f3275a7fa79c508a94d6a91
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="handling-rotation"></a>Tratamento de rotação

_Este tópico descreve como manipular as alterações de orientação do dispositivo em xamarin. Ele aborda como trabalhar com o sistema de recursos do Android para carregar automaticamente os recursos para uma orientação de dispositivo específico, bem como manipular programaticamente a orientação é alterado._


## <a name="overview"></a>Visão geral

Como os dispositivos móveis são girados facilmente, rotação interna é um recurso padrão em sistemas operacionais móveis. Android fornece uma estrutura de sofisticada para lidar com rotação em aplicativos, se a interface do usuário é criada declarativamente em XML ou por meio de programação em código. Ao tratar automaticamente as alterações de layout declarativa em um dispositivo girada, um aplicativo pode aproveitar a integração com o sistema de recurso Android. Layout de programação, as alterações devem ser tratadas manualmente. Isso permite um controle mais preciso no tempo de execução, mas às custas de mais trabalho para o desenvolvedor. Um aplicativo também pode optar por recusar a reinicialização de atividade e assumir o controle manual das alterações de orientação.

Este guia examina os seguintes tópicos de orientação:

-   **Rotação de Layout declarativa** &ndash; como usar o sistema Android recursos para criar aplicativos com reconhecimento de orientação, incluindo como carregar layouts e drawables para orientações específicas.

-   **Rotação de Layout programático** &ndash; como adicionar controles programaticamente, bem como tratar alterações de orientação manualmente.


## <a name="handling-rotation-declaratively-with-layouts"></a>Rotação de tratamento de forma declarativa com Layouts

Incluindo arquivos em pastas que seguem as convenções de nomenclatura, Android carrega automaticamente os arquivos apropriados quando altera a orientação.
Isso inclui suporte para:

-   *Recursos de layout* &ndash; especificando quais arquivos de layout são aumentados para cada orientação.

-   *Recursos drawable* &ndash; especificando quais drawables são carregados para cada orientação.


### <a name="layout-resources"></a>Recursos de layout

Por padrão, os arquivos XML Android (AXML) incluídos no **recursos/layout** pasta são usados para modos de exibição de renderização para uma atividade. Os recursos desta pasta são usados para orientação de retrato e paisagem se não há recursos de layout adicionais são fornecidos especificamente para o cenário. Considere a estrutura de projeto criada pelo modelo de projeto padrão:

[![Estrutura do modelo de projeto padrão](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Esse projeto cria um único **Main.axml** arquivo o **recursos/layout** pasta. Quando a atividade `OnCreate` método é chamado, ele inflação de exibição definida no **Main.axml,** que declara um botão, conforme mostrado no XML abaixo:

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

Se o dispositivo for girado para orientação de paisagem, a atividade do `OnCreate` método é chamado novamente e o mesmo **Main.axml** arquivo é inflado, conforme mostrado na captura de tela abaixo:

[![Mesma tela mas na orientação paisagem](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>Layouts de orientação específica

Além da pasta de layout (que o padrão é retrato e também pode ser chamada explicitamente *layout porta* , incluindo uma pasta chamada `layout-land`), um aplicativo pode definir os modos de exibição necessários no cenário sem qualquer código alterações.

Suponha que o **Main.axml** arquivo continha XML a seguir:

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

Se uma pasta nomeada Terra de layout que contém outros **Main.axml** arquivo é adicionado ao projeto, aumentando o layout no cenário agora resulta em Android Carregando recém-adicionado **Main.axml.** Considere a versão do cenário do **Main.axml** arquivo que contém o código a seguir (para simplificar, esse XML é semelhante para a versão de retrato padrão do código, mas usa uma cadeia de caracteres diferente no `TextView`):

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


### <a name="drawable-resources"></a>Recursos drawable

Durante a rotação, Android trata drawable recursos de maneira semelhante aos recursos de layout. Nesse caso, o sistema obtém drawables do **recursos/drawable** e **recursos/drawable-Terra** pastas, respectivamente.

Por exemplo, digamos que o projeto inclui uma imagem chamada Monkey.png no **recursos/drawable** pasta, onde o drawable referenciado a partir um `ImageView` no XML como esta:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Vamos supor que a mais uma versão diferente do **Monkey.png** está incluída em **recursos/drawable-Terra**. Assim como com os arquivos de layout, quando o dispositivo for girado, as alterações drawable para a orientação de determinado, conforme mostrado abaixo:

[![Versão diferente de Monkey.png mostrados nos modos retrato e paisagem](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>Tratamento de rotação programaticamente

Às vezes, podemos definir layouts no código. Isso pode ocorrer por várias razões, incluindo as limitações técnicas, preferência de desenvolvedor, etc. Quando adicionamos controles programaticamente, um aplicativo deve considerar manualmente para orientação do dispositivo, que é tratada automaticamente quando usamos os recursos XML.


### <a name="adding-controls-in-code"></a>Adicionando controles em código

Para adicionar controles programaticamente, um aplicativo precisa executar as seguintes etapas:

-  Crie um layout.
-  Definir parâmetros de layout.
-  Crie controles.
-  Definir parâmetros de controle de layout.
-  Adicione controles ao layout.
-  Defina o layout da exibição de conteúdo.

Por exemplo, considere uma interface do usuário consiste em um único `TextView` controle adicionado a um `RelativeLayout`, conforme mostrado no código a seguir.

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

Esse código cria uma instância de um `RelativeLayout` classe e define seu `LayoutParameters` propriedade. O `LayoutParams` classe é a forma do Android do encapsulamento de como os controles são posicionados de forma reutilizável. Depois de criar uma instância de um layout, controles podem ser criados e adicionados a ela. Controles também têm `LayoutParameters`, como o `TextView` neste exemplo. Após o `TextView` é criado, adicioná-lo para o `RelativeLayout` e configuração o `RelativeLayout` como os resultados da exibição de conteúdo na exibição de aplicativo a `TextView` conforme mostrado:

[![Botão de contador de incremento mostrado nos modos retrato e paisagem](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>Detectando a orientação no código

Se um aplicativo tentar carregar uma interface de usuário diferente para cada orientação quando `OnCreate` é chamado (Isso ocorrerá sempre que um dispositivo for girado), ele deve detectar a orientação e, em seguida, carregar o código de interface do usuário desejado. Android tem uma classe chamada a `WindowManager`, que pode ser usado para determinar a rotação do dispositivo atual por meio de `WindowManager.DefaultDisplay.Rotation` propriedade, conforme mostrado abaixo:

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

Esse código define o `TextView` para ser posicionadas 100 pixels da parte superior esquerda da tela, animação automaticamente para o novo layout, quando girado para paisagem, conforme mostrado aqui:

[![Estado de exibição é preservado em modos retrato e paisagem](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>Impedindo a reinicialização de atividade

Além de controlar tudo no `OnCreate`, um aplicativo também pode impedir que uma atividade que está sendo reiniciado quando altera a orientação, definindo `ConfigurationChanges` no `ActivityAttribute` da seguinte maneira:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
Agora quando o dispositivo for girado, a atividade não for reiniciada. Para tratar manualmente a alteração da orientação nesse caso, uma atividade pode substituir o `OnConfigurationChanged` método e determinar a orientação do `Configuration` objeto que é transmitido, como a nova implementação da atividade abaixo:

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

Aqui o `TextView's` parâmetros de layout são inicializados para retrato e paisagem. Variáveis de classe mantenha os parâmetros, juntamente com o `TextView` em si, desde que a atividade não será novamente criada quando a orientação é alterado. O código ainda usa o `surfaceOrientartion` em `OnCreate` para definir o layout inicial para o `TextView`. Depois disso, `OnConfigurationChanged` trata todas as alterações de layout subsequentes.

Quando o aplicativo é executado, o Android carrega as alterações de interface do usuário como rotação de dispositivos ocorre e reinicia a atividade.


## <a name="preventing-activity-restart-for-declarative-layouts"></a>Impedindo a reinicialização de atividade para Layouts declarativas

Reinicializações de atividade resultantes de rotação de dispositivos também podem ser evitadas se definimos o layout em XML. Por exemplo, podemos usar essa abordagem para evitar uma reinicialização de atividade (por motivos de desempenho, talvez) e não precisamos carregar novos recursos para orientações diferentes.

Para fazer isso, podemos siga o mesmo procedimento que usamos com um layout de programação. Basta definir `ConfigurationChanges` no `ActivityAttribute`, como foi o `CodeLayoutActivity` anteriormente. Qualquer código que precisam ser executados para a alteração da orientação novamente pode ser implementada no `OnConfigurationChanged` método.


## <a name="maintaining-state-during-orientation-changes"></a>Manter estado durante alterações de orientação

Se tratar de rotação declarativamente ou por meio de programação, todos os aplicativos Android devem implementar as mesmas técnicas para gerenciar o estado quando altera a orientação do dispositivo. Gerenciamento de estado é importante porque o sistema reinicia uma atividade em execução quando um dispositivo Android for girado. Android faz isso para que seja fácil carregar recursos alternativos, como layouts e drawables projetados especificamente para uma orientação específica. Quando ele for reiniciado, a atividade perde qualquer estado transitório, que ele pode ter armazenados em variáveis de classe local. Portanto, se uma atividade é dependente do estado, ele deve manter seu estado no nível do aplicativo. Um aplicativo precisa tratar salvar e restaurar qualquer estado de aplicativo que deseja preservar nas alterações de orientação.

Para obter mais informações sobre o estado persistente no Android, consulte o [ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md) guia.


## <a name="summary"></a>Resumo

Este artigo abordou como usar recursos internos do Android para trabalhar com a rotação. Primeiro, ele explicou como usar o sistema Android recursos para criar aplicativos com reconhecimento de orientação. Em seguida, apresentadas como adicionar controles no código, bem como tratar alterações de orientação manualmente.



## <a name="related-links"></a>Links relacionados

- [Demonstração de rotação (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Tratando alterações de tempo de execução](http://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Alteração de orientação da tela rápida](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
