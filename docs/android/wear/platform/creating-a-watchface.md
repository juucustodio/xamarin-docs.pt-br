---
title: Criando uma face de inspeção para o desgaste do Android 1,0
description: Este guia explica como implementar um serviço de face de inspeção personalizado para o desgaste do Android 1,0. Instruções passo a passo são fornecidas para a criação de um serviço de face de inspeção digital retirado, seguido por mais código para criar uma face de inspeção de estilo analógico.
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/23/2018
ms.openlocfilehash: 38123e2b1ef20144606bcc77ad33af572aa3707a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030378"
---
# <a name="creating-a-watch-face"></a>Como criar um mostrador de relógio

_Este guia explica como implementar um serviço de face de inspeção personalizado para o desgaste do Android 1,0. Instruções passo a passo são fornecidas para a criação de um serviço de face de inspeção digital retirado, seguido por mais código para criar uma face de inspeção de estilo analógico._

## <a name="overview"></a>Visão Geral

Neste tutorial, um serviço de face de inspeção básico é criado para ilustrar os conceitos básicos da criação de uma face de relógio do Android desgaste 1,0 personalizada.
O serviço de face de inspeção inicial exibe uma inspeção digital simples que exibe a hora atual em horas e minutos:

[![Rosto de inspeção digital](creating-a-watchface-images/01-initial-face.png "Captura de tela de exemplo do rosto de inspeção digital inicial")](creating-a-watchface-images/01-initial-face.png#lightbox)

Depois que esse tipo de inspeção digital é desenvolvido e testado, mais código é adicionado para atualizá-lo para uma face de inspeção analógica mais sofisticada com três mãos:

[![Face de inspeção analógica](creating-a-watchface-images/02-example-watchface.png "Captura de tela de exemplo da face de inspeção analógica final")](creating-a-watchface-images/02-example-watchface.png#lightbox)

Observe que os serviços de face são agrupados e instalados como parte de um aplicativo de desgaste 1,0. Nos exemplos a seguir, `MainActivity` não contém nada mais do que o código do modelo de aplicativo de desgaste 1,0 para que o serviço Watch face possa ser empacotado e implantado no Smart Watch como parte do aplicativo. Na verdade, esse aplicativo servirá puramente como um veículo para obter o serviço de face de inspeção carregado no dispositivo (ou emulador) de desgaste 1,0 para depuração e teste.

## <a name="requirements"></a>Requisitos

Para implementar um serviço Watch face, é necessário o seguinte:

- Android 5,0 (nível de API 21) ou superior no dispositivo ou emulador de desgaste.

- As [bibliotecas de suporte a desgaste do Xamarin Android](https://www.nuget.org/packages/Xamarin.Android.Wear) devem ser adicionadas ao projeto Xamarin. Android.

Embora o Android 5,0 seja o nível mínimo de API para implementar um serviço Watch face, o Android 5,1 ou posterior é recomendado. Dispositivos Android que executam o Android 5,1 (API 22) ou superior permitem que aplicativos de desgaste controlem o que é exibido na tela enquanto o dispositivo está no modo de *ambiente* de baixa energia. Quando o dispositivo deixa o modo de *ambiente* de baixa energia, ele está no modo *interativo* . Para obter mais informações sobre esses modos, consulte [mantendo seu aplicativo visível](https://developer.android.com/training/wearables/apps/always-on.html).

## <a name="start-an-app-project"></a>Iniciar um projeto de aplicativo

Crie um novo projeto do Android desgaste 1,0 chamado **WatchFace** (para obter mais informações sobre como criar novos projetos do Xamarin. Android, consulte [Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Caixa de diálogo novo projeto](creating-a-watchface-images/03-wear-project-vs-sml.png "Selecionar aplicativo de desgaste na caixa de diálogo novo projeto")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Caixa de diálogo novo projeto](creating-a-watchface-images/03-wear-project-xs-sml.png "Selecionar aplicativo de desgaste na caixa de diálogo novo projeto")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

Defina o nome do pacote como `com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configuração do nome do pacote](creating-a-watchface-images/04-package-name-vs.png "Defina o nome do pacote como com. xamarin. watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Configuração do nome do pacote](creating-a-watchface-images/04-package-name-xs.png "Defina o nome do pacote como com. xamarin. watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Além disso, role para baixo e habilite as permissões **Internet** e **WAKE_LOCK** :

[![Permissões necessárias](creating-a-watchface-images/05-required-permissions-vs.png "Habilitar permissões de INTERNET e WAKE_LOCK")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Defina a versão mínima do Android para **android 5,1 (API nível 22)** .
Além disso, habilite as permissões de **Internet** e **WakeLock** :

[![Permissões necessárias](creating-a-watchface-images/05-required-permissions-xs.png "Habilitar permissões de Internet e WakeLock")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

Em seguida, baixe [Preview. png](creating-a-watchface-images/preview.png) &ndash; isso será adicionado à pasta **drawables** mais adiante neste passo a passos.

## <a name="add-the-xamarinandroid-wear-package"></a>Adicionar o pacote de desgaste do Xamarin. Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Inicie o Gerenciador de pacotes NuGet (no Visual Studio, clique com o botão direito do mouse em **referências** no **Gerenciador de soluções** e selecione **gerenciar pacotes NuGet...** ). Atualize o projeto para a versão estável mais recente do **Xamarin. Android. desgaste**:

[![Adicionar Gerenciador de pacotes NuGet](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "Adicionar o pacote Xamarin. Android. desgaste")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

Em seguida, se o **Xamarin. Android. support. v13** estiver instalado, desinstale-o:

[![Remoção do Gerenciador de pacotes NuGet](creating-a-watchface-images/07-uninstall-v13-sml.png "Remover Xamarin. support. v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Inicie o Gerenciador de pacotes NuGet (em Visual Studio para Mac, clique com o botão direito do mouse em **pacotes** no **painel de solução** e selecione **adicionar pacotes...** ). Atualize o projeto para a versão estável mais recente do **Xamarin. Android. desgaste**:

[![Adicionar Gerenciador de pacotes NuGet](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "Adicionar o pacote Xamarin. Android. desgaste")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

Compilar e executar o aplicativo em um dispositivo ou emulador de desgaste (para obter mais informações sobre como fazer isso, consulte o guia de [introdução](~/android/wear/get-started/index.md) ). Você deverá ver a seguinte tela de aplicativo no dispositivo de desgaste:

[![Captura de tela do aplicativo](creating-a-watchface-images/08-app-screen.png "Tela de aplicativo no dispositivo de desgaste")](creating-a-watchface-images/08-app-screen.png#lightbox)

Neste ponto, o aplicativo de desgaste básico não tem a funcionalidade de relógio de inspeção porque ainda não fornece uma implementação de serviço de inspeção. Esse serviço será adicionado em seguida.

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

O desgaste do Android implementa rostos de inspeção por meio da classe `CanvasWatchFaceService`. `CanvasWatchFaceService` é derivado de `WatchFaceService`, que é derivado de `WallpaperService` conforme mostrado no diagrama a seguir:

[![Diagrama de herança](creating-a-watchface-images/09-inheritance-diagram-sml.png "Diagrama de herança do CanvasWatchFaceService")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` inclui um `CanvasWatchFaceService.Engine` aninhado; Ele cria uma instância de um objeto `CanvasWatchFaceService.Engine` que faz o trabalho real de desenhar a face do relógio. `CanvasWatchFaceService.Engine` é derivado de `WallpaperService.Engine` conforme mostrado no diagrama acima.

Não é mostrado neste diagrama um `Canvas` que `CanvasWatchFaceService` usa para desenhar a face de inspeção &ndash; essa `Canvas` é passada por meio do método `OnDraw`, conforme descrito abaixo.

Nas seções a seguir, um serviço de face de inspeção personalizado será criado seguindo estas etapas:

1. Defina uma classe chamada `MyWatchFaceService` que é derivada de `CanvasWatchFaceService`.

2. Em `MyWatchFaceService`, crie uma classe aninhada chamada `MyWatchFaceEngine` derivada de `CanvasWatchFaceService.Engine`.

3. No `MyWatchFaceService`, implemente um método `CreateEngine` que instancia `MyWatchFaceEngine` e o retorna.

4. No `MyWatchFaceEngine`, implemente o método `OnCreate` para criar o estilo de rosto de inspeção e executar outras tarefas de inicialização.

5. Implemente o método de `OnDraw` de `MyWatchFaceEngine`. Esse método é chamado sempre que a face de inspeção precisa ser redesenhada (ou seja, *invalidada*). `OnDraw` é o método que desenha (e redesenha) inspecionar elementos de face, como hora, minuto e segundo, mãos.

6. Implemente o método de `OnTimeTick` de `MyWatchFaceEngine`.
    `OnTimeTick` é chamado pelo menos uma vez por minuto (nos modos de ambiente e interativo) ou quando a data/hora foi alterada.

Para obter mais informações sobre `CanvasWatchFaceService`, consulte a documentação da API [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) do Android.
Da mesma forma, [CanvasWatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) explica a implementação real da face Watch.

### <a name="add-the-canvaswatchfaceservice"></a>Adicionar o CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Adicione um novo arquivo chamado **MyWatchFaceService.cs** (no Visual Studio, clique com o botão direito do mouse em **WatchFace** na **Gerenciador de Soluções**, clique em **Adicionar > novo item...** e selecione **classe**).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Adicione um novo arquivo chamado **MyWatchFaceService.cs** (em Visual Studio para Mac, clique com o botão direito do mouse no projeto **WatchFace** , clique em **Adicionar > novo arquivo...** e selecione **classe vazia**).

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

`MyWatchFaceService` (derivado de `CanvasWatchFaceService`) é o "programa principal" da face de inspeção. `MyWatchFaceService` implementa apenas um método, `OnCreateEngine`, que instancia e retorna um objeto `MyWatchFaceEngine` (`MyWatchFaceEngine` é derivado de `CanvasWatchFaceService.Engine`). O objeto de `MyWatchFaceEngine` instanciado deve ser retornado como um `WallpaperService.Engine`. O objeto de `MyWatchFaceService` encapsulamento é passado para o construtor.

`MyWatchFaceEngine` é a implementação da face de observação real &ndash; ele contém o código que desenha a face de inspeção. Ele também manipula eventos do sistema, como alterações de tela (modos de ambiente/interativo, ativação de tela, etc.).

### <a name="implement-the-engine-oncreate-method"></a>Implementar o método OnCreate do mecanismo

O método `OnCreate` Inicializa o rosto de inspeção. Adicione o seguinte campo a `MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

Este objeto de `Paint` será usado para desenhar a hora atual na face do relógio. Em seguida, adicione o seguinte método para `MyWatchFaceEngine`:

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

`OnCreate` será chamado logo após o `MyWatchFaceEngine` ser iniciado. Ele configura o `WatchFaceStyle` (que controla como o dispositivo de desgaste interage com o usuário) e instancia o objeto `Paint` que será usado para exibir a hora.

A chamada para `SetWatchFaceStyle` faz o seguinte:

1. Define o *modo de Peek* como `PeekModeShort`, o que faz com que as notificações apareçam como pequenas cartas de "Peek" na tela.

2. Define a visibilidade em segundo plano como `Interruptive`, o que faz com que o plano de fundo de um cartão de Peek seja mostrado apenas resumidamente se ele representa uma notificação de interrupção.

3. Desabilita o tempo de interface do usuário do sistema padrão de ser desenhado na face de inspeção para que a face de inspeção personalizada possa exibir a hora em vez disso.

Para obter mais informações sobre essas e outras opções de estilo facial de inspeção, consulte a documentação da API do Android [WatchFaceStyle. Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) .

Depois que `SetWatchFaceStyle` for concluído, `OnCreate` instanciará o objeto `Paint` (`hoursPaint`) e definirá sua cor como branco e seu tamanho de texto como 48 pixels (o[TEXTSIZE](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29) deve ser especificado em pixels).

### <a name="implement-the-engine-ondraw-method"></a>Implementar o método OnDraw do mecanismo

O método de `OnDraw` é talvez o método `CanvasWatchFaceService.Engine` mais importante &ndash; é o método que realmente desenha os elementos de face de relógio, como dígitos e mãos.
No exemplo a seguir, ele desenha uma cadeia de caracteres de tempo na face do relógio.
Adicione o seguinte método a `MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

Quando o Android chama `OnDraw`, ele passa uma instância de `Canvas` e os limites em que a face pode ser desenhada. No exemplo de código acima, `DateTime` é usado para calcular a hora atual em horas e minutos (no formato de 12 horas). A cadeia de caracteres de tempo resultante é desenhada na tela usando o método `Canvas.DrawText`. A cadeia de caracteres será exibida 70 pixels acima da borda esquerda e 80 pixels para baixo na borda superior.

Para obter mais informações sobre o método `OnDraw`, consulte a documentação da API [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) do Android.

### <a name="implement-the-engine-ontimetick-method"></a>Implementar o método OnTimeTick do mecanismo

O Android chama periodicamente o método `OnTimeTick` para atualizar o tempo mostrado pela face de inspeção. Ele é chamado pelo menos uma vez por minuto (nos modos de ambiente e interativo) ou quando a data/hora ou o fuso horário foram alterados. Adicione o seguinte método a `MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

Essa implementação de `OnTimeTick` simplesmente chama `Invalidate`. O método `Invalidate` agenda `OnDraw` para redesenhar a face de inspeção.

Para obter mais informações sobre o método `OnTimeTick`, consulte a documentação da API [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) do Android.

## <a name="register-the-canvaswatchfaceservice"></a>Registrar o CanvasWatchFaceService

`MyWatchFaceService` deve ser registrado no **AndroidManifest. xml** do aplicativo de desgaste associado. Para fazer isso, adicione o seguinte XML à seção `<application>`:

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

1. Define a permissão `android.permission.BIND_WALLPAPER`. Essa permissão dá permissão ao serviço Watch face para alterar o papel de parede do sistema no dispositivo. Observe que essa permissão deve ser definida na seção `<service>` em vez de na seção `<application>` externa.

2. Define um recurso de `watch_face`. Esse recurso é um arquivo XML curto que declara um recurso de `wallpaper` (esse arquivo será criado na próxima seção).

3. Declara uma imagem desenháa chamada `preview` que será exibida pela tela de seleção do seletor de inspeção.

4. Inclui um `intent-filter` para permitir que o Android saiba que `MyWatchFaceService` exibirá uma face de inspeção.

Isso conclui o código para o exemplo de `WatchFace` básico. A próxima etapa é adicionar os recursos necessários.

## <a name="add-resource-files"></a>Adicionar arquivos de recurso

Para poder executar o serviço Watch, você deve adicionar o recurso **watch_face** e a imagem de visualização. Primeiro, crie um novo arquivo XML em **Resources/XML/watch_face. xml** e substitua seu conteúdo pelo seguinte XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

Defina a ação de Build deste arquivo como **AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Ação de Build](creating-a-watchface-images/10-android-resource-vs.png "Definir ação de compilação como AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![Ação de Build](creating-a-watchface-images/10-android-resource-xs.png "Definir ação de compilação como AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

Esse arquivo de recurso define um elemento de `wallpaper` simples que será usado para a face de inspeção.

Se você ainda não tiver feito isso, baixe [Preview. png](creating-a-watchface-images/preview.png).
Instale-o em **recursos/empates/Preview. png**. Certifique-se de adicionar esse arquivo ao projeto `WatchFace`. Essa imagem de visualização é exibida para o usuário no seletor de face de inspeção no dispositivo de desgaste. Para criar uma imagem de visualização para sua própria face de inspeção, você pode tirar uma captura de tela da face de inspeção enquanto ela está em execução. (Para obter mais informações sobre como obter capturas de tela de dispositivos de desgaste, consulte [tirando capturas de tela](~/android/wear/deploy-test/debug-on-device.md#screenshots)).

## <a name="try-it"></a>Experimente!

Crie e implante o aplicativo no dispositivo de desgaste. Você deve ver a tela de aplicativo de desgaste aparecer como antes. Faça o seguinte para habilitar a nova face de inspeção:

1. Passe o dedo para a direita até ver o plano de fundo da tela de observação.

2. Toque e segure em qualquer lugar do plano de fundo da tela por dois segundos.

3. Passe o dedo da esquerda para a direita para navegar pelas várias faces de inspeção.

4. Selecione a face de inspeção de **exemplo do Xamarin** (mostrada à direita):

    [![Seletor de Watchface](creating-a-watchface-images/11-watchface-picker.png "Passe o dedo para localizar o rosto de inspeção de exemplo do Xamarin")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. Toque na face de inspeção de **exemplo do Xamarin** para selecioná-la.

Isso altera a face do relógio do dispositivo de desgaste para usar o serviço de face de inspeção personalizado implementado até o momento:

[![Rosto de inspeção digital](creating-a-watchface-images/12-digital-watchface.png "Inspeção digital personalizada em execução no dispositivo de desgaste")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

Trata-se de uma superfície de inspeção relativamente crua, pois a implementação do aplicativo é tão mínima (por exemplo, não inclui um plano de fundo da face de inspeção e não chama `Paint` métodos AntiAlias para melhorar a aparência).
No entanto, ele implementa a funcionalidade básica que é necessária para criar uma face de inspeção personalizada.

Na próxima seção, essa face de inspeção será atualizada para uma implementação mais sofisticada.

## <a name="upgrading-the-watch-face"></a>Atualizando a face do relógio

No restante deste passo a passos, `MyWatchFaceService` é atualizado para exibir uma face de inspeção de estilo analógico e é estendido para dar suporte a mais recursos. Os seguintes recursos serão adicionados para criar o rosto de inspeção atualizado:

1. Indica a hora com as mãos de hora, minuto e segundo analógicas.

2. Reage a alterações na visibilidade.

3. Responde a alterações entre o modo de ambiente e o modo interativo.

4. Lê as propriedades do dispositivo de desgaste subjacente.

5. Atualiza automaticamente a hora em que ocorre uma alteração de fuso horário.

Antes de implementar as alterações de código abaixo, baixe [drawed. zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true), descompacte-o e mova os arquivos. png descompactados para **recursos/desenháveis** (substitua a **visualização anterior. png**). Adicione os novos arquivos. png ao projeto `WatchFace`.

### <a name="update-engine-features"></a>Recursos do mecanismo de atualização

A próxima etapa é atualizar **MyWatchFaceService.cs** para uma implementação que desenha uma face de inspeção analógica e dá suporte a novos recursos. Substitua o conteúdo de **MyWatchFaceService.cs** pela versão analógica do código de rosto de inspeção em [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (você pode recortar e colar essa fonte no **MyWatchFaceService.cs**existente).

Esta versão do **MyWatchFaceService.cs** adiciona mais código aos métodos existentes e inclui métodos adicionais substituídos para adicionar mais funcionalidade. As seções a seguir fornecem um tour guiado do código-fonte.

#### <a name="oncreate"></a>OnCreate

O método **OnCreate** atualizado configura o estilo facial de inspeção como antes, mas inclui algumas etapas adicionais:

1. Define a imagem de plano de fundo para o recurso **xamarin_background** que reside em **Resources/Drawable-hdpi/xamarin_background. png**.

2. Inicializa `Paint` objetos para desenhar a hora, o minuto e a segunda mão.

3. Inicializa um objeto `Paint` para desenhar os tiques de hora ao lado da borda da face de inspeção.

4. Cria um temporizador que chama o método `Invalidate` (redesenhar) para que a segunda mão seja redesenhada a cada segundo. Observe que esse temporizador é necessário porque `OnTimeTick` chama `Invalidate` apenas uma vez a cada minuto.

Este exemplo inclui apenas uma imagem **xamarin_background. png** ; no entanto, talvez você queira criar uma imagem de plano de fundo diferente para cada densidade de tela que sua face de inspeção personalizada dará suporte.

#### <a name="ondraw"></a>OnDraw

O método **OnDraw** atualizado desenha uma face de inspeção de estilo analógico usando as seguintes etapas:

1. Obtém a hora atual, que agora é mantida em um objeto `time`.

2. Determina os limites da superfície de desenho e seu centro.

3. Desenha o plano de fundo, dimensionado para caber no dispositivo quando o plano de fundo é desenhado.

4. Desenha doze *tiques* em frente ao relógio (correspondendo às horas no rosto do relógio).

5. Calcula o ângulo, a rotação e o comprimento de cada inspeção.

6. Desenha cada mão na superfície de inspeção. Observe que a segunda mão não será desenhada se a inspeção estiver no modo ambiente.

#### <a name="onpropertieschanged"></a>Onpropertieschanged

Esse método é chamado para informar `MyWatchFaceEngine` sobre as propriedades do dispositivo de desgaste (como o modo ambiente de baixo bit e a proteção de Burn-in). No `MyWatchFaceEngine`, esse método verifica apenas o modo de ambiente de bit baixo (no modo de ambiente de bit baixo, a tela dá suporte a menos bits para cada cor).

Para obter mais informações sobre esse método, consulte a documentação da API do Android [Onpropertieschanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) .

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

Esse método é chamado quando o dispositivo de desgaste entra ou sai do modo de ambiente. Na implementação de `MyWatchFaceEngine`, a face de inspeção desabilita a suavização de serrilhado quando está no modo ambiente.

Para obter mais informações sobre esse método, consulte a documentação da API do Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) .

#### <a name="onvisibilitychanged"></a>Onvisibilitychanged

Esse método é chamado sempre que a inspeção se torna visível ou oculta. Em `MyWatchFaceEngine`, esse método registra/cancela o registro do receptor de fuso horário (descrito abaixo) de acordo com o estado de visibilidade.

Para obter mais informações sobre esse método, consulte a documentação da API do Android [Onvisibilidadechanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) .

### <a name="time-zone-feature"></a>Recurso de fuso horário

O novo **MyWatchFaceService.cs** também inclui a funcionalidade para atualizar a hora atual sempre que o fuso horário for alterado (por exemplo, ao viajar entre fusos horários). Próximo ao final de **MyWatchFaceService.cs**, uma alteração de fuso horário `BroadcastReceiver` é definida que manipula os objetos de intenção de alteração de fuso horário:

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

Os métodos `RegisterTimezoneReceiver` e `UnregisterTimezoneReceiver` são chamados pelo método `OnVisibilityChanged`.
`UnregisterTimezoneReceiver` é chamado quando o estado de visibilidade da face de inspeção é alterado para oculto. Quando a face Watch está visível novamente, `RegisterTimezoneReceiver` é chamado (consulte o método `OnVisibilityChanged`).

O método de `RegisterTimezoneReceiver` do mecanismo declara um manipulador para esse evento de `Receive` do receptor de fuso horário; Esse manipulador atualiza o objeto `time` com a nova hora sempre que um fuso horário é cruzado:

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

O método `UnregisterTimezoneReceiver` cancela o registro do receptor de fuso horário:

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>Executar o rosto de inspeção aprimorado

Crie e implante o aplicativo no dispositivo de desgaste novamente. Selecione a face de inspeção do seletor de face de inspeção como antes. A visualização no seletor de inspeção é mostrada à esquerda e a nova face de inspeção é mostrada à direita:

[![Face de inspeção analógica](creating-a-watchface-images/13-analog-watchface.png "Aparência analógica aprimorada no seletor e no dispositivo")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

Nesta captura de tela, a segunda mão é movida uma vez por segundo. Quando você executa esse código em um dispositivo de desgaste, a segunda mão desaparece quando o relógio entra no modo ambiente.

## <a name="summary"></a>Resumo

Neste tutorial, um watchface de desgaste do Android 1,0 personalizado foi implementado e testado. As classes `CanvasWatchFaceService` e `CanvasWatchFaceService.Engine` foram introduzidas, e os métodos essenciais da classe Engine foram implementados para criar uma face de inspeção digital simples. Essa implementação foi atualizada com mais funcionalidade para criar uma face de inspeção analógica, e métodos adicionais foram implementados para lidar com alterações de visibilidade, modo ambiente e diferenças nas propriedades do dispositivo. Por fim, um receptor de difusão de fuso horário foi implementado para que o relógio atualize automaticamente a hora em que um fuso horário é ultrapassado.

## <a name="related-links"></a>Links relacionados

- [Criando rostos de inspeção](https://developer.android.com/training/wearables/watch-faces/index.html)
- [Exemplo de WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService. Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
