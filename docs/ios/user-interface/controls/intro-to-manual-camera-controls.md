---
title: Controles de câmera manuais no Xamarin. iOS
description: Este documento descreve como a estrutura AVFoundation do iOS pode ser usada com o Xamarin. iOS para habilitar controles de câmera manuais. Os controles de câmera manual permitem que um usuário controle o foco, o equilíbrio de branco e as configurações de exposição.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 889bc13cfd0cbea51c34e8b3bcb6393293f4c2ae
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528754"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Controles de câmera manuais no Xamarin. iOS

Os controles de câmera manual, fornecidos pelo `AVFoundation Framework` no Ios 8, permitem que um aplicativo móvel assuma o controle total sobre a câmera de um dispositivo IOS. Esse nível refinado de controle pode ser usado para criar aplicativos de câmera de nível profissional e fornecer composições de artista ajustando os parâmetros da câmera ao mesmo tempo em que faz uma imagem ou um vídeo ainda.

Esses controles também podem ser úteis ao desenvolver aplicativos científicos ou industriais, onde os resultados são menos direcionados para a exatidão ou a beleza da imagem, e são voltados para realçar algum recurso ou elemento da imagem que está sendo executada.

## <a name="avfoundation-capture-objects"></a>Objetos de captura AVFoundation

Seja ao tirar vídeo ou imagens que usam a câmera em um dispositivo iOS, o processo usado para capturar essas imagens é basicamente o mesmo. Isso é verdadeiro para aplicativos que usam os controles de câmera automatizados padrão ou aqueles que aproveitam os novos controles de câmera manual:

 [![](intro-to-manual-camera-controls-images/image1.png "Visão geral dos objetos de captura do AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

A entrada é retirada de `AVCaptureDeviceInput` um em `AVCaptureSession` um por meio de `AVCaptureConnection`um. O resultado é a saída como uma imagem ainda ou como um fluxo de vídeo. Todo o processo é controlado por um `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controles manuais fornecidos

Usando as novas APIs fornecidas pelo iOS 8, o aplicativo pode assumir o controle dos seguintes recursos de câmera:

- **Foco manual** – ao permitir que o usuário final assuma o controle do foco diretamente, um aplicativo pode fornecer mais controle sobre a imagem obtida.
- **Exposição manual** – ao fornecer controle manual sobre a exposição, um aplicativo pode fornecer mais liberdade aos usuários e permitir que eles atinjam uma aparência estilizada.
- **Balanço de branco manual** – o balanço de branco é usado para ajustar a cor em uma imagem — geralmente para torná-la realista. Fontes de luz diferentes têm temperaturas de cor diferentes e as configurações de câmera usadas para capturar uma imagem são ajustadas para compensar essas diferenças. Novamente, ao permitir que o usuário controle o equilíbrio de branco, os usuários podem fazer ajustes que não podem ser feitos automaticamente.


o iOS 8 fornece extensões e aprimoramentos às APIs do iOS existentes para fornecer esse controle refinado sobre o processo de captura de imagem.

## <a name="bracketed-capture"></a>Captura entre colchetes

A captura entre colchetes é baseada nas configurações dos controles de câmera manuais apresentados acima e permite que o aplicativo Capture um momento no tempo, de várias maneiras diferentes.

Simplesmente declarado, a captura entre colchetes é uma intermitência de imagens ainda realizadas com uma variedade de configurações de imagem para imagem.

## <a name="requirements"></a>Requisitos

Os itens a seguir são necessários para concluir as etapas apresentadas neste artigo:

- **Xcode 7 + e Ios 8 ou mais recente** – as APIs do Xcode 7 e Ios 8 ou mais recentes da Apple precisam ser instaladas e configuradas no computador do desenvolvedor.
- **Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
- **dispositivo IOS 8** – um dispositivo IOS executando a versão mais recente do IOS 8. Os recursos de câmera não podem ser testados no simulador de iOS.


## <a name="general-av-capture-setup"></a>Configuração geral da captura de AV

Ao gravar vídeo em um dispositivo iOS, há algum código de instalação geral que é sempre necessário. Esta seção abordará a configuração mínima necessária para gravar vídeo da câmera do dispositivo iOS e exibir esse vídeo em tempo real em um `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegar exemplo de buffer de saída

Uma das primeiras coisas necessárias será um delegado para monitorar o buffer de saída de exemplo e exibir uma imagem capturada do buffer para um `UIImageView` na interface do usuário do aplicativo.

A rotina a seguir monitorará o buffer de exemplo e atualizará a interface do usuário:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

Com essa rotina em vigor, o `AppDelegate` pode ser modificado para abrir uma sessão de captura de AV para registrar um feed de vídeo ao vivo.

### <a name="creating-an-av-capture-session"></a>Criando uma sessão de captura do AV

A sessão de captura de AV é usada para controlar a gravação de vídeo ao vivo da câmera do dispositivo iOS e é necessária para obter o vídeo em um aplicativo iOS. Como o aplicativo `ManualCameraControl` de exemplo de exemplo está usando a sessão de captura em vários locais diferentes, ele será configurado `AppDelegate` no e disponibilizado para todo o aplicativo.

Faça o seguinte para modificar o aplicativo `AppDelegate` e adicionar o código necessário:


1. Clique duas vezes no `AppDelegate.cs` arquivo no Gerenciador de soluções para abri-lo para edição.
1. Adicione as seguintes instruções using à parte superior do arquivo:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. Adicione as seguintes variáveis particulares e propriedades computadas à `AppDelegate` classe:
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. Substitua o método concluído e altere-o para:
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. Salve as alterações no arquivo.


Com esse código em vigor, os controles manuais da câmera podem ser facilmente implementados para experimentação e teste.

## <a name="manual-focus"></a>Foco manual

Ao permitir que o usuário final assuma os controles do foco diretamente, um aplicativo pode fornecer mais controle artístico sobre a imagem feita.

Por exemplo, um fotógrafo profissional pode suavizar o foco de uma imagem para obter um [efeito bokeh](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Um efeito bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

Ou crie um [efeito de pull de foco](http://www.mediacollege.com/video/camera/focus/pull.html), como:

[![](intro-to-manual-camera-controls-images/image3.png "O efeito de pull de foco")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Para cientistas ou um escritor de aplicativos médicos, o aplicativo pode querer mover a lente de forma programática para os experimentos. De qualquer forma, a nova API permite que o usuário final ou o aplicativo assuma o controle do foco no momento em que a imagem é executada.

### <a name="how-focus-works"></a>Como o foco funciona

Antes de abordar os detalhes do controle do foco em um aplicativo IOS 8. Vamos dar uma olhada rápida em como o foco funciona em um dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Como o foco funciona em um dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

A luz entra na lente da câmera no dispositivo iOS e concentra-se em um sensor de imagem. A distância da lente do sensor controla onde o ponto focal (a área onde a imagem aparecerá mais nítida) é, em relação ao sensor. Quanto mais distante a lente for do sensor, os objetos de distância parecem mais nítidos e mais próximos, os objetos próximos parecem mais nítidos.

Em um dispositivo iOS, a lente é movida para perto, ou ainda, do sensor por ímãs e molas. Como resultado, o posicionamento exato da lente é impossível, pois ela varia de dispositivo para dispositivo e pode ser afetada por parâmetros como a orientação do dispositivo ou a idade do dispositivo e a primavera.

### <a name="important-focus-terms"></a>Termos de foco importantes

Ao lidar com o foco, há alguns termos com os quais o desenvolvedor deve estar familiarizado:

- **Profundidade do campo** – a distância entre os objetos em foco mais próximo e mais distante. 
- **Macro** -essa é a extremidade próxima do espectro de foco e é a distância mais próxima na qual a lente pode se concentrar.
- **Infinito** – esta é a extremidade distante do espectro de foco e é a distância mais distante na qual a lente pode se concentrar.
- **Hyperfocal Distance** – esse é o ponto no espectro de foco em que o objeto mais distante no quadro está apenas no final do foco. Em outras palavras, essa é a posição focal que maximiza a profundidade do campo. 
- **Posição da lente** – é isso que controla todos os outros termos acima. Essa é a distância da lente do sensor e, portanto, do controle de foco.


Com esses termos e conhecimento em mente, os novos controles de foco manual podem ser implementados com êxito em um aplicativo iOS 8.

### <a name="existing-focus-controls"></a>Controles de foco existentes

Ios 7 e versões anteriores, fornecidos controles de foco existentes por `FocusMode`meio de propriedade como:

- `AVCaptureFocusModeLocked`– O foco está bloqueado em um único ponto de foco.
- `AVCaptureFocusModeAutoFocus`– A câmera varre a lente por todos os pontos focal até encontrar um foco nítido e, em seguida, permanecer lá.
- `AVCaptureFocusModeContinuousAutoFocus`– A câmera se concentra sempre que detecta uma condição fora de foco.


Os controles existentes também forneciam um ponto de interesse configurável por meio`FocusPointOfInterest` da propriedade, para que o usuário possa tocar para se concentrar em uma área específica. O aplicativo também pode rastrear a movimentação de lentes monitorando `IsAdjustingFocus` a propriedade.

Além disso, a restrição de `AutoFocusRangeRestriction` intervalo foi fornecida pela propriedade como:

- `AVCaptureAutoFocusRangeRestrictionNear`– Restringe o foco para as profundidades adjacentes. Útil em situações como, por exemplo, digitalizar um código QR ou códigos de barras.
- `AVCaptureAutoFocusRangeRestrictionFar`– Restringe o foco para as camadas distantes. Útil em situações em que os objetos conhecidos por serem irrelevantes estão no campo de exibição (por exemplo, um quadro de janela).


Finalmente, há a `SmoothAutoFocus` propriedade que reduz o algoritmo de foco automático e o percorre em incrementos menores para evitar a movimentação de artefatos ao gravar vídeo.

### <a name="new-focus-controls-in-ios-8"></a>Novos controles de foco no iOS 8

Além dos recursos já fornecidos pelo iOS 7 e posterior, os recursos a seguir agora estão disponíveis para controlar o foco no iOS 8:

- Controle manual completo da posição da lente ao bloquear o foco.
- Observação de valor-chave da posição da lente em qualquer modo de foco.


Para implementar os recursos acima, a `AVCaptureDevice` classe foi modificada para incluir uma propriedade somente `LensPosition` leitura usada para obter a posição atual da lente da câmera.

Para assumir o controle manual da posição da lente, o dispositivo de captura deve estar no modo de foco bloqueado. Exemplo:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

O `SetFocusModeLocked` método do dispositivo de captura é usado para ajustar a posição da lente da câmera. Uma rotina de retorno de chamada opcional pode ser fornecida para receber notificações quando a alteração entrar em vigor. Exemplo:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Como visto no código acima, o dispositivo de captura deve ser bloqueado para configuração antes que uma alteração na posição da lente possa ser feita. Os valores válidos da posição da lente estão entre 0,0 e 1,0.

### <a name="manual-focus-example"></a>Exemplo de foco manual

Com o código de configuração geral da captura AV em vigor `UIViewController` , um pode ser adicionado ao storyboard do aplicativo e configurado da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image5.png "Um UIViewController pode ser adicionado ao storyboard dos aplicativos e configurado como mostrado aqui")](intro-to-manual-camera-controls-images/image5.png#lightbox)

A exibição contém os seguintes elementos principais:

- Um `UIImageView` que exibirá o feed de vídeo.
- Um `UISegmentedControl` que irá alterar o modo de foco de automático para bloqueado.
- Um `UISlider` que mostrará e atualizará a posição atual da lente.


Faça o seguinte para conectar o controlador de exibição para controle de foco manual:


1. Adicione as seguintes instruções using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Adicione as seguintes variáveis privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. Adicione as seguintes propriedades computadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Substitua o `ViewDidLoad` método e adicione o seguinte código:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Substitua o `ViewDidAppear` método e adicione o seguinte para iniciar a gravação quando a exibição for carregada:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Com a câmera no modo auto, o controle deslizante será movido automaticamente à medida que a câmera ajustar o foco:

    [![](intro-to-manual-camera-controls-images/image6.png "O controle deslizante será movido automaticamente conforme a câmera ajustar o foco neste aplicativo de exemplo")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Toque no segmento bloqueado e arraste o controle deslizante posição para ajustar a posição da lente manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Ajustando manualmente a posição da lente")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Pare o aplicativo.


O código acima mostrou como monitorar a posição da lente quando a câmera está no modo automático ou usar um controle deslizante para controlar a posição da lente quando ela estiver no modo bloqueado.

## <a name="manual-exposure"></a>Exposição manual

A exposição refere-se ao brilho de uma imagem em relação ao brilho da origem e é determinada pelo quanto a luz atinge o sensor, por quanto tempo e pelo nível de lucro do sensor (mapeamento ISO). Ao fornecer controle manual sobre a exposição, um aplicativo pode fornecer mais liberdade ao usuário final e permitir que eles atinjam uma aparência estilizada.

Usando os controles de exposição manual, o usuário pode tirar uma imagem de forma inrealista para escuro e Moody:

[![](intro-to-manual-camera-controls-images/image8.png "Um exemplo de imagem que mostra a exposição de forma inrealista ao escuro e Moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Novamente, isso pode ser feito automaticamente usando o controle programático para aplicativos científicos ou por meio de controles manuais fornecidos pela interface do usuário dos aplicativos. De qualquer forma, as novas APIs de exposição do iOS 8 fornecem um controle refinado sobre as configurações de exposição da câmera.

### <a name="how-exposure-works"></a>Como funciona a exposição

Antes de discutir os detalhes de controlar a exposição em um aplicativo IOS 8. Vamos dar uma olhada rápida em como funciona a exposição:

[![](intro-to-manual-camera-controls-images/image9.png "Como funciona a exposição")](intro-to-manual-camera-controls-images/image9.png#lightbox)

Os três elementos básicos que vêm juntos para controlar a exposição são:

- **Velocidade** do obturador – esse é o período de tempo que o obturador está aberto para permitir a luz do sensor de câmera. Quanto menor o tempo que o obturador está aberto, a menor luz é deixá-in e a imagem é mais nítida (menos desfoque de movimento). Quanto mais longo o obturador estiver aberto, mais claro será deixar em e quanto mais desfoque de movimento ocorrer.
- **Mapeamento ISO** – esse é um termo emprestado de fotografia de filmes e refere-se à sensibilidade das químicas no filme à luz. Valores ISO baixos em filme têm uma reprodução de cor menos granular e mais fina; valores ISO baixos em sensores digitais têm menos ruído de sensor, mas menos brilho. Quanto maior o valor ISO, mais brilhante a imagem, mas com mais ruído de sensor. "ISO" em um sensor digital é uma medida de [lucro eletrônico](https://en.wikipedia.org/wiki/Gain), não um recurso físico. 
- **Abertura de lentes** – esse é o tamanho da abertura da lente. Em todos os dispositivos iOS, a abertura da lente é fixa, portanto, os dois únicos valores que podem ser usados para ajustar a exposição são velocidade do obturador e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Como a exposição automática contínua funciona

Antes de aprender como a exposição manual funciona, é uma boa ideia entender como a exposição automática contínua funciona em um dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Como a exposição automática contínua funciona em um dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Primeiro, é o bloco de exposição automática, ele tem o trabalho de calcular a exposição ideal e está continuamente sendo alimentado com estatísticas de medição. Ele usa essas informações para calcular a combinação ideal de ISO e velocidade do obturador para que a cena fique bem acesa. Esse ciclo é chamado de loop AE.

### <a name="how-locked-exposure-works"></a>Como a exposição bloqueada funciona

Em seguida, vamos examinar como a exposição bloqueada funciona em dispositivos iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Como a exposição bloqueada funciona em dispositivos iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Novamente, você tem o bloco de exposição automática que está tentando calcular os valores ideais de iOS e duração. No entanto, nesse modo, o bloco AE é desconectado do mecanismo de estatísticas de medição.

### <a name="existing-exposure-controls"></a>Controles de exposição existentes

Ios 7 e posterior, forneça os seguintes controles de exposição existentes por `ExposureMode` meio da propriedade:

- `AVCaptureExposureModeLocked`– Amostra a cena uma vez e usa esses valores em toda a cena.
- `AVCaptureExposureModeContinuousAutoExposure`– Amostra a cena continuamente para garantir que ela esteja bem acesa.


O `ExposurePointOfInterest` pode ser usado para tocar para expor a cena selecionando um objeto de destino para expor, e o aplicativo pode monitorar a `AdjustingExposure` propriedade para ver quando a exposição está sendo ajustada.

### <a name="new-exposure-controls-in-ios-8"></a>Novos controles de exposição no iOS 8

Além dos recursos já fornecidos pelo iOS 7 e posterior, os recursos a seguir agora estão disponíveis para controlar a exposição no iOS 8:

- Exposição personalizada totalmente manual.
- Get, set e key-value observam o IOS e a velocidade do obturador (duração).


Para implementar os recursos acima, um novo `AVCaptureExposureModeCustom` modo foi adicionado. Quando a câmera no é o modo personalizado, o código a seguir pode ser usado para ajustar a duração da exposição e o ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

Nos modos automático e bloqueado, o aplicativo pode ajustar a tendência da rotina de exposição automática usando o seguinte código:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Os intervalos de configuração mínimo e máximo dependem do dispositivo em que o aplicativo está sendo executado, portanto, eles nunca devem ser embutidos em código. Em vez disso, use as seguintes propriedades para obter os intervalos de valor mínimo e máximo:

- `CaptureDevice.MinExposureTargetBias` 
- `CaptureDevice.MaxExposureTargetBias` 
- `CaptureDevice.ActiveFormat.MinISO` 
- `CaptureDevice.ActiveFormat.MaxISO` 
- `CaptureDevice.ActiveFormat.MinExposureDuration` 
- `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Como visto no código acima, o dispositivo de captura deve ser bloqueado para configuração antes que uma alteração na exposição possa ser feita.

### <a name="manual-exposure-example"></a>Exemplo de exposição manual

Com o código de configuração geral da captura AV em vigor `UIViewController` , um pode ser adicionado ao storyboard do aplicativo e configurado da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image12.png "Um UIViewController pode ser adicionado ao storyboard dos aplicativos e configurado como mostrado aqui")](intro-to-manual-camera-controls-images/image12.png#lightbox)

A exibição contém os seguintes elementos principais:

- Um `UIImageView` que exibirá o feed de vídeo.
- Um `UISegmentedControl` que irá alterar o modo de foco de automático para bloqueado.
- Quatro `UISlider` controles que irão mostrar e atualizar o deslocamento, a duração, a ISO e a tendência.


Faça o seguinte para conectar o controlador de exibição para controle de exposição manual:


1. Adicione as seguintes instruções using:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Adicione as seguintes variáveis privadas:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. Adicione as seguintes propriedades computadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Substitua o `ViewDidLoad` método e adicione o seguinte código:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Substitua o `ViewDidAppear` método e adicione o seguinte para iniciar a gravação quando a exibição for carregada:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Com a câmera no modo auto, os controles deslizantes serão movidos automaticamente à medida que a câmera ajustar a exposição:

    [![](intro-to-manual-camera-controls-images/image13.png "Os controles deslizantes serão movidos automaticamente à medida que a câmera ajustar a exposição")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Toque no segmento bloqueado e arraste o controle deslizante de tendência para ajustar a tendência da exposição automática manualmente:

    [![](intro-to-manual-camera-controls-images/image14.png "Ajustando a tendência da exposição automática manualmente")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Toque no segmento personalizado e arraste os controles deslizantes de duração e ISO para controlar manualmente a exposição:

    [![](intro-to-manual-camera-controls-images/image15.png "Arraste os controles deslizantes de duração e ISO para controlar manualmente a exposição")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Pare o aplicativo.


O código acima mostrou como monitorar as configurações de exposição quando a câmera está no modo automático e como usar os controles deslizantes para controlar a exposição quando está nos modos bloqueados ou personalizados.

## <a name="manual-white-balance"></a>Saldo de branco manual

Os controles de balanço de branco permitem que os usuários ajustem o saldo de colosr em uma imagem para que eles pareçam mais realistas. Fontes de luz diferentes têm temperaturas de cor diferentes e as configurações de câmera usadas para capturar uma imagem devem ser ajustadas para compensar essas diferenças. Novamente, ao permitir que o usuário controle o equilíbrio de branco, eles podem fazer ajustes profissionais de que as rotinas automáticas são incapazes de obter efeitos artísticos.

[![](intro-to-manual-camera-controls-images/image16.png "Uma imagem de exemplo mostrando os ajustes de balanço de branco manual")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Por exemplo, o horário de verão tem uma conversão azul, enquanto as luzes Tungsten incandescentes têm um tom mais quente e laranja amarelo. (Confusamente, as cores "frias" têm temperaturas de cor mais altas do que as cores "quentes". As temperaturas de cor são uma medida física, e não uma perceptiva.)

A mente humana é muito boa em compensar as diferenças na temperatura de cor, mas isso é algo que uma câmera não pode fazer. A câmera funciona aumentando a cor no espectro oposto para ajustar as diferenças de cor.

A nova API de exposição do iOS 8 permite que o aplicativo assuma o controle do processo e forneça controle refinado sobre as configurações de balanço de branco da câmera.

### <a name="how-white-balance-works"></a>Como o equilíbrio de branco funciona

Antes de discutir os detalhes do equilíbrio de branco de controle em um aplicativo IOS 8. Vamos dar uma olhada rápida em como o equilíbrio de branco funciona:

No estudo de percepção de cor, o espaço de cores [RGB 1931 de cie e o espaço de cores cie 1931 XYZ](https://en.wikipedia.org/wiki/CIE_1931_color_space) são os primeiros espaços de cores definidos matematicamente. Eles foram criados pela CIE (Comissão Internacional de iluminação) em 1931.

[![](intro-to-manual-camera-controls-images/image17.png "O espaço de cores RGB de CIE 1931 e o espaço de cores CIE 1931 XYZ")](intro-to-manual-camera-controls-images/image17.png#lightbox)

O gráfico acima mostra todas as cores visíveis para o olho humano, de azul profundo a verde brilhante a vermelho brilhante. Qualquer ponto no diagrama pode ser plotado com um valor X e Y, conforme mostrado no gráfico acima.

Como visível no grafo, há valores X e Y que podem ser plotados no grafo que estaria fora do intervalo de visão humana e, como resultado, essas cores não podem ser reproduzidas por uma câmera.

A curva menor no gráfico acima é chamada de [Planckian local](https://en.wikipedia.org/wiki/Planckian_locus), que expressa a temperatura de cor (em graus Kelvin), com números mais altos no lado azul (mais quente) e números menores no lado vermelho (resfriamento). Elas são úteis para situações de iluminação típicas.

Em condições de iluminação mista, os ajustes de balanço de branco precisarão se desviar do Planckian local para fazer as alterações necessárias. Nessas situações, o ajuste precisará ser deslocado para o lado verde ou vermelho/magenta da escala de CIE.

os dispositivos iOS compensam as conversões de cores aumentando o lucro de cor oposto. Por exemplo, se uma cena tiver muito azul, o lucro vermelho será aumentado para compensar. Esses valores de obter são calibrados para dispositivos específicos para que eles sejam dependentes do dispositivo.

### <a name="existing-white-balance-controls"></a>Controles de balanço de branco existentes

o Ios 7 e superior forneceu os seguintes controles de balanço `WhiteBalanceMode` de branco existentes por meio da propriedade:

- `AVCapture WhiteBalance ModeLocked`– Amostras da cena uma vez e usando esses valores em toda a cena.
- `AVCapture WhiteBalance ModeContinuousAutoExposure`– Amostra a cena continuamente para garantir que ela esteja bem equilibrada.


E o aplicativo pode monitorar a `AdjustingWhiteBalance` propriedade para ver quando a exposição está sendo ajustada.

### <a name="new-white-balance-controls-in-ios-8"></a>Novos controles de balanço de branco no iOS 8

Além dos recursos já fornecidos pelo iOS 7 e posterior, os seguintes recursos agora estão disponíveis para controlar o equilíbrio de branco no iOS 8:

- Controle totalmente manual dos ganhos de RGB do dispositivo.
- Get, set e key-value observam os ganhos de RGB do dispositivo.
- Suporte para balanço de branco usando cartão cinza.
- Rotinas de conversão de e para espaços de cores independentes de dispositivo.


Para implementar os recursos acima, a `AVCaptureWhiteBalanceGain` estrutura foi adicionada com os seguintes membros:

- `RedGain` 
- `GreenGain` 
- `BlueGain` 


O máximo de equilíbrio de branco é de quatro (4) no momento e pode estar `MaxWhiteBalanceGain` pronto a partir da propriedade. Portanto, o intervalo legal é de um (1) `MaxWhiteBalanceGain` a (4) no momento.

A `DeviceWhiteBalanceGains` propriedade pode ser usada para observar os valores atuais. Use `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` para ajustar os ganhos de saldo quando a câmera estiver no modo de balanço de branco bloqueado.

#### <a name="conversion-routines"></a>Rotinas de conversão

Rotinas de conversão foram adicionadas ao iOS 8 para ajudar na conversão de espaços de cores independentes de dispositivo. Para implementar as rotinas de conversão `AVCaptureWhiteBalanceChromaticityValues` , a estrutura foi adicionada com os seguintes membros:

- `X`-é um valor de 0 a 1.
- `Y`-é um valor de 0 a 1.


Uma `AVCaptureWhiteBalanceTemperatureAndTintValues` estrutura também foi adicionada com os seguintes membros:

- `Temperature`-é um valor de ponto flutuante em graus Kelvin.
- `Tint`-é um deslocamento de verde ou magenta de 0 a 150 com valores positivos na direção verde e negativo em direção no magenta.


Use os `CaptureDevice.GetTemperatureAndTintValues` `CaptureDevice.GetDeviceWhiteBalanceGains`métodos e para converter entre temperatura e tonalidade, desvio e espaços de cores de lucro de RGB.

> [!NOTE]
> As rotinas de conversão são mais precisas quanto mais próximo o valor a ser convertido é a local Planckian.




#### <a name="gray-card-support"></a>Suporte a cartão cinza

A Apple usa o termo cinza mundo para se referir ao suporte de cartão cinza incorporado ao iOS 8. Ele permite que o usuário se concentre em um cartão cinza físico que cobre pelo menos 50% do centro do quadro e o usa para ajustar o equilíbrio de branco. A finalidade do cartão cinza é obter branco que pareça neutro.

Isso pode ser implementado em um aplicativo solicitando que o usuário Coloque um cartão cinza físico na frente da câmera, monitorando a `GrayWorldDeviceWhiteBalanceGains` Propriedade e aguardando até que os valores sejam liquidados.

O aplicativo bloquearia os ganhos do equilíbrio de branco para `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` o método usando os valores `GrayWorldDeviceWhiteBalanceGains` da propriedade para aplicar as alterações.

O dispositivo de captura deve ser bloqueado para configuração antes que uma alteração no equilíbrio de branco possa ser feita.

### <a name="manual-white-balance-example"></a>Exemplo de balanço de branco manual

Com o código de configuração geral da captura AV em vigor `UIViewController` , um pode ser adicionado ao storyboard do aplicativo e configurado da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image18.png "Um UIViewController pode ser adicionado ao storyboard dos aplicativos e configurado como mostrado aqui")](intro-to-manual-camera-controls-images/image18.png#lightbox)

A exibição contém os seguintes elementos principais:

- Um `UIImageView` que exibirá o feed de vídeo.
- Um `UISegmentedControl` que irá alterar o modo de foco de automático para bloqueado.
- Dois `UISlider` controles que mostrarão e atualizarão a temperatura e a tonalidade.
- Um `UIButton` usado para exemplificar um espaço cinza (mundo cinza) e definir o equilíbrio de branco usando esses valores.


Faça o seguinte para conectar o controlador de exibição para controle de balanço de branco manual:


1. Adicione as seguintes instruções using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Adicione as seguintes variáveis privadas:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. Adicione as seguintes propriedades computadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Adicione o seguinte método particular para definir a nova temperatura e a tonalidade do balanço de branco:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. Substitua o `ViewDidLoad` método e adicione o seguinte código:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. Substitua o `ViewDidAppear` método e adicione o seguinte para iniciar a gravação quando a exibição for carregada:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Salve as alterações no código e execute o aplicativo.
1. Com a câmera no modo auto, os controles deslizantes serão movidos automaticamente à medida que a câmera ajustar o equilíbrio de branco:

    [![](intro-to-manual-camera-controls-images/image19.png "Os controles deslizantes serão movidos automaticamente à medida que a câmera ajustar o equilíbrio de branco")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Toque no segmento bloqueado e arraste os controles deslizantes de temp e de tonalidade para ajustar o balanço de branco manualmente:

    [![](intro-to-manual-camera-controls-images/image20.png "Arraste os controles deslizantes temporários e de tonalidade para ajustar o balanço de branco manualmente")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Com o segmento bloqueado ainda selecionado, coloque um cartão cinza físico na frente da câmera e toque no botão cartão cinza para ajustar o equilíbrio de branco ao mundo cinza:

    [![](intro-to-manual-camera-controls-images/image21.png "Toque no botão cartão cinza para ajustar o equilíbrio de branco ao mundo cinza")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Pare o aplicativo.

O código acima mostrou como monitorar as configurações de balanço de branco quando a câmera está no modo automático ou usar controles deslizantes para controlar o equilíbrio de branco quando ele está no modo bloqueado.

## <a name="bracketed-capture"></a>Captura entre colchetes

A captura entre colchetes é baseada nas configurações dos controles de câmera manuais apresentados acima e permite que o aplicativo Capture um momento no tempo, de várias maneiras diferentes.

Simplesmente declarado, a captura entre colchetes é uma intermitência de imagens ainda realizadas com uma variedade de configurações de imagem para imagem.

[![](intro-to-manual-camera-controls-images/image22.png "Como funciona a captura entre colchetes")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Usando a captura entre colchetes no iOS 8, um aplicativo pode predefinir uma série de controles de câmera manual, emitir um único comando e fazer com que a cena atual retorne uma série de imagens para cada uma das predefinições manuais.

### <a name="bracketed-capture-basics"></a>Noções básicas de captura entre colchetes

Novamente, a captura entre colchetes é uma intermitência de imagens ainda realizadas com configurações variadas de imagem para imagem. Os tipos de captura entre colchetes disponíveis são:

- **Colchete de exposição automática** – onde todas as imagens têm um valor de tendência variado.
- **Colchete de exposição manual** – onde todas as imagens têm uma velocidade de obturador (duração) variada e um valor ISO.
- **Colchete de intermitência simples** – uma série de imagens ainda realizadas em sucessão rápida.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Novos controles de captura entre colchetes no iOS 8

Todos os comandos de captura entre colchetes são `AVCaptureStillImageOutput` implementados na classe. Use o `CaptureStillImageBracket`método para obter uma série de imagens com a matriz de configurações determinada.

Duas novas classes foram implementadas para lidar com as configurações:

- `AVCaptureAutoExposureBracketedStillImageSettings`– Ele tem uma propriedade, `ExposureTargetBias`, usada para definir a tendência de um colchete de exposição automática. 
- `AVCaptureManual`  `ExposureBracketedStillImageSettings`– Ele tem duas propriedades `ExposureDuration` e `ISO`, usado para definir a velocidade do obturador e o ISO para um colchete de exposição manual. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Controles de captura entre colchetes são e não são

#### <a name="dos"></a>Do

Veja a seguir uma lista de coisas que devem ser feitas ao usar os controles de captura entre colchetes no iOS 8:

- Prepare o aplicativo para a pior situação de captura chamando o `PrepareToCaptureStillImageBracket` método.
- Suponha que os buffers de exemplo vão vir do mesmo pool compartilhado.
- Para liberar a memória que foi alocada por uma chamada de preparação anterior `PrepareToCaptureStillImageBracket` , chame novamente e envie uma matriz de um objeto.


#### <a name="donts"></a>Regras

Veja a seguir uma lista de coisas que não devem ser feitas ao usar os controles de captura entre colchetes no iOS 8:

- Não misture tipos de configurações de captura entre colchetes em uma única captura.
- Não solicite mais `MaxBracketedCaptureStillImageCount` de imagens em uma única captura.


### <a name="bracketed-capture-details"></a>Detalhes da captura entre colchetes

Os detalhes a seguir devem ser levados em consideração ao trabalhar com a captura entre colchetes no iOS 8:

- Configurações entre colchetes substituem `AVCaptureDevice` temporariamente as configurações.
- As configurações de estabilização do flash e da imagem ainda são ignoradas.
- Todas as imagens devem usar o mesmo formato de saída (JPEG, png, etc.)
- Visualização de vídeo pode descartar quadros.
- Há suporte para a captura entre colchetes em todos os dispositivos compatíveis com o iOS 8.


Com essas informações em mente, vamos dar uma olhada em um exemplo de uso da captura entre colchetes no iOS 8.

### <a name="bracket-capture-example"></a>Exemplo de captura de colchete

Com o código de configuração geral da captura AV em vigor `UIViewController` , um pode ser adicionado ao storyboard do aplicativo e configurado da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image23.png "Um UIViewController pode ser adicionado ao storyboard dos aplicativos e configurado como mostrado aqui")](intro-to-manual-camera-controls-images/image23.png#lightbox)

A exibição contém os seguintes elementos principais:

- Um `UIImageView` que exibirá o feed de vídeo.
- Três `UIImageViews` que exibirão os resultados da captura.
- Um `UIScrollView` para alojar as exibições de feed de vídeo e resultado.
- Um `UIButton` usado para fazer uma captura entre colchetes com algumas configurações predefinidas.


Faça o seguinte para conectar o controlador de exibição para captura entre colchetes:


1. Adicione as seguintes instruções using:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. Adicione as seguintes variáveis privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. Adicione as seguintes propriedades computadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. Adicione o seguinte método particular para criar as exibições de imagem de saída necessárias:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. Substitua o `ViewDidLoad` método e adicione o seguinte código:
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. Substitua o `ViewDidAppear` método e adicione o seguinte código:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. Salve as alterações no código e execute o aplicativo.
1. Quadro uma cena e toque no botão capturar colchete:

    [![](intro-to-manual-camera-controls-images/image24.png "Quadro uma cena e toque no botão de colchete de captura")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Passe o dedo da direita para a esquerda para ver as três imagens tiradas pela captura entre colchetes:

    [![](intro-to-manual-camera-controls-images/image25.png "Passe o dedo da direita para a esquerda para ver as três imagens tiradas pela captura entre colchetes")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Pare o aplicativo.


O código acima mostrou como configurar e pegar uma captura com colchetes de exposição automática no iOS 8.

## <a name="summary"></a>Resumo

Neste artigo, abordamos uma introdução aos novos controles de câmera manual fornecidos pelo iOS 8 e abordamos os conceitos básicos do que eles fazem e como eles funcionam. Nós fornecemos exemplos de foco manual, exposição manual e saldo branco manual. Finalmente, fornecemos um exemplo de uma captura com colchetes usando os controles de câmera manual abordados anteriormente

## <a name="related-links"></a>Links relacionados

- [ManualCameraControls (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualcameracontrols)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
