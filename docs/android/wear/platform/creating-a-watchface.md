---
title: Criar uma Mostrador para Android Wear 1.0
description: Este guia explica como implementar um serviço de face de Observação personalizada para o Android Wear 1.0. São fornecidas instruções passo a passo para a criação de um stripped interromper serviço de detecção facial watch digital, seguido por mais de código para criar um estilo analógico Mostrador.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: 067a39838fbfe3f1b33ac0d30b5069366b11e407
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171996"
---
# <a name="creating-a-watch-face"></a>Como criar um mostrador de relógio

_Este guia explica como implementar um serviço de face de Observação personalizada para o Android Wear 1.0. São fornecidas instruções passo a passo para a criação de um stripped interromper serviço de detecção facial watch digital, seguido por mais de código para criar um estilo analógico Mostrador._

## <a name="overview"></a>Visão geral

Neste passo a passo, um serviço de face de inspeção básica é criado para ilustrar os conceitos básicos da criação de uma Mostrador do relógio Android Wear 1.0 personalizada.
O serviço de detecção facial inspeção inicial exibe uma simple inspeção digital que exibe a hora atual em horas e minutos:

[![Mostrador do relógio digital](creating-a-watchface-images/01-initial-face.png "captura de tela de exemplo da face do relógio digital inicial")](creating-a-watchface-images/01-initial-face.png#lightbox)

Depois que essa face do relógio digital é desenvolvido e testado, mais código é adicionado para atualizá-lo para uma mais sofisticada Mostrador do relógio analógico com três mãos:

[![Mostrador do relógio analógico](creating-a-watchface-images/02-example-watchface.png "captura de tela de exemplo da face do relógio analógico final")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Assista a face serviços são agrupados e instalados como parte de um aplicativo de desgaste 1.0. Nos exemplos a seguir, `MainActivity` contém nada mais do que o código do modelo de aplicativo Wear 1.0, para que o serviço de detecção facial watch pode ser empacotado e implantado para o relógio inteligente como parte do aplicativo. Na verdade, esse aplicativo servirá puramente como um veículo para obter o serviço de detecção facial de inspeção carregado no dispositivo Wear 1.0 (ou emulador) para depuração e teste.

## <a name="requirements"></a>Requisitos

Para implementar um serviço de face de inspeção, é necessário o seguinte:

-   Android 5.0 (API nível 21) ou superior no desgaste de dispositivo ou emulador.

-   O [Xamarin Android Wear Support Libraries](https://www.nuget.org/packages/Xamarin.Android.Wear) deve ser adicionado ao projeto xamarin. Android.

Embora o Android 5.0 é a API mínimo nível para implementar um serviço de face de inspeção, Android 5.1 ou posterior é recomendado. Android Wear dispositivos que executam o Android 5.1 (API 22) ou superior permitem que aplicativos de desgaste controlar o que é exibido na tela enquanto o dispositivo estiver em baixa energia *ambiente* modo. Quando o dispositivo deixa baixa energia *ambiente* modo, ele está em *interativo* modo. Para obter mais informações sobre esses modos, consulte [manter seu aplicativo visível](https://developer.android.com/training/wearables/apps/always-on.html).


## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Criar um novo projeto Android Wear 1.0 chamado **WatchFace** (para obter mais informações sobre como criar novos projetos do xamarin. Android, consulte [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Caixa de diálogo Novo projeto](creating-a-watchface-images/03-wear-project-vs-sml.png "Selecionar aplicativo Wear na caixa de diálogo Novo projeto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Caixa de diálogo Novo projeto](creating-a-watchface-images/03-wear-project-xs-sml.png "Selecionar aplicativo Wear na caixa de diálogo Novo projeto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


Defina o nome do pacote como `com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configuração do nome do pacote](creating-a-watchface-images/04-package-name-vs.png "definido o nome do pacote como com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Configuração do nome do pacote](creating-a-watchface-images/04-package-name-xs.png "definido o nome do pacote como com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Além disso, role para baixo e habilitar o **INTERNET** e **WAKE_LOCK** permissões:

[![As permissões necessárias](creating-a-watchface-images/05-required-permissions-vs.png "permissões Enable INTERNET e WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Defina a versão mínima do Android para **5.1 Android (API nível 22)**.
Além disso, habilitar o **Internet** e **WakeLock** permissões:

[![As permissões necessárias](creating-a-watchface-images/05-required-permissions-xs.png "permissões Enable Internet e WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Em seguida, baixe [preview.png](creating-a-watchface-images/preview.png) &ndash; isso será adicionado para o **desenháveis** pasta posteriormente neste passo a passo.


## <a name="add-the-xamarinandroid-wear-package"></a>Adicione o pacote xamarin. Android Wear

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Iniciar o Gerenciador de pacotes do NuGet (no Visual Studio, clique com botão direito **referências** na **Gerenciador de soluções** e selecione **Manage NuGet Packages...** ). Atualizar o projeto para a versão estável mais recente do **wear**:

[![Adicionar Gerenciador de pacotes NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "adicionar o pacote wear")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Em seguida, se **Xamarin.Android.Support.v13** é instalado, desinstale-o:

[![Remover Gerenciador de pacotes NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Xamarin.Support.v13 remover")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Iniciar o Gerenciador de pacotes do NuGet (no Visual Studio para Mac, clique com botão direito **pacotes** na **painel de solução** e selecione **adicionar pacotes...** ). Atualizar o projeto para a versão estável mais recente do **wear**:

[![Adicionar Gerenciador de pacotes NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "adicionar o pacote wear")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


Compilar e executar o aplicativo em um emulador ou dispositivo de desgaste (para obter mais informações sobre como fazer isso, consulte a [guia de Introdução](~/android/wear/get-started/index.md) guia). Você verá a seguinte tela do aplicativo no dispositivo Wear:

[![Captura de tela do aplicativo](creating-a-watchface-images/08-app-screen.png "tela do aplicativo no dispositivo Wear")](creating-a-watchface-images/08-app-screen.png#lightbox)

Neste ponto, o aplicativo básico de desgaste não tem funcionalidade de detecção facial watch porque ele não fornece uma implementação de serviço de face de inspeção. Em seguida, esse serviço será adicionado.


## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Implementa do Android Wear Assista faces por meio de `CanvasWatchFaceService` classe. `CanvasWatchFaceService` é derivado de `WatchFaceService`, que por si só é derivado de `WallpaperService` conforme mostrado no diagrama a seguir:

[![Diagrama de herança](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService diagrama de herança")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` inclui um aninhados `CanvasWatchFaceService.Engine`; ele instancia um `CanvasWatchFaceService.Engine` objeto que faz o trabalho real do mostrador de desenho. `CanvasWatchFaceService.Engine` é derivado de `WallpaperService.Engine` conforme mostrado no diagrama acima.

Não mostrado neste diagrama é um `Canvas` que `CanvasWatchFaceService` usa para desenhar o Mostrador &ndash; isso `Canvas` é passado por meio de `OnDraw` método conforme descrito abaixo.

Nas seções a seguir, um serviço de face de Observação personalizada será criado seguindo estas etapas:

1.  Definir uma classe chamada `MyWatchFaceService` que é derivada de `CanvasWatchFaceService`.

2.  Dentro de `MyWatchFaceService`, crie uma classe aninhada chamada `MyWatchFaceEngine` que é derivada de `CanvasWatchFaceService.Engine`.

3.  No `MyWatchFaceService`, implemente uma `CreateEngine` método que instancia `MyWatchFaceEngine` e o retorna.

4.  Na `MyWatchFaceEngine`, implemente o `OnCreate` método para criar o estilo da face de inspeção e realizar outras tarefas de inicialização.

5.  Implemente a `OnDraw` método de `MyWatchFaceEngine`. Esse método é chamado sempre que precisa ser redesenhada face do relógio (ou seja, *invalidado*). `OnDraw` é o método que desenha (e redesenha) elementos de face de inspeção, como hora, minuto e segundo mãos.

6.  Implemente a `OnTimeTick` método de `MyWatchFaceEngine`.
    `OnTimeTick` é chamado pelo menos uma vez por minuto (nos modos de ambientes e interativos) ou quando a data/hora foi alterado.

Para obter mais informações sobre `CanvasWatchFaceService`, consulte o Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) documentação da API.
Da mesma forma, [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explica a implementação real da face do relógio.


### <a name="add-the-canvaswatchfaceservice"></a>Adicionar o CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Adicionar um novo arquivo chamado **MyWatchFaceService.cs** (no Visual Studio, clique com botão direito **WatchFace** no **Gerenciador de soluções**, clique em **Adicionar > Novo Item...** e selecione **classe**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Adicionar um novo arquivo chamado **MyWatchFaceService.cs** (no Visual Studio para Mac, clique com botão direito do **WatchFace** do projeto, clique em **Adicionar > novo arquivo...** e selecione **classe vazia**).

-----

Substitua o conteúdo deste arquivo pelo código a seguir:

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

`MyWatchFaceService` (derivado de `CanvasWatchFaceService`) é o "programa principal" de face do relógio. `MyWatchFaceService` implementa apenas um método, `OnCreateEngine`, que instancia e retorna um `MyWatchFaceEngine` objeto (`MyWatchFaceEngine` é derivado de `CanvasWatchFaceService.Engine`). Instanciada `MyWatchFaceEngine` objeto deve ser retornado como um `WallpaperService.Engine`. O encapsulamento `MyWatchFaceService` objeto é passado para o construtor.

`MyWatchFaceEngine` é a implementação de face de inspeção real &ndash; ele contém o código que desenha a face do relógio. Ele também manipula eventos do sistema, como alterações de tela (modos de ambiente interativo, tela desligar, etc.).


### <a name="implement-the-engine-oncreate-method"></a>Implementar o método OnCreate do mecanismo

O `OnCreate` método inicializa o Mostrador do relógio. Adicione o seguinte campo à `MyWatchFaceEngine`:

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

`OnCreate` é chamado logo após `MyWatchFaceEngine` é iniciado. Ele configura a `WatchFaceStyle` (que controla como o dispositivo de desgaste interage com o usuário) e cria uma instância de `Paint` objeto que será usado para exibir a hora.

A chamada para `SetWatchFaceStyle` faz o seguinte:

1.  Conjuntos *modo de inspeção* para `PeekModeShort`, que faz com que as notificações sejam exibidos como cartões pequeno "espiada" na exibição.

2.  Define a visibilidade do plano de fundo `Interruptive`, que faz com que o plano de fundo de um cartão de inspeção a ser mostrado apenas rapidamente se ele representa uma notificação semelhantes.

3.  Desabilita a hora de interface do usuário do sistema padrão seja desenhada no Mostrador para que o Mostrador personalizados pode exibir a hora em vez disso.

Para obter mais informações sobre essas e outras opções de estilo da face de inspeção, consulte o Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) documentação da API.

Após `SetWatchFaceStyle` for concluída, `OnCreate` instancia o `Paint` objeto (`hoursPaint`) e define sua cor para branco e seu tamanho de texto para 48 pixels ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) deve ser especificada em pixels).


### <a name="implement-the-engine-ondraw-method"></a>Implementar o método OnDraw do mecanismo

O `OnDraw` método talvez seja o mais importante `CanvasWatchFaceService.Engine` método &ndash; é o método que realmente desenha Assista a elementos de face, como dígitos e mãos face do relógio.
No exemplo a seguir desenha uma cadeia de caracteres de tempo na face do relógio.
Adicione o seguinte método à `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quando o Android chama `OnDraw`, ele passa um `Canvas` instância e os limites nos quais a face pode ser desenhada. No exemplo de código acima, `DateTime` é usada para calcular o tempo atual em horas e minutos (no formato de 12 horas). A cadeia de caracteres de hora resultante é desenhada na tela usando o `Canvas.DrawText` método. A cadeia de caracteres aparecerão 70 pixels ao longo da borda esquerda e 80 pixels para baixo da borda superior.

Para obter mais informações sobre o `OnDraw` método, consulte o Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) documentação da API.


### <a name="implement-the-engine-ontimetick-method"></a>Implementar o método de mecanismo OnTimeTick

Android chama o `OnTimeTick` método para atualizar o horário mostrado por face do relógio. Ele é chamado no mínimo uma vez por minuto (em modos interativos e de ambientes), ou quando a data/hora ou o fuso horário foram alterados. Adicione o seguinte método à `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Essa implementação do `OnTimeTick` simplesmente chama `Invalidate`. O `Invalidate` agendas de método `OnDraw` para redesenhar o Mostrador do relógio.

Para obter mais informações sobre o `OnTimeTick` método, consulte o Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) documentação da API.


## <a name="register-the-canvaswatchfaceservice"></a>Registrar o CanvasWatchFaceService

`MyWatchFaceService` deve ser registrado na **androidmanifest. XML** do aplicativo de desgaste associado. Para fazer isso, adicione o seguinte XML para o `<application>` seção:

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

1.  Conjuntos de `android.permission.BIND_WALLPAPER` permissão. Essa permissão concede a permissão de serviço de face de inspeção para alterar o papel de parede de sistema no dispositivo. Observe que essa permissão deve ser definida na `<service>` seção em vez de em externo `<application>` seção.

2.  Define um `watch_face` recursos. Esse recurso é um arquivo XML curto que declara um `wallpaper` recursos (este arquivo será criado na próxima seção).

3.  Declara uma imagem desenhável chamada `preview` que será exibida a tela de seleção de seletor de inspeção.

4.  Inclui um `intent-filter` para permitir que o Android Saiba que `MyWatchFaceService` exibirá uma Mostrador do relógio.

Isso conclui o código para o basic `WatchFace` exemplo. A próxima etapa é adicionar os recursos necessários.


## <a name="add-resource-files"></a>Adicionar arquivos de recurso

Antes de executar o serviço de inspeção, você deve adicionar o **watch_face** recurso e a imagem de visualização. Primeiro, crie um novo arquivo XML em **Resources/xml/watch_face.xml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Definir ação de build deste arquivo como **AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Ação de Build](creating-a-watchface-images/10-android-resource-vs.png "conjunto de compilação para AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Ação de Build](creating-a-watchface-images/10-android-resource-xs.png "conjunto de compilação para AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Esse arquivo de recurso define um simples `wallpaper` elemento que será usado para a face do relógio.

Se você não ainda tiver feito isso, baixe [preview.png](creating-a-watchface-images/preview.png).
Instalá-lo em **Resources/drawable/preview.png**. Certifique-se de adicionar esse arquivo para o `WatchFace` projeto. Esta imagem de visualização é exibida para o usuário no seletor de face de inspeção no dispositivo Wear. Para criar uma imagem de visualização para seu próprios Mostrador do relógio, você pode tirar uma captura de tela do Mostrador enquanto ele está em execução. (Para obter mais informações sobre como obter capturas de tela dos dispositivos de desgaste, consulte [fazer capturas de tela](~/android/wear/deploy-test/debug-on-device.md#screenshots)).


## <a name="try-it"></a>Experimente!

Crie e implante o aplicativo para o dispositivo Wear. Você deverá ver a tela de desgaste do aplicativo como antes. Faça o seguinte para habilitar a nova face do relógio:

1.  Passe o dedo para a direita até ver a tela de fundo da tela de inspeção.

2.  Toque e mantenha-se em qualquer lugar no plano de fundo da tela por dois segundos.

3.  Passe o dedo da esquerda para a direita para navegar por meio de várias faces de inspeção.

4.  Selecione o **exemplo de Xamarin** Mostrador do relógio (mostrado à direita):

    [![Seletor de Watchface](creating-a-watchface-images/11-watchface-picker.png "passar o dedo para localizar o mostrador de amostra do Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  Toque o **exemplo de Xamarin** Mostrador do relógio para selecioná-lo.

Isso altera a face do relógio do dispositivo Wear para usar o serviço de detecção facial Observação personalizada implementado até agora:

[![Mostrador do relógio digital](creating-a-watchface-images/12-digital-watchface.png "inspeção de digital personalizado em execução no dispositivo Wear")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Isso é uma Mostrador relativamente bruta porque a implementação do aplicativo é tão mínima (por exemplo, ele não inclui um plano de fundo de face de inspeção e ele não chame `Paint` métodos de suavização de borda para melhorar a aparência).
No entanto, ele implementa a funcionalidade básica que é necessária para criar uma Mostrador personalizado.

Na próxima seção, o Mostrador do relógio será atualizado para uma implementação mais sofisticada.


## <a name="upgrading-the-watch-face"></a>Atualizando o Mostrador do relógio

No restante deste passo a passo, `MyWatchFaceService` é atualizado para exibir um mostrador analógico estilo e é estendido para dar suporte a mais recursos. Os seguintes recursos serão adicionados para criar o Mostrador atualizado:

1.  Indica o tempo com analógica hora, minuto e segundo mãos.

2.  Reage a alterações na visibilidade.

3.  Responde a alterações entre o modo de ambiente e o modo interativo.

4.  Lê as propriedades do dispositivo Wear subjacente.

5.  Atualiza automaticamente o tempo quando ocorre uma alteração de fuso horário.

Antes de implementar as alterações de código abaixo, baixe [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), descompactá-lo e mover os arquivos. PNG descompactado **recursos/drawable** (substituir anterior **preview.png**). Adicionar os novos arquivos. png para o `WatchFace` projeto.


### <a name="update-engine-features"></a>Recursos do mecanismo de atualização

A próxima etapa é a atualização **MyWatchFaceService.cs** a uma implementação que desenha um mostrador do relógio analógico e dá suporte a novos recursos. Substitua o conteúdo do **MyWatchFaceService.cs** com a versão analógica do código na face inspeção [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (você pode recortar e colar esse código-fonte em existente  **MyWatchFaceService.cs**).

Esta versão do **MyWatchFaceService.cs** adiciona mais código para os métodos existentes e inclui métodos substituídos adicionais para adicionar mais funcionalidade. As seções a seguir fornecem um tour guiado do código-fonte.

#### <a name="oncreate"></a>OnCreate

Atualizada **OnCreate** método configura o estilo da face de inspeção como antes, mas inclui algumas etapas adicionais:

1.  Define a imagem de plano de fundo o **xamarin_background** recurso que reside no **Resources/drawable-hdpi/xamarin_background.png**.

2.  Inicializa `Paint` objetos de desenho a mão de horas, minutos e mão de segundo.

3.  Inicializa um `Paint` objeto para desenhar os tiques de hora em torno da borda do Mostrador.

4.  Cria um temporizador que chama o `Invalidate` método (redesenho), de modo que a mão de segundo será redesenhada cada segundo. Observe que esse temporizador é necessário porque `OnTimeTick` chamadas `Invalidate` apenas uma vez por minuto.

Este exemplo inclui apenas uma **xamarin_background.png** imagem; no entanto, você talvez queira criar uma imagem de plano de fundo diferente para cada densidade da tela que darão suporte a face do relógio personalizado.

#### <a name="ondraw"></a>OnDraw

Atualizada **OnDraw** método desenha um mostrador do estilo analógico usando as seguintes etapas:

1.  Obtém a hora atual, que agora é mantida em um `time` objeto.

2.  Determina os limites de seu centro e a superfície de desenho.

3.  Desenha a tela de fundo, dimensionada para ajustar o dispositivo quando o plano de fundo for desenhado.

4.  Desenha doze *tiques* em torno a face do relógio (correspondentes às horas na face do relógio).

5.  Calcula o ângulo, rotação e comprimento de cada ponteiro de inspeção.

6.  Desenha cada mão na superfície de inspeção. Observe que a mão de segundo não é desenhada se o relógio está no modo de ambiente.


#### <a name="onpropertieschanged"></a>OnPropertiesChanged

Esse método é chamado para informar o usuário `MyWatchFaceEngine` sobre as propriedades do dispositivo Wear (como o modo de ambiente de baixo bits e burn-in de proteção). Em `MyWatchFaceEngine`, esse método só verifica para baixo de bits modo ambiente (no modo ambiente de bits inferior, a tela dá suporte ao menos bits para cada cor).

Para obter mais informações sobre esse método, consulte o Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) documentação da API.


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Esse método é chamado quando o dispositivo de desgaste entra ou sai do modo de ambiente. No `MyWatchFaceEngine` implementação, o Mostrador desabilita a suavização quando ele estiver no modo de ambiente.

Para obter mais informações sobre esse método, consulte o Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) documentação da API.


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

Esse método é chamado sempre que o relógio se torna visível ou oculto. Em `MyWatchFaceEngine`, esse método registra/cancela o registro o receptor de fuso horário (descrito abaixo) acordo com o estado de visibilidade.

Para obter mais informações sobre esse método, consulte o Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) documentação da API.


### <a name="time-zone-feature"></a>Recurso de fuso horário

O novo **MyWatchFaceService.cs** também inclui a funcionalidade para atualizar a hora atual, sempre que o tempo de zona alterações (como durante viagens entre fusos horários). Perto do final da **MyWatchFaceService.cs**, uma alteração de zona de tempo `BroadcastReceiver` é definida que lida com objetos de tentativas de alteração do fuso horário:

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
`UnregisterTimezoneReceiver` é chamado o quando o estado de visibilidade de face do relógio é alterado para oculto. Quando o Mostrador do relógio está visível novamente, `RegisterTimezoneReceiver` é chamado (consulte o `OnVisibilityChanged` método).

O mecanismo `RegisterTimezoneReceiver` método declara um manipulador para esse receptor de fuso horário `Receive` evento; esse manipulador atualiza o `time` objeto com a nova hora sempre que um fuso horário é cruzado:

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

Um filtro de intenção é criado e registrado para o receptor de fuso horário:

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

O `UnregisterTimezoneReceiver` método cancela o registro o receptor de fuso horário:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Execute o Mostrador aprimorado

Crie e implante o aplicativo para o dispositivo de desgaste novamente. Selecione o mostrador de seletor de face de inspeção como antes. A visualização no seletor de inspeção é mostrada à esquerda e a nova face do relógio é exibido à direita:

[![Mostrador do relógio analógico](creating-a-watchface-images/13-analog-watchface.png "aprimorado face analógico no seletor e no dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

Nesta captura de tela, o segundo disponível está movendo uma vez por segundo. Quando você executa esse código em um dispositivo Wear, a mão segundo desaparece quando o relógio entra no modo de ambiente.


## <a name="summary"></a>Resumo

Neste passo a passo, um watchface personalizada do Android Wear 1.0 foi implementado e testado. O `CanvasWatchFaceService` e `CanvasWatchFaceService.Engine` classes foram introduzidas e os métodos essenciais da classe mecanismo foram implementados para criar uma Mostrador digital simples. Essa implementação foi atualizada com mais funcionalidades para criar uma Mostrador do relógio analógico e métodos adicionais foram implementados para lidar com mudanças na visibilidade, modo de ambiente e as diferenças nas propriedades do dispositivo. Por fim, um receptor de difusão de fuso horário foi implementado para que o relógio atualiza automaticamente a hora quando um fuso horário é cruzado.


## <a name="related-links"></a>Links relacionados

- [Criação de mostradores de relógio](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Exemplo de WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
