---
title: Tratamento de rotação
description: Este tópico descreve como tratar as alterações de orientação do dispositivo no Xamarin. Android. Ele aborda como trabalhar com o sistema de recursos do Android para carregar automaticamente os recursos para uma determinada orientação do dispositivo, bem como para manipular alterações de orientação de forma programática.
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 944452160ed023f47fc2c648f092032c245350fb
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455659"
---
# <a name="handling-rotation"></a>Tratamento de rotação

_Este tópico descreve como tratar as alterações de orientação do dispositivo no Xamarin. Android. Ele aborda como trabalhar com o sistema de recursos do Android para carregar automaticamente os recursos para uma determinada orientação do dispositivo, bem como para manipular alterações de orientação de forma programática._

## <a name="overview"></a>Visão geral

Como os dispositivos móveis são facilmente girados, a rotação interna é um recurso padrão em sistemas operacionais móveis. O Android fornece uma estrutura sofisticada para lidar com a rotação em aplicativos, independentemente de a interface do usuário ser criada declarativamente no XML ou programaticamente no código. Ao lidar automaticamente com alterações de layout declarativas em um dispositivo girado, um aplicativo pode se beneficiar da forte integração com o sistema de recursos do Android. Para o layout programático, as alterações devem ser manipuladas manualmente. Isso permite um controle mais preciso no tempo de execução, mas às custas de mais trabalho para o desenvolvedor. Um aplicativo também pode optar por recusar a reinicialização da atividade e assumir o controle manual das alterações de orientação.

Este guia examina os seguintes tópicos de orientação:

- Rotação de layout **declarativo** &ndash; Como usar o sistema de recursos do Android para criar aplicativos com reconhecimento de orientação, incluindo como carregar layouts e drawables para orientações específicas.

- Rotação de layout **programática** &ndash; Como adicionar controles programaticamente e como tratar as alterações de orientação manualmente.

## <a name="handling-rotation-declaratively-with-layouts"></a>Tratamento de rotação declarativamente com layouts

Ao incluir arquivos em pastas que seguem convenções de nomenclatura, o Android carrega automaticamente os arquivos apropriados quando a orientação é alterada.
Isso inclui suporte para:

- *Recursos* &ndash; de layout Especificar quais arquivos de layout são desinflados para cada orientação.

- *Recursos* &ndash; de desenho Especificar quais drawables são carregados para cada orientação.

### <a name="layout-resources"></a>Recursos de layout

Por padrão, os arquivos de XML do Android (AXML) incluídos na pasta de **recursos/layout** são usados para renderizar exibições de uma atividade. Os recursos dessa pasta serão usados para orientação Retrato e paisagem se nenhum recurso de layout adicional for fornecido especificamente para paisagem. Considere a estrutura do projeto criada pelo modelo de projeto padrão:

[![Estrutura de modelo de projeto padrão](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

Este projeto cria um único arquivo **Main. axml** na pasta **Resources/layout** . Quando o método da atividade `OnCreate` é chamado, ele define o modo de exibição definido em **Main. axml,** que declara um botão, conforme mostrado no XML abaixo:

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

Se o dispositivo for girado para a orientação paisagem, o método da atividade `OnCreate` será chamado novamente e o mesmo arquivo **Main. axml** será inalterado, conforme mostrado na captura de tela abaixo:

[![Mesma tela, mas na orientação paisagem](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)

#### <a name="orientation-specific-layouts"></a>Layouts específicos de orientação

Além da pasta de layout (que usa como padrão retrato e também pode ser nomeada explicitamente *com o layout-Port* , incluindo uma pasta chamada `layout-land` ), um aplicativo pode definir as exibições necessárias quando em paisagem sem qualquer alteração de código.

Suponha que o arquivo **Main. axml** continha o seguinte XML:

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

Se uma pasta chamada layout-Land que contém um arquivo **Main. axml** adicional for adicionada ao projeto, a replanação do layout quando em paisagem fará com que o Android carregue o **Main. axml recém-adicionado.** Considere a versão de paisagem do arquivo **Main. axml** que contém o código a seguir (para simplificar, esse XML é semelhante à versão padrão de retrato do código, mas usa uma cadeia de caracteres diferente no `TextView` ):

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

Executar esse código e girar o dispositivo de retrato para paisagem demonstra o novo carregamento de XML, conforme mostrado abaixo:

[![Capturas de tela retrato e paisagem imprimindo o modo retrato](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)

### <a name="drawable-resources"></a>Recursos de desenho

Durante a rotação, o Android trata os recursos de desenho de forma semelhante aos recursos de layout. Nesse caso, o sistema Obtém o drawables das pastas **recursos/desenháveis** e **recursos/empates** , respectivamente.

Por exemplo, digamos que o projeto inclua uma imagem chamada Monkey.png na pasta **Resources/drawáveis** , em que o empate é referenciado de um `ImageView` em XML como este:

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

Vamos supor ainda que uma versão diferente do **Monkey.png** esteja incluída em **Resources/Draw-Land**. Assim como ocorre com os arquivos de layout, quando o dispositivo é girado, as alterações desenhadas para a orientação determinada, conforme mostrado abaixo:

[![Versão diferente do Monkey.png mostrada nos modos retrato e paisagem](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)

## <a name="handling-rotation-programmatically"></a>Manipulação de rotação programaticamente

Às vezes, definimos layouts no código. Isso pode ocorrer por vários motivos, incluindo limitações técnicas, preferência do desenvolvedor, etc. Quando adicionamos controles programaticamente, um aplicativo deve considerar manualmente a orientação do dispositivo, que é manipulada automaticamente quando usamos recursos XML.

### <a name="adding-controls-in-code"></a>Adicionando controles no código

Para adicionar controles programaticamente, um aplicativo precisa executar as seguintes etapas:

- Crie um layout.
- Definir parâmetros de layout.
- Criar controles.
- Definir parâmetros de layout de controle.
- Adicione controles ao layout.
- Defina o layout como a exibição de conteúdo.

Por exemplo, considere uma interface do usuário que consiste em um único `TextView` controle adicionado a um `RelativeLayout` , conforme mostrado no código a seguir.

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

Esse código cria uma instância de uma `RelativeLayout` classe e define sua `LayoutParameters` propriedade. A `LayoutParams` classe é a maneira do Android de encapsular como os controles são posicionados de forma reutilizável. Depois que uma instância de um layout é criada, os controles podem ser criados e adicionados a ele. Os controles também têm `LayoutParameters` , como o `TextView` neste exemplo. Depois que o `TextView` for criado, adicioná-lo ao `RelativeLayout` e definir o `RelativeLayout` como a exibição de conteúdo resultará no aplicativo exibindo o `TextView` conforme mostrado:

[![Botão de contador de incremento mostrado nos modos retrato e paisagem](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)

### <a name="detecting-orientation-in-code"></a>Detectando orientação no código

Se um aplicativo tentar carregar uma interface do usuário diferente para cada orientação quando `OnCreate` for chamado (isso ocorrerá sempre que um dispositivo for girado), ele deverá detectar a orientação e, em seguida, carregar o código de interface do usuário desejado. O Android tem uma classe chamada `WindowManager` , que pode ser usada para determinar a rotação atual do dispositivo por meio da `WindowManager.DefaultDisplay.Rotation` propriedade, conforme mostrado abaixo:

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

Esse código define o `TextView` a ser posicionado 100 pixels a partir da parte superior esquerda da tela, animando automaticamente para o novo layout, quando girado para paisagem, como mostrado aqui:

[![O estado de exibição é preservado nos modos retrato e paisagem](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)

### <a name="preventing-activity-restart"></a>Impedindo a reinicialização da atividade

Além de lidar com tudo no `OnCreate` , um aplicativo também pode impedir que uma atividade seja reiniciada quando a orientação é alterada por meio da configuração da `ConfigurationChanges` `ActivityAttribute` seguinte maneira:

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```

Agora, quando o dispositivo for girado, a atividade não será reiniciada. Para tratar manualmente a alteração de orientação nesse caso, uma atividade pode substituir o `OnConfigurationChanged` método e determinar a orientação do `Configuration` objeto que é passado, como na nova implementação da atividade abaixo:

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

Aqui `TextView's` , os parâmetros de layout são inicializados para paisagem e retrato. As variáveis de classe contêm os parâmetros, juntamente com o `TextView` próprio, uma vez que a atividade não será recriada quando a orientação for alterada. O código ainda usa o `surfaceOrientartion` no `OnCreate` para definir o layout inicial para o `TextView` . Depois disso, o `OnConfigurationChanged` lida com todas as alterações de layout subsequentes.

Quando executamos o aplicativo, o Android carrega as alterações da interface do usuário à medida que a rotação do dispositivo ocorre e não reinicia a atividade.

## <a name="preventing-activity-restart-for-declarative-layouts"></a>Evitando a reinicialização da atividade para layouts declarativos

As reinicializações de atividade causadas pela rotação do dispositivo também podem ser evitadas se definirmos o layout em XML. Por exemplo, podemos usar essa abordagem se quisermos impedir uma reinicialização da atividade (por motivos de desempenho, talvez) e não precisarmos carregar novos recursos para orientações diferentes.

Para fazer isso, seguimos o mesmo procedimento que usamos com um layout programático. Basta definir `ConfigurationChanges` no `ActivityAttribute` , como fizemos no `CodeLayoutActivity` anterior. Qualquer código que precise ser executado para a alteração de orientação pode ser novamente implementado no `OnConfigurationChanged` método.

## <a name="maintaining-state-during-orientation-changes"></a>Mantendo o estado durante as alterações de orientação

Se a rotação de manipulação for declarativa ou programaticamente, todos os aplicativos Android devem implementar as mesmas técnicas para gerenciar o estado quando as alterações de orientação do dispositivo forem alteradas. O gerenciamento de estado é importante porque o sistema reinicia uma atividade em execução quando um dispositivo Android é girado. O Android faz isso para facilitar a carga de recursos alternativos, como layouts e drawables projetados especificamente para uma orientação específica. Quando ele é reiniciado, a atividade perde qualquer estado transitório que possa ter sido armazenado em variáveis de classe local. Portanto, se uma atividade é dependente de estado, ela deve persistir seu estado no nível do aplicativo. Um aplicativo precisa lidar com a gravação e a restauração de qualquer estado de aplicativo que deseja preservar nas alterações de orientação.

Para obter mais informações sobre o estado de persistência no Android, consulte o guia [do ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md) .

## <a name="summary"></a>Resumo

Este artigo abordou como usar os recursos internos do Android para trabalhar com rotação. Primeiro, ele explicou como usar o sistema de recursos do Android para criar aplicativos com reconhecimento de orientação. Em seguida, ele apresentou como adicionar controles no código, bem como tratar as alterações de orientação manualmente.

## <a name="related-links"></a>Links Relacionados

- [Demonstração de rotação (exemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-rotationdemo)
- [Ciclo de vida de atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Manipulando alterações de tempo de execução](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [Alteração da orientação da tela rápida](https://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)