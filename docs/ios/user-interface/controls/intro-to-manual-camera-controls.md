---
title: Controles de câmera manuais no xamarin. IOS
description: Este documento descreve como o framework do iOS AVFoundation pode ser usado com xamarin. IOS para habilitar controles de câmera manuais. Controles de câmera manuais permitem que um usuário de configurações de exposição, balanço de branco e o foco do controle.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c61b3fee9009afb86ccd3fd0e16d7812a8e90feb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384056"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Controles de câmera manuais no xamarin. IOS

Os controles de câmera manuais, fornecida pelo `AVFoundation Framework` no iOS 8, que um aplicativo móvel para assumir controle total sobre a câmera de um dispositivo iOS. Esse nível refinado de controle pode ser usado para criar aplicativos de câmera de nível profissional e forneça composições artista ao ajustar os parâmetros da câmera ao mesmo tempo, uma imagem ou um vídeo ainda.

Esses controles também podem ser úteis ao desenvolver aplicativos científicos ou industriais, onde os resultados são menos destinados a correção ou a beleza da imagem e se destinam mais a realçar algum recurso ou elemento da imagem que está sendo executada.

## <a name="avfoundation-capture-objects"></a>Objetos de captura AVFoundation

Se colocar o vídeo ou imagens usando a câmera em um dispositivo iOS, o processo usado para capturar essas imagens é basicamente o mesmo. Isso é verdadeiro para aplicativos que usam os controles de câmera padrão automatizada ou aqueles que tiram proveito dos novos controles de câmera manuais:

 [![](intro-to-manual-camera-controls-images/image1.png "Visão geral de objetos da captura AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

Entrada provém de um `AVCaptureDeviceInput` em um `AVCaptureSession` por meio de um `AVCaptureConnection`. O resultado é a saída como uma imagem estática ou como um fluxo de vídeo. O processo inteiro é controlado por um `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Manuais controles fornecidos

Usando as novas APIs fornecidas pelo iOS 8, o aplicativo pode assumir o controle dos recursos de câmera a seguir:

-  **Foco manual** –, permitindo que o usuário final assumir o controle do foco diretamente, um aplicativo pode fornecer mais controle sobre a imagem extraída.
-  **Exposição manual** – fornecendo o controle manual sobre a exposição, um aplicativo pode fornecer mais liberdade para usuários e permitir que eles tenham uma aparência estilizada.
-  **Saldo de branco manual** – saldo em branco é usado para ajustar a cor de uma imagem — muitas vezes para que ela fique realista. Fontes de luz diferentes têm temperaturas de cor diferente e as configurações da câmera usadas para capturar uma imagem é ajustada para compensar essas diferenças. Novamente, permitindo que o usuário controle a proporção de branco, os usuários podem fazer ajustes que não podem ser feitos automaticamente.


iOS 8 fornece extensões e aprimoramentos para APIs para fornecer esse controle refinado sobre a imagem existentes do iOS o processo de captura.

## <a name="bracketed-capture"></a>Captura entre colchetes

A captura entre colchetes é baseada nas configurações dos controles de câmera manuais apresentadas acima e permite que o aplicativo capturar um momento específico, em uma variedade de maneiras diferentes.

Em poucas palavras, entre colchetes captura é uma intermitência de ainda imagens tiradas com uma variedade de configurações de imagem para a imagem.

## <a name="requirements"></a>Requisitos

A seguir é necessários para concluir as etapas apresentadas neste artigo:

-  **Xcode 7 + e iOS 8 ou mais recente** – do Apple Xcode 7 e iOS 8 ou APIs mais recentes precisarão ser instalado e configurado no computador do desenvolvedor.
-  **O Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
-  **iOS 8 dispositivo** – um dispositivo iOS executando a versão mais recente do iOS 8. Recursos de câmera não podem ser testados no simulador de iOS.


## <a name="general-av-capture-setup"></a>Captura de AV geral de instalação

Quando a gravação de vídeo em um dispositivo iOS, há alguns códigos de configuração geral que é sempre necessário. Esta seção abordará a configuração mínima necessária para gravar vídeo de câmera do dispositivo iOS e exibir esse vídeo em tempo real em um `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegado de Buffer de exemplo de saída

Uma das primeiras coisas que precisam estarão um delegado para monitorar o buffer de saída de exemplo e exibir uma imagem capturada no buffer para um `UIImageView` no aplicativo de interface do usuário.

A seguinte rotina monitorará o Buffer de exemplo e atualizar a interface do usuário:

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

Com essa rotina em vigor, o `AppDelegate` podem ser modificados para abrir uma sessão de captura de AV para gravar um feed de vídeo ao vivo.

### <a name="creating-an-av-capture-session"></a>Criar uma sessão de captura de AV

A sessão de captura de AV é usada para controlar a gravação de vídeo ao vivo da câmera do dispositivo iOS e é necessário para obter um vídeo em um aplicativo iOS. Desde o exemplo `ManualCameraControl` aplicativo de exemplo está usando a sessão de captura em vários lugares diferentes, ele será configurado no `AppDelegate` e disponibilizado para todo o aplicativo.

Faça o seguinte para modificar o aplicativo `AppDelegate` e adicione o código necessário:


1. Clique duas vezes o `AppDelegate.cs` arquivo no Gerenciador de soluções para abri-lo para edição.
1. Adicione o seguinte usando instruções na parte superior do arquivo:
    
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

1. Adicione as seguintes variáveis particulares e propriedades computadas para o `AppDelegate` classe:
    
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
  
1. Substitua o método concluído e altere-a para:
    
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


Com esse código em vigor, os controles de câmera manuais podem ser facilmente implementados para experimentação e teste.

## <a name="manual-focus"></a>Foco manual

Ao permitir que o usuário final entrem em contato diretamente com controles do foco, um aplicativo pode fornecer mais artístico controle sobre a imagem extraída.

Por exemplo, fotógrafa profissional pode suavizar o foco de uma imagem para obter um [Bokeh efeito](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Um efeito Bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

Ou, criar uma [foco Pull efeito](http://www.mediacollege.com/video/camera/focus/pull.html), tais como:

[![](intro-to-manual-camera-controls-images/image3.png "O efeito de Pull de foco")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Para cientistas ou um gravador de aplicativos médicos, o aplicativo talvez queira mover a lente em torno de experimentos de forma programática. De qualquer forma, a nova API permite que o usuário final ou o aplicativo para assumir controle foco no momento em que a imagem seja colocado.

### <a name="how-focus-works"></a>Como funciona o foco

Antes de discutir os detalhes de controle do foco em um aplicativo IOS 8. Vamos dar uma olhada rápida como foco funciona em um dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Como o foco funciona em um dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Luz insere a Lente de câmera no dispositivo iOS e se concentra em um sensor de imagem. A distância da lente do sensor de controla onde é o ponto Focal (a área em que a imagem será exibida a mais agudo), em relação ao sensor. Quanto mais distante das lentes é do sensor, os objetos de distância parecem mais brilhantes e quanto mais próximo, quase objetos parecem mais brilhantes.

Um dispositivo iOS, a Lente é movida mais próximo para ou mais longe, o sensor ímãs e springs. Como resultado, o posicionamento exato da lente é impossível, pois irá variar de um dispositivo para o dispositivo e pode ser afetada por parâmetros, como a orientação do dispositivo ou a idade do dispositivo e spring.

### <a name="important-focus-terms"></a>Termos importantes de foco

Ao lidar com foco, há alguns termos que o desenvolvedor deve estar familiarizado com:

-  **Profundidade do campo** – a distância entre os objetos mais próximos e mais distante em foco. 
-  **Macro** -esse é o próximo ao fim do espectro de foco e é a distância mais próxima em que a Lente possa se concentrar.
-  **Infinito** – essa é a extremidade do espectro de foco e é a distância mais distante no qual a Lente possa se concentrar.
-  **Distância Hyperfocal** – isso é o ponto no espectro de foco em que o objeto mais distante do quadro é apenas na extremidade oposta de foco. Em outras palavras, essa é a posição de focal que maximiza a profundidade do campo. 
-  **Posição de lente** – que é o que controla todas as opções acima outros termos. Isso é a distância da lente do sensor e, portanto, o controlador de foco.


Com esses termos e dados de conhecimento em mente, os novos controles de foco Manual pode ser implementados com êxito em um aplicativo iOS 8.

### <a name="existing-focus-controls"></a>Controles existentes do foco

iOS 7 e versões anteriores, fornecidas controles existentes do foco via `FocusMode`a propriedade como:

-   `AVCaptureFocusModeLocked` – O foco está bloqueado em um ponto único de foco.
-   `AVCaptureFocusModeAutoFocus` – A câmera varre a Lente por meio de todos os pontos de focal até que ele encontra o foco definido e, em seguida, permanece lá.
-   `AVCaptureFocusModeContinuousAutoFocus` – A câmera refocuses sempre que detecta uma condição de fora de foco.


Os controles existentes fornecidos também definível pelo ponto de interesse por meio de`FocusPointOfInterest` propriedade, para que o usuário pode tocar nos concentrar em uma área específica. O aplicativo também pode rastrear o movimento de lente monitorando o `IsAdjustingFocus` propriedade.

Além disso, a restrição de intervalo foi fornecida pelo `AutoFocusRangeRestriction` a propriedade como:

-   `AVCaptureAutoFocusRangeRestrictionNear` – Restringe o autofocus intensidades próximas. É útil em situações como digitalizar um código QR ou código de barras.
-   `AVCaptureAutoFocusRangeRestrictionFar` – Restringe o autofocus intensidades distantes. É útil em situações em que os objetos que são conhecidos como irrelevante no campo de visão (por exemplo, um quadro de janela).


Finalmente, há o `SmoothAutoFocus` propriedade que diminui o algoritmo de foco automaticamente e as etapas-lo em incrementos menores para evitar a movimentação de artefatos durante a gravação de vídeo.

### <a name="new-focus-controls-in-ios-8"></a>Novos controles de foco no iOS 8

Além de recursos já fornecidos pelo iOS 7 e superior, os recursos a seguir agora estão disponíveis para controlar o foco no iOS 8:

-  Controle manual completa da posição das lentes ao bloquear o foco.
-  Observação de chave-valor da posição das lentes em qualquer modo de foco.


Para implementar os recursos acima, o `AVCaptureDevice` classe foi modificado para incluir somente leitura `LensPosition` propriedade usada para obter a posição atual da Lente de câmera.

Para assumir o controle manual da Lente de posição, o dispositivo de captura deve ser no modo de foco bloqueado. Exemplo:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

O `SetFocusModeLocked` método do dispositivo de captura é usado para ajustar a posição da Lente de câmera. Uma rotina de retorno de chamada opcional pode ser fornecer ao receber notificação quando a alteração entra em vigor. Exemplo:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Como visto no código acima, o dispositivo de captura deve ser bloqueado para a configuração antes de uma alteração na posição de Lente pode ser feita. Os valores de posição de lente válidos são entre 0,0 e 1,0.

### <a name="manual-focus-example"></a>Exemplo de foco manual

Com o código de configuração de captura geral de AV em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image5.png "Um UIViewController pode ser adicionado aos aplicativos Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image5.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a transmissão de vídeo.
-  Um `UISegmentedControl` que irá alterar o modo de foco de automático para bloqueado.
-  Um `UISlider` que mostrará e atualize a posição atual de lente.


Faça o seguinte para o controlador de exibição de transmissão-up para controle de foco Manual:


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
  
1. Adicione as seguintes variáveis particulares:

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
  
1. Substituir o `ViewDidLoad` método e adicione o seguinte código:

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
  
1. Substituir o `ViewDidAppear` método e adicione o seguinte para iniciar a gravação quando o modo de exibição é carregado:

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
  
1. Com a câmera no modo Auto, o controle deslizante será movido automaticamente como a câmera ajusta o foco:

    [![](intro-to-manual-camera-controls-images/image6.png "O controle deslizante será movido automaticamente como a câmera ajusta o foco neste aplicativo de exemplo")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Toque o segmento bloqueado e arraste o controle deslizante de posição para ajustar a posição de lente manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Ajustar manualmente a posição de Lente")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Interrompa o aplicativo.


O código acima mostra como monitorar a posição de lente quando a câmera está no modo automático ou usar um controle deslizante para controlar a posição de lente quando ele estiver no modo bloqueado.

## <a name="manual-exposure"></a>Exposição manual

Exposição refere-se ao brilho de uma imagem em relação o brilho do código-fonte e é determinada pelo quanta luz atinge sensor, como long e pelo nível de ganho do sensor (mapeamento ISO). Fornecendo o controle manual sobre a exposição, um aplicativo pode fornecer mais liberdade para o usuário final e permitir que eles tenham uma aparência estilizada.

Usando os controles de exposição de Manual, o usuário pode executar uma imagem de forma irrealista brilhante em escuro e moody:

[![](intro-to-manual-camera-controls-images/image8.png "Um exemplo de imagem que mostra a exposição de forma irrealista brilhante para escuro e moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Novamente, isso pode ser feito automaticamente usando o controle programático para aplicativos científicos ou por meio de controles manuais fornecidos pela interface do usuário de aplicativos. De qualquer forma, o novo iOS 8 APIs de exposição fornecem controle refinado sobre as configurações de exposição da câmera.

### <a name="how-exposure-works"></a>Como funciona a exposição

Antes de discutir os detalhes de controle da exposição em um aplicativo IOS 8. Vamos dar uma olhada rápida como exposição funciona:

[![](intro-to-manual-camera-controls-images/image9.png "Como funciona a exposição")](intro-to-manual-camera-controls-images/image9.png#lightbox)

Os três elementos básicos que são reunidos para controlar a exposição são:

-  **Obturador velocidade** – esse é o período de tempo que obturador está aberto para permitir que a luz sobre o sensor da câmera. A mais curta o tempo que obturador estiver aberto, a menos luz é permitem que e é a imagem de nítidos (menos movimento desfoque). Quanto mais longa obturador estiver aberto, a luz mais let além e movimento mais desfoque ocorre.
-  **Mapeamento de ISO** – isso é um termo emprestado da fotografia de filme e refere-se a confidencialidade de produtos químicos no filme à luz. Valores de ISO de baixa no filme têm menor detalhamento e a reprodução de cores mais refinada; valores baixos de ISO nos sensores digitais têm menos ruído de sensor, mas menos brilho. Quanto maior o valor ISO, quanto mais brilhantes na imagem, mas com mais ruído do sensor. "ISO" em um sensor digital é uma medida da [ganho eletrônico](https://en.wikipedia.org/wiki/Gain), não é um recurso físico. 
-  **Abertura de lente** – esse é o tamanho da abertura das lentes. Em todos os dispositivos iOS a diafragma for corrigida, para que o somente dois valores que podem ser usados para ajustar a exposição do obturador velocidade e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Funciona como contínua de exposição automática

Antes de aprendizado de como funciona a exposição manual, é uma boa ideia compreender como contínua exposição de automático funciona em um dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Como funciona a exposição automática contínua em um dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Primeiro é o bloco de exposição automática, ele tem o trabalho de cálculo de exposição ideal e é feito continuamente estatísticas de medição. Ele usa essas informações para calcular a combinação ideal de ISO e do obturador velocidade para obter a cena bem iluminada. Esse ciclo é conhecido como o Loop AE.

### <a name="how-locked-exposure-works"></a>Funciona como bloqueado de exposição

Em seguida, vamos examinar como bloqueado works exposição em dispositivos iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Como bloqueado exposição funciona em dispositivos iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Novamente, você tem o bloco de exposição automática que está tentando calcular os valores de duração e o iOS ideal. No entanto, nesse modo, o bloco AE está desconectado do mecanismo de medição de estatísticas.

### <a name="existing-exposure-controls"></a>Controles de exposição existentes

iOS 7 e acima, forneça os seguintes controles existentes de exposição por meio de `ExposureMode` propriedade:

-   `AVCaptureExposureModeLocked` – Amostras da cena uma vez e usa esses valores ao longo da cena.
-   `AVCaptureExposureModeContinuousAutoExposure` – A cena continuamente para garantir que ele está bem aceso a amostra.


O `ExposurePointOfInterest` pode ser usado para toque para expor a cena, selecionando um objeto de destino para expor no, e o aplicativo pode monitorar o `AdjustingExposure` propriedade para ver quando exposição é ajustada.

### <a name="new-exposure-controls-in-ios-8"></a>Novos controles de exposição no iOS 8

Além de recursos já fornecidos pelo iOS 7 e superior, os recursos a seguir agora estão disponíveis para controlar a exposição no iOS 8:

-  Exposição personalizada totalmente manual.
-  Get, Set e chave-valor observam do obturador velocidade (duração) e IOS.


Para implementar os recursos acima, um novo `AVCaptureExposureModeCustom` modo foi adicionado. Quando a câmera no é o modo personalizado, o código a seguir pode ser usado para ajustar a duração de exposição e ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

Nos modos Auto e bloqueado, o aplicativo pode ajustar o desvio da rotina exposição automática usando o seguinte código:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Os intervalos de configuração de mínimo e máximo dependem do dispositivo que o aplicativo está em execução, para que eles devem nunca ser codificados. Em vez disso, use as propriedades a seguir para obter os intervalos de valores mínimo e máximo:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Como visto no código acima, o dispositivo de captura deve ser bloqueado para a configuração antes de uma alteração na exposição pode ser feita.

### <a name="manual-exposure-example"></a>Exemplo de exposição manual

Com o código de configuração de captura geral de AV em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image12.png "Um UIViewController pode ser adicionado aos aplicativos Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image12.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a transmissão de vídeo.
-  Um `UISegmentedControl` que irá alterar o modo de foco de automático para bloqueado.
-  Quatro `UISlider` controles que mostrará e atualizar o deslocamento, a duração, o ISO e o desvio.


Faça o seguinte para o controlador de exibição de transmissão-up para controle de exposição Manual:


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
  
1. Adicione as seguintes variáveis particulares:

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
  
1. Substituir o `ViewDidLoad` método e adicione o seguinte código:

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
  
1. Substituir o `ViewDidAppear` método e adicione o seguinte para iniciar a gravação quando o modo de exibição é carregado:

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
  
1. Com a câmera no modo automático, os controles deslizantes moverá automaticamente como a câmera ajusta exposição:

    [![](intro-to-manual-camera-controls-images/image13.png "Os controles deslizantes moverá automaticamente como a câmera ajusta exposição")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Toque o segmento bloqueado e arraste o controle deslizante de desvio para ajustar o desvio da exposição automática manualmente:

    [![](intro-to-manual-camera-controls-images/image14.png "Ajustando o desvio da exposição automática manualmente")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Toque o segmento personalizado e arraste os controles deslizantes de duração e ISO para controlar a exposição manualmente:

    [![](intro-to-manual-camera-controls-images/image15.png "Arraste os controles deslizantes de duração e ISO para controlar a exposição manualmente")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Interrompa o aplicativo.


O código acima mostra como monitorar as configurações de exposição quando a câmera está no modo automático e como usar os controles deslizantes para controlar a exposição quando ele estiver em modos de bloqueado ou personalizado.

## <a name="manual-white-balance"></a>Proporção de branco manual

Controles de balanço de branco permitem aos usuários ajustar o equilíbrio de colosr em uma imagem para torná-las mais realista. Fontes de luz diferentes têm temperaturas de cor diferente e as configurações da câmera usadas para capturar uma imagem devem ser ajustadas para compensar essas diferenças. Novamente, permitindo que o usuário controle a proporção de branco, eles podem tornar profissionais ajustes que as rotinas automática não são capazes de obter efeitos artísticos.

[![](intro-to-manual-camera-controls-images/image16.png "Uma imagem de exemplo que mostra os ajustes de balanço de branco Manual")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Por exemplo, o horário de verão tem uma conversão blueish, enquanto as luzes incandescentes de tungsten têm uma tonalidade mais frequente, laranja. (Erroneamente, cores "interessantes" têm maior temperaturas de cor que cores "passivos". Cor temperaturas são uma medida física, não uma percepção.)

A mente humana é muito boa para compensar as diferenças de temperatura de cor, mas isso é algo que não é possível uma câmera. A câmera funciona pelo aumento de cor no espectro oposto para ajustar as diferenças de cor.

O novo iOS 8 exposição API permite que o aplicativo assumir o controle do processo e fornecer controle refinado sobre as configurações de balanço de branco da câmera.

### <a name="how-white-balance-works"></a>Funciona como o branco de saldo

Antes de discutir os detalhes de controle de saldo em branco em um aplicativo IOS 8. Vamos dar uma olhada rápida em branco como funciona o saldo:

No estudo de percepção de cor, o [CIE 1931 RGB de cor espaço e o espaço de cor CIE 1931 XYZ](https://en.wikipedia.org/wiki/CIE_1931_color_space) são o primeiro matematicamente, espaços de cores. Eles foram criados pela comissão internacional de iluminação (CIE) em 1931.

[![](intro-to-manual-camera-controls-images/image17.png "O espaço de cores RGB CIE 1931 e CIE 1931 XYZ espaço de cores")](intro-to-manual-camera-controls-images/image17.png#lightbox)

O gráfico acima mostra todas as cores visíveis ao olho humano, dos níveis mais profundos de azul para verde brilhante para vermelho brilhante. Qualquer ponto no diagrama pode ser plotado com um valor de X e Y, conforme mostrado no gráfico acima.

Como visível no gráfico, há valores de X e Y que podem ser plotados no gráfico que seria fora do intervalo de visão humana e, como resultado, essas cores não podem ser reproduzidas por uma câmera.

A curva menor no gráfico acima é chamada de [Planckian local](https://en.wikipedia.org/wiki/Planckian_locus), que expressa a temperatura de cor (em graus kelvin) com números mais altos no lado do azul (mais quente) e inferior de números no lado do vermelho (mais frio). Elas são úteis para situações de iluminação típico.

Em condições de iluminação misto, os ajustes de balanço de branco precisa desviar-se o local de Planckian para fazer as alterações necessárias. Nessas situações, que o ajuste será necessário ser deslocados para verde ou vermelho/magenta lado o CIE dimensionar.

Dispositivos iOS compensam projeções de cores, aumentando o ganho de cor oposta. Por exemplo, se uma cena tem muita azul, o ganho de vermelho serão ser aumentado para compensar. Esses valores são calibrados para dispositivos específicos para que sejam dependentes do dispositivo de ganho.

### <a name="existing-white-balance-controls"></a>Controles existentes do saldo em branco

iOS 7 e acima fornecido os seguintes controles de balanço de branco existentes por meio de `WhiteBalanceMode` propriedade:

-   `AVCapture WhiteBalance ModeLocked` – Amostras de uma vez a cena e usar esses valores ao longo da cena.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` – Amostras de cena continuamente para garantir que ele também é balanceado.


E o aplicativo pode monitorar o `AdjustingWhiteBalance` propriedade para ver quando exposição é ajustada.

### <a name="new-white-balance-controls-in-ios-8"></a>Novos controles de saldo em branco no iOS 8

Além de recursos já fornecidos pelo iOS 7 e superior, os recursos a seguir agora estão disponíveis para controle de saldo em branco no iOS 8:

-  Obtém controle totalmente manual do dispositivo RGB.
-  Get, Set e chave-valor observar o dispositivo RGB ganha.
-  Suporte para o saldo em branco usando um cartão de cinza.
-  Rotinas de conversão para e de espaços de cores independentes de dispositivo.


Para implementar os recursos acima, o `AVCaptureWhiteBalanceGain` estrutura foi adicionada com os seguintes membros:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


O ganho de balanço de branco máximo está atualmente quatro (4) e pode ser pronta a partir de `MaxWhiteBalanceGain` propriedade. Portanto, o intervalo válido é de um (1) `MaxWhiteBalanceGain` (4) no momento.

O `DeviceWhiteBalanceGains` propriedade pode ser usada para observar os valores atuais. Use `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` ajustar o equilíbrio ganha quando a câmera está no modo de balanço de branco bloqueado.

#### <a name="conversion-routines"></a>Rotinas de conversão

Rotinas de conversão foram adicionadas ao iOS 8 para ajudar com a conversão para e de, espaços de cores independentes de dispositivo. Para implementar as rotinas de conversão, o `AVCaptureWhiteBalanceChromaticityValues` estrutura foi adicionada com os seguintes membros:

-   `X` -é um valor de 0 a 1.
-   `Y` -é um valor de 0 a 1.


Um `AVCaptureWhiteBalanceTemperatureAndTintValues` estrutura também foi adicionada com os seguintes membros:

-   `Temperature` -é flutuante valor de ponto em graus Kelvin.
-   `Tint` -é um deslocamento de verde ou magenta de 0 a 150 com valores positivos em direção a direção de verde e negativo em direção a magenta.


Use o `CaptureDevice.GetTemperatureAndTintValues`e o `CaptureDevice.GetDeviceWhiteBalanceGains`métodos para converter entre a temperatura e tom, cromaticidade e RGB obtém espaços de cores.

> [!NOTE]
> As rotinas de conversão são mais precisas quanto mais próximo o valor a ser convertido é o local de Planckian.




#### <a name="gray-card-support"></a>Suporte a cartões de cinza

Apple usa o termo mundo cinza para referir-se para o suporte de cartão de cinza embutido no iOS 8. Ele permite que o usuário para se concentrar em um cartão físico de cinza que abrange pelo menos 50% do centro do quadro e usa isso para ajustar o equilíbrio em branco. A finalidade do cartão cinza é em branco que aparece com neutralidade de atingir.

Isso pode ser implementado em um aplicativo, solicitando que o usuário coloque um cartão físico cinza na frente da câmera, monitorando o `GrayWorldDeviceWhiteBalanceGains` propriedade e esperar até que os valores acomode.

O aplicativo, em seguida, pode bloquear os ganhos de balanço de branco para o `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` método usando os valores da `GrayWorldDeviceWhiteBalanceGains` propriedade para aplicar as alterações.

O dispositivo de captura deve ser bloqueado para a configuração antes de uma alteração no saldo em branco pode ser feita.

### <a name="manual-white-balance-example"></a>Exemplo de balanço de branco manual

Com o código de configuração de captura geral de AV em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image18.png "Um UIViewController pode ser adicionado aos aplicativos Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image18.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a transmissão de vídeo.
-  Um `UISegmentedControl` que irá alterar o modo de foco de automático para bloqueado.
-  Dois `UISlider` controles que mostrará e atualizar a temperatura e tom.
-  Um `UIButton` usado para um espaço de cartão de cinza (mundo cinza) de exemplo e definir o saldo em branco usando esses valores.


Faça o seguinte para o controlador de exibição de transmissão-up Manual em branco para controle de saldo:


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
  
1. Adicione as seguintes variáveis particulares:

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
  
1. Adicione o seguinte método privado para definir o novo white equilibrar a temperatura e tonalidade:

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
  
1. Substituir o `ViewDidLoad` método e adicione o seguinte código:

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
  
1. Substituir o `ViewDidAppear` método e adicione o seguinte para iniciar a gravação quando o modo de exibição é carregado:

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
  
1. Salve as alterações de código e executar o aplicativo.
1. Com a câmera no modo automático, os controles deslizantes moverá automaticamente como a câmera ajusta proporção de branco:

    [![](intro-to-manual-camera-controls-images/image19.png "Os controles deslizantes moverá automaticamente como a câmera ajusta proporção de branco")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Toque o segmento bloqueado e arraste os controles deslizantes Temp e tom para ajustar o equilíbrio de branco manualmente:

    [![](intro-to-manual-camera-controls-images/image20.png "Arraste os controles deslizantes Temp e tom para ajustar o equilíbrio de branco manualmente")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Com o segmento bloqueado ainda selecionado, coloque um cartão físico cinza na frente da câmera e toque no botão de cartão de cinza para ajustar o equilíbrio de branco para o mundo cinza:

    [![](intro-to-manual-camera-controls-images/image21.png "Toque no botão de cartão de cinza para ajustar o equilíbrio de branco para o mundo de cinza")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Interrompa o aplicativo.

O código acima mostra como monitorar as configurações de balanço de branco quando a câmera está no modo automático ou usar os controles deslizantes para controlar o equilíbrio em branco quando ele estiver no modo bloqueado.

## <a name="bracketed-capture"></a>Captura entre colchetes

A captura entre colchetes é baseada nas configurações dos controles de câmera manuais apresentadas acima e permite que o aplicativo capturar um momento específico, em uma variedade de maneiras diferentes.

Em poucas palavras, entre colchetes captura é uma intermitência de ainda imagens tiradas com uma variedade de configurações de imagem para a imagem.

[![](intro-to-manual-camera-controls-images/image22.png "Como funciona a capturar entre colchetes")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Usando a captura entre colchetes no iOS 8, um aplicativo pode predefinir uma série de controles de câmera manuais, emitir um comando único e têm a cena atual retornar uma série de imagens para cada uma das predefinições de manual.

### <a name="bracketed-capture-basics"></a>Noções básicas de captura entre colchetes

Novamente, entre colchetes de captura é uma intermitência de ainda imagens tiradas com configurações variadas de imagem para imagem. Os tipos de entre colchetes capturar disponíveis são:

-  **Auto exposição colchete** – em que todas as imagens têm uma quantidade de Bias variada.
-  **Colchete de exposição manual** – em que todas as imagens têm uma variadas do obturador velocidade (duração) e ISO quantidade.
-  **Colchete de intermitente simples** – uma série de imagens estáticas executadas em sucessão rápida.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Novos entre colchetes capturar controles no iOS 8

Todos os comandos de captura entre colchetes são implementados no `AVCaptureStillImageOutput` classe. Use o `CaptureStillImageBracket`método para obter uma série de imagens com a matriz de configurações fornecida.

Duas novas classes foram implementadas para lidar com as configurações:

-   `AVCaptureAutoExposureBracketedStillImageSettings` – Ele tem uma propriedade, `ExposureTargetBias`, usado para definir o desvio para um colchete de exposição automática. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – Ele tem duas propriedades, `ExposureDuration` e `ISO`, usado para definir a velocidade do obturador e ISO para um colchete de exposição manual. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Regras de controla a captura entre colchetes

#### <a name="dos"></a>Tarefas pendentes

A seguir está uma lista de coisas que deve ser feita quando o uso da captura entre colchetes controla no iOS 8:

-  Preparar o aplicativo para a captura pior situação chamando o `PrepareToCaptureStillImageBracket` método.
-  Suponha que os buffers de exemplo serão vêm do mesmo pool compartilhado.
-  Para liberar a memória que foi alocada por uma chamada anterior de preparação, chamar `PrepareToCaptureStillImageBracket` novamente e enviá-lo em uma matriz de um objeto.


#### <a name="donts"></a>Dicas sobre

A seguir está uma lista de coisas que não deve ser feita quando o uso da captura entre colchetes controla no iOS 8:

-  Não misture entre colchetes capturar tipos de configurações em uma única captura.
-  Não solicitar mais de `MaxBracketedCaptureStillImageCount` imagens em uma única captura.


### <a name="bracketed-capture-details"></a>Detalhes de captura entre colchetes

Os seguintes detalhes devem ser levados em consideração ao trabalhar com a captura de entre colchetes no iOS 8:

-  Configurações entre colchetes substituem temporariamente o `AVCaptureDevice` configurações.
-  Configurações de estabilização da imagem Flash e ainda são ignoradas.
-  Todas as imagens devem usar o mesmo formato de saída (jpeg, png, etc.)
-  Visualização de vídeo pode descartar quadros.
-  Captura entre colchetes é compatível com todos os dispositivos compatíveis com iOS 8.


Com essas informações em mente, vamos dar uma olhada em um exemplo de como usar o entre colchetes capturar no iOS 8.

### <a name="bracket-capture-example"></a>Exemplo de captura de colchete

Com o código de configuração de captura geral de AV em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image23.png "Um UIViewController pode ser adicionado aos aplicativos Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image23.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a transmissão de vídeo.
-  Três `UIImageViews` que exibirá os resultados da captura.
-  Um `UIScrollView` para abrigar o feed de vídeo e modos de exibição de resultado.
-  Um `UIButton` usado para tirar uma captura entre colchetes com algumas configurações predefinidas.


Faça o seguinte para o controlador de exibição de transmissão-up para capturar entre colchetes:


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
  
1. Adicione as seguintes variáveis particulares:

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
  
1. Adicione o seguinte método particular para criar os modos de exibição de imagem de saída necessária:

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
  
1. Substituir o `ViewDidLoad` método e adicione o seguinte código:
    
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
    
  
1. Substituir o `ViewDidAppear` método e adicione o seguinte código:

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
    
1. Salve as alterações de código e executar o aplicativo.
1. Uma cena de quadro e toque no botão de colchete capturar:

    [![](intro-to-manual-camera-controls-images/image24.png "Uma cena de quadro e toque no botão de captura de colchete")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Passe o dedo para direita para a esquerda para ver as três imagens tomadas por capturar entre colchetes:

    [![](intro-to-manual-camera-controls-images/image25.png "Passe o dedo para direita para a esquerda para ver as três imagens tomadas por capturar entre colchetes")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Interrompa o aplicativo.


O código acima mostra como configurar e levar um automática exposição entre colchetes capturar no iOS 8.

## <a name="summary"></a>Resumo

Neste artigo temos coberto uma introdução para os novos controles de câmera manuais fornecido pelo iOS 8 e abordou os conceitos básicos do que eles fazem e como eles funcionam. Podemos dar exemplos de foco Manual, Manual de exposição e balanço de branco Manual. Por fim, demos um exemplo levando um entre colchetes capturar usando os controles de câmera manuais discutido anteriormente

## <a name="related-links"></a>Links relacionados

- [ManualCameraControls (amostra)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
