---
title: Criando uma Face do relógio
description: Este guia explica como implementar um serviço de face de inspecionar personalizado para desgaste Android. São fornecidas instruções passo a passo para a criação de um extraído para o serviço de face inspecionar digital, seguido por mais de código para criar uma face do relógio de estilo analógico.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: c02755cc3ff5b46a5a97b6c14185794d8ad538d8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772933"
---
# <a name="creating-a-watch-face"></a>Criando uma Face do relógio

_Este guia explica como implementar um serviço de face de inspecionar personalizado para desgaste Android. São fornecidas instruções passo a passo para a criação de um extraído para o serviço de face inspecionar digital, seguido por mais de código para criar uma face do relógio de estilo analógico._

## <a name="overview"></a>Visão geral 

Neste passo a passo, um serviço de face de inspecionar básico é criado para ilustrar os conceitos básicos de criar uma personalizado face do relógio desgaste Android. O serviço de face de inspeção inicial exibe uma inspeção digital simple que exibe a hora atual em horas e minutos: 

[![Face relógio digital](creating-a-watchface-images/01-initial-face.png "captura de tela de exemplo da face do relógio digital inicial")](creating-a-watchface-images/01-initial-face.png#lightbox)

Depois que essa face do relógio digital é desenvolvido e testado, mais código é adicionado para atualizá-lo para uma mais sofisticado face de relógio analógico com três mãos: 

[![Face relógio analógico](creating-a-watchface-images/02-example-watchface.png "captura de tela de exemplo da face do relógio analógico final")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Assista a face serviços são agrupados e instalados como parte de um aplicativo de desgaste. Nos exemplos a seguir, `MainActivity` contém nada mais do que o código do modelo de aplicativo de desgaste para que o serviço de face inspecionar pode ser empacotado e implantado para o relógio inteligente como parte do aplicativo. Na verdade, este aplicativo servirá puramente um veículo para obter o serviço de face de inspecionar carregado no dispositivo de desgaste (ou no emulador do Windows) para depuração e teste. 

## <a name="requirements"></a>Requisitos

Para implementar um serviço de face watch, é necessário o seguinte:

-   Android 5.0 (API nível 21) ou superior no desgaste dispositivo ou emulador.

-   O [Xamarin Android desgaste suporte bibliotecas](https://www.nuget.org/packages/Xamarin.Android.Wear) devem ser adicionados ao projeto xamarin. 

Embora o Android 5.0 é a API mínima nível para implementar um serviço de face de observação, Android 5.1 ou posterior é recomendado. Android use dispositivos que executam o Android 5.1 (API 22) ou superior permitir que os aplicativos de desgaste controlar o que é exibido na tela enquanto o dispositivo estiver em baixa energia *ambiente* modo. Quando o dispositivo deixa baixa energia *ambiente* modo, ele está em *interativo* modo. Para obter mais informações sobre esses modos, consulte [manter seu aplicativo visível](https://developer.android.com/training/wearables/apps/always-on.html). 


## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto de desgaste Android chamado **WatchFace** (para obter mais informações sobre como criar novos projetos de xamarin, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Caixa de diálogo Novo projeto](creating-a-watchface-images/03-wear-project-vs-sml.png "Selecionar aplicativo desempenham na caixa de diálogo Novo projeto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Caixa de diálogo Novo projeto](creating-a-watchface-images/03-wear-project-xs-sml.png "Selecionar aplicativo desempenham na caixa de diálogo Novo projeto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Defina o nome do pacote para `com.xamarin.watchface`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Configuração do nome do pacote](creating-a-watchface-images/04-package-name-vs.png "definido o nome do pacote como com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Configuração do nome do pacote](creating-a-watchface-images/04-package-name-xs.png "definido o nome do pacote como com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Além disso, role para baixo e habilite o **INTERNET** e **WAKE_LOCK** permissões: 

[![As permissões necessárias](creating-a-watchface-images/05-required-permissions-vs.png "permissões Enable INTERNET e WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Defina a versão do Android mínimo **5.1 Android (API nível 22)**. Além disso, habilite o **Internet** e **WakeLock** permissões:

[![As permissões necessárias](creating-a-watchface-images/05-required-permissions-xs.png "permissões Enable Internet e WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Em seguida, baixar [preview.png](creating-a-watchface-images/preview.png) &ndash; será adicionada para o **drawables** pasta posteriormente neste passo a passo.


## <a name="add-the-xamarin-android-wear-package"></a>Adicionar o pacote de desgaste Xamarin Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Iniciar o Gerenciador de pacotes do NuGet (no Visual Studio, clique com botão direito **referências** no **Solution Explorer** e selecione **gerenciar pacotes NuGet …** ). Atualize o projeto para a versão estável mais recente do **Xamarin.Android.Wear**: 

[![Adicionar Gerenciador de pacotes do NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "adicionar o pacote de Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Em seguida, se **Xamarin.Android.Support.v13** é instalado, desinstale-o:

[![Remover Gerenciador de pacotes do NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Xamarin.Support.v13 remover")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Iniciar o Gerenciador de pacotes do NuGet (no Visual Studio para Mac, clique com botão direito **pacotes** no **solução painel** e selecione **adicionar pacotes de...** ). Atualize o projeto para a versão estável mais recente do **Xamarin.Android.Wear**: 

[![Adicionar Gerenciador de pacotes do NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "adicionar o pacote de Xamarin.Android.Wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Compilar e executar o aplicativo em um dispositivo de desgaste ou emulador (para obter mais informações sobre como fazer isso, consulte o [Introdução](~/android/wear/get-started/index.md) guia). Você verá a tela a seguir aplicativo no dispositivo desgaste:

[![Captura de tela do aplicativo](creating-a-watchface-images/08-app-screen.png "tela do aplicativo no dispositivo de desgaste")](creating-a-watchface-images/08-app-screen.png#lightbox)

Neste ponto, o aplicativo de desgaste básico não tem funcionalidade de face de inspecionar porque ele não fornece uma implementação de serviço de face de inspeção. Esse serviço será adicionado ao lado. 

 
## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Implementa de desgaste Android Assista faces por meio de `CanvasWatchFaceService` classe. `CanvasWatchFaceService` é derivado de `WatchFaceService`, que é derivada de `WallpaperService` conforme mostrado no diagrama a seguir: 

[![Diagrama de herança](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService diagrama de herança")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` inclui um aninhada `CanvasWatchFaceService.Engine`; ele instancia um `CanvasWatchFaceService.Engine` objeto que faz o trabalho real de desenho face do relógio. `CanvasWatchFaceService.Engine` é derivado de `WallpaperService.Engine` conforme mostrado no diagrama acima. 

Não mostrado neste diagrama é um `Canvas` que `CanvasWatchFaceService` usa para desenhar a face do relógio &ndash; isso `Canvas` é passado por meio de `OnDraw` método conforme descrito abaixo. 

Nas seções a seguir, um serviço de face inspecionar personalizado será criado seguindo estas etapas: 

1.  Definir uma classe chamada `MyWatchFaceService` que é derivado de `CanvasWatchFaceService`. 

2.  Dentro de `MyWatchFaceService`, crie uma classe aninhada chamada `MyWatchFaceEngine` que é derivado de `CanvasWatchFaceService.Engine`. 

3.  Em `MyWatchFaceService`, implementar um `CreateEngine` método que instancia `MyWatchFaceEngine` e o retorna.

4.  Em `MyWatchFaceEngine`, implemente o `OnCreate` método para criar o estilo de face de inspecionar e executar outras tarefas de inicialização. 

5.  Implementar o `OnDraw` método `MyWatchFaceEngine`. Este método é chamado sempre que a face do relógio precisa ser redesenhado (ou seja, *invalidado*). `OnDraw` é o método que desenha (e redesenha) inspecionar face elementos, como hora, minuto e segundo mãos. 

6.  Implementar o `OnTimeTick` método `MyWatchFaceEngine`. 
    `OnTimeTick` é chamado pelo menos uma vez por minuto (em modos interativos e de ambiente) ou quando a data/hora foi alterado. 

Para obter mais informações sobre `CanvasWatchFaceService`, consulte o Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) documentação da API.
Da mesma forma, [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explica a implementação real da face do relógio.


### <a name="add-the-canvaswatchfaceservice"></a>Adicionar o CanvasWatchFaceService

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Adicionar um novo arquivo chamado **MyWatchFaceService.cs** (no Visual Studio, clique com botão direito **WatchFace** no **Solution Explorer**, clique em **Adicionar > Novo Item...** e selecione **classe**).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Adicionar um novo arquivo chamado **MyWatchFaceService.cs** (no Visual Studio para Mac, clique com botão direito do **WatchFace** de projeto, clique em **Adicionar > novo arquivo...** e selecione **classe vazia**). 

-----

Substitua o conteúdo deste arquivo com o código a seguir: 

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (derivado de `CanvasWatchFaceService`) é o "programa principal" de face do relógio. `MyWatchFaceService` implementa apenas um método, `OnCreateEngine`, que cria e retorna um `MyWatchFaceEngine` objeto (`MyWatchFaceEngine` é derivado de `CanvasWatchFaceService.Engine`). O instanciado `MyWatchFaceEngine` objeto deve ser retornado como um `WallpaperService.Engine`. O encapsulamento `MyWatchFaceService` objeto é passado para o construtor. 

`MyWatchFaceEngine` é a implementação de face de inspeção real &ndash; contém o código que chama a face do relógio. Ele também trata os eventos de sistema, como alterações de tela (modos de ambiente interativo, tela desligar, etc.). 

 
### <a name="implement-the-engine-oncreate-method"></a>Implementar o método do mecanismo de OnCreate

O `OnCreate` método inicializa a face do relógio. Adicione o seguinte campo para `MyWatchFaceEngine`: 

```csharp
Paint hoursPaint;
```

Isso `Paint` objeto será usado para desenhar a hora atual na face do relógio. Em seguida, adicione o seguinte método à `MyWatchFaceEngine`: 

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` é chamado logo após `MyWatchFaceEngine` é iniciado. Define o `WatchFaceStyle` (que controla como o dispositivo de desgaste interage com o usuário) e instancia o `Paint` objeto que será usado para exibir a hora. 

A chamada para `SetWatchFaceStyle` faz o seguinte:

1.  Conjuntos de *modo espiada* para `PeekModeShort`, que faz com que as notificações aparecem como cartões pequeno "peek" na tela. 

2.  Define a visibilidade do plano de fundo `Interruptive`, que faz com que o plano de fundo de um cartão de pico para ser mostrada apenas rapidamente se ele representa uma notificação semelhantes.

3.  Desabilita a hora de interface do usuário do sistema padrão seja desenhado na face do relógio para que a face do relógio personalizados pode exibir a hora em vez disso.

Para obter mais informações sobre essas e outras opções de estilo de face de inspeção de variáveis, consulte o Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) documentação da API.

Depois de `SetWatchFaceStyle` for concluído, `OnCreate` instancia o `Paint` objeto (`hoursPaint`) e define sua cor para branco e o tamanho do texto para 48 pixels ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) deve ser especificado em pixels). 


### <a name="implement-the-engine-ondraw-method"></a>Implementar o método OnDraw do mecanismo

O `OnDraw` método talvez seja mais importante `CanvasWatchFaceService.Engine` método &ndash; é o método que realmente desenhos Assista a elementos de face como dígitos e ponteiros de face do relógio. No exemplo a seguir baseia-se uma cadeia de caracteres de hora na face do relógio.
Adicione o seguinte método para `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str, 
        (float)(frame.Left + 70), 
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quando chama Android `OnDraw`, ele passa um `Canvas` instância e os limites em que pode ser desenhada face. No exemplo de código acima, `DateTime` é usada para calcular o tempo atual em horas e minutos (no formato de 12 horas). A cadeia de caracteres de hora resultante é desenhada na tela usando o `Canvas.DrawText` método. A cadeia de caracteres aparecerá 70 pixels sobre a borda esquerda e 80 pixels para baixo da borda superior. 

Para obter mais informações sobre o `OnDraw` método, consulte o Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html#onDraw(android.graphics.Canvas, android.graphics.Rect)) documentação da API.


### <a name="implement-the-engine-ontimetick-method"></a>Implementar o método OnTimeTick do mecanismo

Android periodicamente chama o `OnTimeTick` para atualizar o tempo mostrado pela face do relógio. Ele é chamado em pelo menos uma vez por minuto (em modos interativos e de ambiente), ou quando a data/hora ou fuso horário foram alterados. Adicione o seguinte método para `MyWatchFaceEngine`: 

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Essa implementação de `OnTimeTick` simplesmente chama `Invalidate`. O `Invalidate` agendas de método `OnDraw` redesenhar face do relógio. 

Para obter mais informações sobre o `OnTimeTick` método, consulte o Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) documentação da API.

 
## <a name="register-the-canvaswatchfaceservice"></a>Registrar o CanvasWatchFaceService

`MyWatchFaceService` deve ser registrado no **AndroidManifest.xml** do aplicativo desgaste associado. Para fazer isso, adicione o seguinte XML para o `<application>` seção: 

```xml
<service 
    android:name="watchface.MyWatchFaceService" 
    android:label="Xamarin Sample" 
    android:allowEmbedded="true" 
    android:taskAffinity="" 
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data 
        android:name="android.service.wallpaper" 
        android:resource="@xml/watch_face" />
    <meta-data 
        android:name="com.google.android.wearable.watchface.preview" 
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

Esse XML faz o seguinte:

1.  Conjuntos de `android.permission.BIND_WALLPAPER` permissão. Essa permissão concede a permissão de serviço de face de inspeção para alterar o papel de parede do sistema no dispositivo. Observe que essa permissão deve ser definida na `<service>` seção em vez de em externa `<application>` seção. 

2.  Define um `watch_face` recursos. Esse recurso é um arquivo XML curto que declara um `wallpaper` recurso (este arquivo será criado na próxima seção). 

3.  Declara uma imagem drawable chamada `preview` que será exibida a tela de seleção de seletor de inspeção. 

4.  Inclui um `intent-filter` informar Android que `MyWatchFaceSevice` exibirá uma face do relógio. 

Isso conclui o código para o básico `WatchFace` exemplo. A próxima etapa é adicionar os recursos necessários.

 
## <a name="add-resource-files"></a>Adicionar arquivos de recurso

Antes de executar o serviço de inspeção, você deve adicionar o **watch_face** recurso e a imagem de visualização. Primeiro, crie um novo arquivo XML em **Resources/xml/watch_face.xml** e substitua o seu conteúdo com o seguinte XML: 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Definir ação de compilação deste arquivo **AndroidResource**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ação de compilação](creating-a-watchface-images/10-android-resource-vs.png "conjunto de compilação para AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ação de compilação](creating-a-watchface-images/10-android-resource-xs.png "conjunto de compilação para AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Este arquivo de recurso define uma simples `wallpaper` elemento que será usado para face do relógio. 

Se você não ainda tiver feito isso, baixe [preview.png](creating-a-watchface-images/preview.png). Instalá-lo em **Resources/drawable/preview.png**. Certifique-se de adicionar esse arquivo para o `WatchFace` projeto. Esta imagem de visualização é exibida para o usuário no seletor de face de inspeção do dispositivo de desgaste. Para criar uma imagem de visualização para seu próprio face do relógio, você pode tirar uma captura de tela de face do relógio enquanto ele está em execução. (Para obter mais informações sobre como obter capturas de tela de desgaste de dispositivos, consulte [fazer capturas de tela](~/android/wear/deploy-test/debug-on-device.md#screenshots)). 


## <a name="try-it"></a>Experimente!

Criar e implantar o aplicativo no dispositivo de desgaste. Você deverá ver a tela do aplicativo desgaste como antes. Faça o seguinte para habilitar a face do relógio novo: 

1.  Passe o dedo para a direita até ver o plano de fundo da tela de observação. 

2.  Toque e mantenha-se em qualquer lugar no plano de fundo da tela de dois segundos.

3.  Passe o dedo da esquerda para a direita para navegar por meio de várias faces inspecionar. 

4.  Selecione o **Xamarin exemplo** Assista a face (mostrado no lado direito): 

    [![Seletor de Watchface](creating-a-watchface-images/11-watchface-picker.png "passe o dedo para localizar a face do relógio de exemplo do Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Toque na **Xamarin exemplo** Assista a face para selecioná-la. 

Isso altera a face do relógio do dispositivo desgaste para usar o serviço de face de inspecionar personalizado implementado até agora: 

[![Face relógio digital](creating-a-watchface-images/12-digital-watchface.png "inspecionar digital personalizada, em execução no dispositivo de desgaste")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Isso é uma face do relógio relativamente crua porque a implementação do aplicativo é então mínima (por exemplo, ele não inclui um plano de fundo de face de inspeção e não chama `Paint` métodos de suavização para melhorar a aparência). No entanto, ele implementa a funcionalidade básica que é necessária para criar uma face do relógio personalizado. 

Na próxima seção, esta face do relógio será atualizado para uma implementação mais sofisticada. 

 
## <a name="upgrading-the-watch-face"></a>Atualizando a Face do relógio

No restante deste passo a passo, `MyWatchFaceService` é atualizado para exibir uma face do relógio de estilo analógico e é estendido para dar suporte a mais recursos. Os seguintes recursos serão adicionados ao criar face do relógio atualizado: 

1.  Indica o tempo com analógica hora, minuto e segundo mãos.

2.  Reage a alterações na visibilidade.

3.  Responde a alterações entre o modo de ambiente e o modo interativo. 

4.  Lê as propriedades do dispositivo desgaste subjacente.

5.  Atualiza automaticamente o tempo quando ocorre uma alteração de fuso horário. 

Antes de implementar as alterações de código abaixo, baixar [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), descompacte-o e mover os arquivos. PNG descompactado **recursos/drawable** (substituir anterior **preview.png**). Adicionar os novos arquivos. png para o `WatchFace` projeto.


### <a name="update-engine-features"></a>Recursos do mecanismo de atualização

A próxima etapa é atualização **MyWatchFaceService.cs** para uma implementação que desenha uma face do relógio analógico e dá suporte a novos recursos. Substitua o conteúdo do **MyWatchFaceService.cs** com a versão analógica do código de face de inspeção em [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (recorte e cole a essa fonte existente  **MyWatchFaceService.cs**). 

Esta versão do **MyWatchFaceService.cs** adiciona mais código para os métodos existentes e inclui métodos substituídos adicionais para adicionar mais funcionalidade. As seções a seguir fornecem um tour guiado do código-fonte.

#### <a name="oncreate"></a>OnCreate

A atualização **OnCreate** método define o estilo de face de observar como antes, mas inclui algumas etapas adicionais: 

1.  Define a imagem de plano de fundo o **xamarin_background** recurso reside no **Resources/drawable-hdpi/xamarin_background.png**. 

2.  Inicializa `Paint` objetos para desenhar as horas, minutos e segundo disponível.

3.  Inicializa uma `Paint` objeto para desenhar os tiques de hora em torno da borda da face do relógio. 

4.  Cria um temporizador que chama o `Invalidate` método (redesenhar) para que a mão de segundo será redesenhada cada segundo. Observe que esse tempo é necessário porque `OnTimeTick` chamadas `Invalidate` apenas uma vez a cada minuto.

Este exemplo inclui apenas uma **xamarin_background.png** imagem; no entanto, você talvez queira criar uma imagem de plano de fundo diferente para cada densidade da tela que darão suporte a face do relógio personalizado. 

#### <a name="ondraw"></a>OnDraw

A atualização **OnDraw** método desenha uma face de inspeção de estilo analógico usando as seguintes etapas: 

1.  Obtém a hora atual, que agora é mantida em um `time` objeto. 

2.  Determina os limites de seu centro e a superfície de desenho.

3.  Desenha o plano de fundo dimensionado para ajustar o dispositivo quando o plano de fundo é desenhado.

4.  Desenha doze *tiques* em torno de face do relógio (correspondente ao horário de na face do relógio). 

5.  Calcula o ângulo, rotação e comprimento de cada ponteiro inspeção.

6.  Desenha cada mão na superfície de inspeção. Observe que a mão segundo não é desenhada se o relógio está no modo de ambiente. 

 
#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Este método é chamado para informar `MyWatchFaceEngine` sobre as propriedades do dispositivo desgaste (como modo de ambiente de baixo bits e proteção de gravação). Em `MyWatchFaceEngine`, esse método verifica apenas para baixo de bits de modo de ambiente (no modo de ambiente bits baixa, a tela dá suporte a menos bits para cada cor). 

Para obter mais informações sobre esse método, consulte o Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) documentação da API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Esse método é chamado quando o dispositivo de desgaste entra ou sai do modo de ambiente. No `MyWatchFaceEngine` implementação, face do relógio desabilita a suavização quando ele estiver no modo de ambiente. 

Para obter mais informações sobre esse método, consulte o Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) documentação da API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Este método é chamado sempre que o relógio se torna visível ou oculto. Em `MyWatchFaceEngine`, este método registra/cancela o registro do receptor de fuso horário (descrito abaixo) de acordo com o estado de visibilidade. 

Para obter mais informações sobre esse método, consulte o Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) documentação da API.

 
### <a name="time-zone-feature"></a>Recurso de fuso horário

O novo **MyWatchFaceService.cs** também inclui a funcionalidade para atualizar a hora atual, sempre que o tempo de alterações (por exemplo, enquanto estiver viajando entre fusos horários) da zona. Perto do final da **MyWatchFaceService.cs**, uma alteração de zona de tempo `BroadcastReceiver` é definida que manipula objetos intenção alterado de fuso horário: 

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

O `RegisterTimezoneReceiver` e `UnregisterTimezoneReceiver` métodos são chamados pelo `OnVisibilityChanged` método. 
`UnregisterTimezoneReceiver` é chamado de quando o estado de visibilidade da face do relógio é alterado para oculto. Quando a face do relógio é visível novamente, `RegisterTimezoneReceiver` é chamado (consulte o `OnVisibilityChanged` método). 

O mecanismo `RegisterTimezoneReceiver` método declara um manipulador para esse destinatário de fuso horário `Receive` evento; esse manipulador atualiza o `time` objeto com a nova hora sempre que um fuso horário é cruzado: 

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Um filtro a intenção é criado e registrado para o receptor de fuso horário:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

O `UnregisterTimezoneReceiver` método cancela o registro do receptor de fuso horário:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Execute a Face do relógio aprimorado

Criar e implantar o aplicativo no dispositivo de desgaste novamente. Selecione a face do relógio inspecionar face do seletor de como antes. A visualização no seletor de inspeção é exibida à esquerda e face do relógio novo é exibido à direita:

[![Face relógio analógico](creating-a-watchface-images/13-analog-watchface.png "aprimorado face analógico no seletor e no dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

Nesta captura de tela, o ponteiro do segundo está movendo uma vez por segundo. Quando você executa esse código em um dispositivo de desgaste, segundos desaparece quando o relógio entra em modo de ambiente.

 
## <a name="summary"></a>Resumo

Neste passo a passo, um watchface personalizada do Android desgaste foi implementado e testado. O `CanvasWatchFaceService` e `CanvasWatchFaceService.Engine` classes foram introduzidas, e os métodos essenciais da classe mecanismo foram implementados para criar uma face do relógio de digital simples. Essa implementação foi atualizada com mais funcionalidade para criar uma face do relógio analógico e métodos adicionais foram implementados para tratar alterações na visibilidade, modo de ambiente e as diferenças nas propriedades do dispositivo. Por fim, um receptor de difusão de fuso horário foi implementado para que o relógio automaticamente atualiza a hora quando um fuso horário é cruzado. 


## <a name="related-links"></a>Links relacionados

- [Criando as Faces de inspeção](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Exemplo de WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
