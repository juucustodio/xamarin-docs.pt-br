---
title: Controles de câmera manual
description: A estrutura AVFoundation torna mais fácil do que nunca para os usuários a tirar fotos grandes, permitindo para controles de câmera Manual. Usando essa estrutura, um aplicativo pode tirar controle direto sobre o foco da câmera, equilíbrio de branco e as configurações de exposição. Um aplicativo também pode usar a captura de exposição entre colchetes para capturar automaticamente imagens com diferentes configurações de exposição. Este artigo será dar uma olhada rápida usando os controles de câmera Manual em um aplicativo móvel iOS simples 8.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 8545dce1b9232e396c4c9e71ad5f20649eef2417
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="manual-camera-controls"></a>Controles de câmera manual

_A estrutura AVFoundation torna mais fácil do que nunca para os usuários a tirar fotos grandes, permitindo para controles de câmera Manual. Usando essa estrutura, um aplicativo pode tirar controle direto sobre o foco da câmera, equilíbrio de branco e as configurações de exposição. Um aplicativo também pode usar a captura de exposição entre colchetes para capturar automaticamente imagens com diferentes configurações de exposição. Este artigo será dar uma olhada rápida usando os controles de câmera Manual em um aplicativo móvel iOS simples 8._

Os controles de câmera Manual, fornecida pelo `AVFoundation Framework` no iOS 8, permitir que um aplicativo móvel assumir o controle completo sobre a câmera de um dispositivo iOS. Esse nível refinado de controle pode ser usado para criar aplicativos de câmera nível professional e fornecer composições artista ajustando os parâmetros da câmera ao tirar uma imagem ou um vídeo ainda.

Esses controles também podem ser úteis ao desenvolver aplicativos científicos ou industriais, onde os resultados são menos voltados para a correção ou beleza da imagem e se destinam mais a realce algum recurso ou um elemento da imagem que está sendo executada.

## <a name="avfoundation-capture-objects"></a>Objetos de captura AVFoundation

Se colocar o vídeo ou imagens usando a câmera em um dispositivo iOS, o processo usado para capturar essas imagens é basicamente o mesmo. Isso é verdadeiro para aplicativos que usam os controles de câmera padrão automatizada ou aqueles que aproveitam os novos controles de câmera Manual:

 [![](intro-to-manual-camera-controls-images/image1.png "Visão geral de objetos de capturar AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

Entrada é obtida uma `AVCaptureDeviceInput` em uma `AVCaptureSession` por meio de um `AVCaptureConnection`. O resultado é a saída como uma imagem estática ou como um fluxo de vídeo. Todo o processo é controlado por um `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Controles manuais fornecidos

Usando as novas APIs fornecidas pelo iOS 8, o aplicativo pode assumir o controle dos recursos de câmera a seguir:

-  **Foco manual** –, permitindo que o usuário final assumir o controle do foco diretamente, um aplicativo pode fornecer mais controle sobre a imagem tomada.
-  **Exposição manual** – fornecendo controlar manualmente a exposição de, um aplicativo pode fornecer mais liberdade para usuários e permiti-las obter uma aparência estilizada.
-  **Balanço de branco manual** – balanço de branco é usado para ajustar as cores em uma imagem — geralmente para torná-lo realista. Fontes de luz diferentes têm temperaturas de cor diferente, e as configurações de câmera usadas para capturar uma imagem é ajustado para compensar essas diferenças. Novamente, permitindo o controle de usuário sobre a proporção de branco, os usuários podem fazer ajustes que não podem ser feitos automaticamente.


iOS 8 fornece extensões e aprimoramentos para iOS existente APIs para fornecer esse controle refinado sobre a imagem do processo de captura.

## <a name="bracketed-capture"></a>Captura entre colchetes

A captura de colchetes é baseada nas configurações dos controles de câmera Manual apresentada acima e permite que o aplicativo capturar um ponto no tempo, em uma variedade de maneiras diferentes.

Simplificando, colchetes de captura é uma intermitência de imagens tiradas com uma variedade de configurações de imagem para a imagem.

## <a name="requirements"></a>Requisitos

A seguir é necessárias para concluir as etapas apresentadas neste artigo:

-  **O Xcode 7 + e iOS 8 ou mais recente** – da Apple Xcode 7 e iOS 8 ou mais recente APIs precisa ser instalado e configurado no computador do desenvolvedor.
-  **O Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada e configurada no dispositivo do usuário.
-  **iOS 8 dispositivo** – um dispositivo iOS executando a versão mais recente do iOS 8. Os recursos da câmera não podem ser testados no simulador iOS.


## <a name="general-av-capture-setup"></a>Configuração de captura de AV geral

Durante a gravação de vídeo em um dispositivo iOS, há um código de configuração geral é sempre necessário. Esta seção aborda a instalação mínima necessária para gravar vídeo da câmera do dispositivo iOS e exibir esse vídeo em tempo real em um `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Delegado de Buffer de exemplo de saída

Uma das primeiras etapas que necessário será um delegado para monitorar o buffer de saída de exemplo e exibir uma imagem capturada no buffer para um `UIImageView` no aplicativo de interface do usuário.

A rotina a seguir irá monitorar o Buffer de exemplo e atualizar a interface do usuário:

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

Com esta rotina em vigor, o `AppDelegate` pode ser modificado para abrir uma sessão de captura de AV para gravar um feed de vídeo ao vivo.

### <a name="creating-an-av-capture-session"></a>Criar uma sessão de captura de AV

A sessão de captura de AV é usada para controlar a gravação de vídeo ao vivo da câmera do dispositivo iOS e é necessário para obter um vídeo em um aplicativo iOS. Desde o exemplo `ManualCameraControl` aplicativo de exemplo está usando a sessão de captura em vários lugares diferentes, ele será configurado o `AppDelegate` e disponibilizados para o aplicativo inteiro.

Faça o seguinte para modificar o aplicativo `AppDelegate` e adicione o código necessário:


1. Clique duas vezes o `AppDelegate.cs` arquivo no Gerenciador de soluções para abri-lo para edição.
1. Adicione o seguinte usando instruções para a parte superior do arquivo:
    
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

1. Adicione as seguintes variáveis particulares e propriedades computadas a `AppDelegate` classe:
    
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
  
1. Substitua o método terminar e altere-a para:
    
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


Com esse código, os controles de câmera Manual pode ser facilmente implementados para experimentação e teste.

## <a name="manual-focus"></a>Foco manual

Ao permitir que o usuário final se controles do foco diretamente, um aplicativo pode fornecer artístico mais controle sobre a imagem tomada.

Por exemplo, fotógrafos profissionais podem suavizar o foco de uma imagem para alcançar um [Bokeh efeito](http://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Um efeito de Bokeh")](intro-to-manual-camera-controls-images/image2.png#lightbox)

Criar um [foco Pull efeito](http://www.mediacollege.com/video/camera/focus/pull.html), tais como:

[![](intro-to-manual-camera-controls-images/image3.png "O efeito de Pull de foco")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Cientistas ou um gravador de aplicativos médicos, o aplicativo talvez queira programaticamente mover a lente para experiências. De qualquer forma, a nova API permite que o usuário final ou o aplicativo para assumir o controle sobre o foco no momento em que a imagem é tomado.

### <a name="how-focus-works"></a>Como funciona o foco

Antes de discutir os detalhes de controle do foco em um aplicativo do IOS 8. Vamos dar uma olhada rápida como foco funciona em um dispositivo iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Como o foco funciona em um dispositivo iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Claro insere a lente da câmera no dispositivo iOS e se concentra em um sensor de imagem. A distância da lente do sensor controla onde é o ponto Focal (a área onde a imagem será exibido o mais nítido), em relação ao sensor. Quanto mais distante a Lente é do sensor, objetos de distância parecerem mais nítidos e quanto mais próximo, próximo a objetos parecer mais nítido.

Um dispositivo iOS, a Lente é movida mais próximo para ou do sensor de atração nossa e molas. Como resultado, o posicionamento exato da lente é impossível, como variam de dispositivo e pode ser afetada por parâmetros, como a orientação do dispositivo ou a idade do dispositivo e spring.

### <a name="important-focus-terms"></a>Termos de foco importante

Ao lidar com foco, há alguns termos que o desenvolvedor deve estar familiarizado com:

-  **Profundidade do campo** – a distância entre os objetos mais próximos e mais em foco. 
-  **Macro** -este é o próximo ao fim do espectro de foco e é a distância mais próxima em que o aplicativo de fotos pode se concentrar.
-  **Infinito** – essa é a extremidade do espectro de foco e é a distância mais distante no qual o aplicativo de fotos pode se concentrar.
-  **Distância Hyperfocal** – isso é o ponto no espectro foco onde o objeto mais distante do quadro é apenas na extremidade oposta de foco. Em outras palavras, esta é a posição focal que maximiza a profundidade do campo. 
-  **Posição de lente** – que é o que controla todas as opções acima outros termos. Essa é a distância da lente do sensor e, portanto, o controlador de foco.


Com esses termos e dados de conhecimento em mente, os novos controles de foco Manual pode ser implementados com êxito em um aplicativo do iOS 8.

### <a name="existing-focus-controls"></a>Controles de foco existentes

iOS 7 e versões anteriores, fornecidos controles foco existentes por meio de `FocusMode`a propriedade como:

-   `AVCaptureFocusModeLocked` – O foco está bloqueado em um ponto único de foco.
-   `AVCaptureFocusModeAutoFocus` – A câmera varre a Lente por meio de todos os pontos de referência até localizar o foco e, em seguida, permanece lá.
-   `AVCaptureFocusModeContinuousAutoFocus` – A câmera refocuses sempre que detecta uma condição de fora de foco.


Os controles existentes fornecidos também definível pelo ponto de interesse por meio de`FocusPointOfInterest` propriedade, para que o usuário pode tocar se concentrar em uma área específica. O aplicativo também pode acompanhar o movimento de lente monitorando o `IsAdjustingFocus` propriedade.

Além disso, a restrição de intervalo foi fornecida pelo `AutoFocusRangeRestriction` a propriedade como:

-   `AVCaptureAutoFocusRangeRestrictionNear` – Restringe o autofocus intensidades próximas. É útil em situações como digitalizar um código QR ou código de barras.
-   `AVCaptureAutoFocusRangeRestrictionFar` – Restringe o autofocus intensidades distantes. É útil em situações em que os objetos que são conhecidos como irrelevantes são no campo de visualização (por exemplo, um quadro de janela).


Por fim há o `SmoothAutoFocus` propriedade que diminui o algoritmo de foco automática e as etapas a ele em incrementos menores para evitar mover artefatos durante a gravação de vídeo.

### <a name="new-focus-controls-in-ios-8"></a>Novos controles de foco do iOS 8

Além dos recursos já fornecidos pelo iOS 7 e posterior, os recursos a seguir agora estão disponíveis para controlar o foco no iOS 8:

-  Controle completo manual da posição Lente ao bloquear o foco.
-  Observação de chave-valor da posição lente em qualquer modo de foco.


Para implementar os recursos acima, o `AVCaptureDevice` classe foi modificada para incluir somente leitura `LensPosition` propriedade usada para obter a posição atual da Lente de câmera.

Para controlar a posição de lente manual, o dispositivo de captura deve ser no modo foco bloqueado. Exemplo:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

O `SetFocusModeLocked` método do dispositivo de captura é usado para ajustar a posição da Lente de câmera. Uma rotina de retorno de chamada opcional pode ser fornecida para receber notificação quando a alteração entra em vigor. Exemplo:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Conforme visto no código acima, o dispositivo de captura deve ser bloqueado para a configuração antes de uma alteração na posição de Lente pode ser feita. Os valores de posição de lente válidos são entre 0,0 e 1,0.

### <a name="manual-focus-example"></a>Exemplo de foco manual

Com o código de configuração de captura de AV geral em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image5.png "Um UIViewController pode ser adicionado aos aplicativos de Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image5.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a alimentação de vídeo.
-  Um `UISegmentedControl` isso alterará o modo de foco de automática para bloqueado.
-  Um `UISlider` que mostrará e atualize a posição atual de lente.


Faça o seguinte para transmissão-se o controlador de exibição para controle do foco Manual:


1. Adicione o seguinte usando instruções:

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
  
1. Com a câmera no modo automático, o controle deslizante moverá automaticamente como a câmera ajusta o foco:

    [![](intro-to-manual-camera-controls-images/image6.png "O controle deslizante moverá automaticamente como a câmera ajusta o foco neste aplicativo de exemplo")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. O segmento bloqueado de toque e arraste o controle deslizante de posição para ajustar a posição de lente manualmente:

    [![](intro-to-manual-camera-controls-images/image7.png "Ajuste manualmente a posição de Lente")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Interrompa o aplicativo.


O código acima mostra como monitorar a posição de lente quando a câmera está no modo automático ou usar um controle deslizante para controlar a posição de lente quando ele estiver no modo bloqueado.

## <a name="manual-exposure"></a>Exposição manual

Exposição refere-se para o brilho de uma imagem em relação o brilho da origem e é determinada pelo quanta luz atinge o sensor, como tempo e o nível de ganho do sensor (mapeamento ISO). Fornecendo controlar manualmente a exposição de, um aplicativo pode fornecer mais liberdade para o usuário final e permiti-las obter uma aparência estilizada.

Usando os controles de exposição Manual, o usuário pode ter uma imagem de inacreditavelmente claro para escuro e moody:

[![](intro-to-manual-camera-controls-images/image8.png "Um exemplo de uma imagem mostrando exposição de inacreditavelmente claro para escuro e moody")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Novamente, isso pode ser feito automaticamente usando o controle programático de aplicativos científicos ou por meio de controles manuais fornecidos pela interface do usuário de aplicativos. De qualquer forma, o novo iOS 8 APIs de exposição fornecem um controle refinado sobre as configurações de exposição da câmera.

### <a name="how-exposure-works"></a>Como funciona a exposição

Antes de discutir os detalhes de controle de exposição em um aplicativo do IOS 8. Vamos dar uma olhada rápida como funciona a exposição:

[![](intro-to-manual-camera-controls-images/image9.png "Como funciona a exposição")](intro-to-manual-camera-controls-images/image9.png#lightbox)

Os três elementos básicos que vir juntos para controlar a exposição são:

-  **Redimensionamento velocidade** – este é o período de tempo que o redimensionamento está aberto para deixar claro para o sensor de câmera. Quanto menor o tempo que de redimensionamento estiver aberto, a luz menos permitem e é a imagem de nítidos (menos Desfoque de movimento). Quanto maior o redimensionamento estiver aberto, mais claro é permitem no e mais de movimento desfoque ocorre.
-  **Mapeamento de ISO** – este é um termo emprestado de fotografia de filme e se refere a sensibilidade de chemicals no filme à luz. Valores de baixa ISO em filme têm menos detalhamento e reprodução de cores maior; valores baixos de ISO em sensores digitais têm menos ruído sensor, mas menos brilho. Quanto maior o valor ISO, quanto mais brilhante a imagem, mas com mais ruído de sensor. "ISO" de um sensor digital é uma medida de [ganho eletrônico](http://en.wikipedia.org/wiki/Gain), não é um recurso físico. 
-  **Abertura de lente** – esse é o tamanho da abertura lente. Em todos os dispositivos iOS a abertura de lente é fixa, portanto apenas dois valores que podem ser usados para ajustar a exposição de velocidade de redimensionamento e ISO.


### <a name="how-continuous-auto-exposure-works"></a>Funciona como contínua de exposição automática

Antes de aprendizado de como funciona a exposição manual, é uma boa ideia para entender como contínua exposição de auto funciona em um dispositivo iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Como funciona a exposição automática contínua em um dispositivo iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

A primeira é o bloco de exposição automática, ela tem o trabalho de cálculo exposição ideal e continuamente está sendo alimentada estatísticas de medição. Ele usa essas informações para calcular a combinação ideal de ISO e velocidade de redimensionamento para obter a cena bem acenda. Esse ciclo é conhecido como o Loop AE.

### <a name="how-locked-exposure-works"></a>Funciona de exposição como bloqueada

Em seguida, vamos examinar como bloqueado funciona exposição em dispositivos iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Como bloqueado exposição funciona em dispositivos iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Novamente, você tem o bloco de exposição automática que está tentando para calcular os valores de duração e iOS ideal. No entanto, nesse modo o bloco AE está desconectado do mecanismo de estatísticas de medição.

### <a name="existing-exposure-controls"></a>Controles de exposição existentes

iOS 7 e acima, forneça os seguintes controles de exposição existentes por meio de `ExposureMode` propriedade:

-   `AVCaptureExposureModeLocked` – Exemplos cena uma vez e usa esses valores ao longo da cena.
-   `AVCaptureExposureModeContinuousAutoExposure` – Exemplos da cena continuamente para garantir que ele está bem aceso.


O `ExposurePointOfInterest` pode ser usado para toque para expor a cena selecionando um objeto de destino para expor no, e o aplicativo pode monitorar o `AdjustingExposure` propriedade para ver quando exposição está sendo ajustada.

### <a name="new-exposure-controls-in-ios-8"></a>Novos controles de exposição do iOS 8

Além dos recursos já fornecidos pelo iOS 7 e posterior, os recursos a seguir agora estão disponíveis para controlar a exposição no iOS 8:

-  Exposição personalizada totalmente manual.
-  Get, Set e chave-valor observam IOS e a velocidade de redimensionamento (duração).


Para implementar os recursos acima, uma nova `AVCaptureExposureModeCustom` modo foi adicionado. Quando a câmera no é o modo personalizado, o código a seguir pode ser usado para ajustar a duração de exposição e ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

Os modos Auto e bloqueado, o aplicativo pode ajustar a diferença da rotina de exposição automática usando o seguinte código:

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


Conforme visto no código acima, o dispositivo de captura deve ser bloqueado para a configuração antes de uma alteração na exposição pode ser feita.

### <a name="manual-exposure-example"></a>Exemplo de exposição manual

Com o código de configuração de captura de AV geral em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image12.png "Um UIViewController pode ser adicionado aos aplicativos de Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image12.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a alimentação de vídeo.
-  Um `UISegmentedControl` isso alterará o modo de foco de automática para bloqueado.
-  Quatro `UISlider` controles que mostrará e atualizar o deslocamento, a duração, o ISO e a tendência.


Faça o seguinte para o controlador de exibição de backup durante a transmissão para controle de exposição Manual:


1. Adicione o seguinte usando instruções:
    
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
1. O segmento bloqueado de toque e arraste o controle deslizante de ajuste para ajustar a diferença da exposição automática manualmente:

    [![](intro-to-manual-camera-controls-images/image14.png "Ajustando a diferença da exposição automática manualmente")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. O segmento personalizada de toque e arraste os controles deslizantes ISO e a duração para controlar manualmente a exposição:

    [![](intro-to-manual-camera-controls-images/image15.png "Arraste os controles deslizantes duração e ISO para controlar a exposição manualmente")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Interrompa o aplicativo.


O código acima mostra como monitorar as configurações de exposição quando a câmera está no modo automático e como usar os controles deslizantes para controlar a exposição quando ele estiver em modos de bloqueado ou personalizado.

## <a name="manual-white-balance"></a>Proporção de branco manual

Controles de balanço de branco permitem que os usuários ajustar o equilíbrio de colosr em uma imagem para torná-las mais realista. Fontes de luz diferentes têm temperaturas de cor diferente, e as configurações de câmera usadas para capturar uma imagem devem ser ajustadas para compensar essas diferenças. Novamente, permitindo que o controle de usuário sobre a proporção de branco, eles podem tornar professional ajustes que as rotinas automática não são capazes de obter efeitos artísticos.

[![](intro-to-manual-camera-controls-images/image16.png "Uma imagem de exemplo mostrando os ajustes de balanço de branco Manual")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Por exemplo, o horário de verão tem uma conversão blueish, enquanto luzes incandescentes tungstênio têm um tom mais quente, laranja. (Erroneamente, cores "interessantes" tem temperaturas de cor mais alto que cores "passivos". Cor temperaturas são uma medida física, não uma percepção).

A mente humana é muito bom para compensar as diferenças de temperatura de cor, mas isso é algo que não é possível fazer uma câmera. A câmera funciona, aumentando a cor no espectro oposto ajustar para as diferenças de cor.

O novo iOS 8 exposição API permite que o aplicativo controlar o processo e fornecer controle refinado sobre configurações de balanço de branco da câmera.

### <a name="how-white-balance-works"></a>Funciona como o branco de saldo

Antes de discutir os detalhes de controle de saldo branco em um aplicativo do IOS 8. Vamos dar uma olhada rápida funciona de saldo como branco:

Estudo de percepção de cor, o [CIE 1931 RGB cor espaço e o espaço de cor CIE 1931 XYZ](http://en.wikipedia.org/wiki/CIE_1931_color_space) são a primeira matematicamente espaços de cores. Eles foram criados pela comissão internacional de iluminação (CIE) em 1931.

[![](intro-to-manual-camera-controls-images/image17.png "O espaço de cor RGB CIE 1931 e CIE 1931 XYZ espaço de cores")](intro-to-manual-camera-controls-images/image17.png#lightbox)

O gráfico acima mostra todas as cores visíveis ao olho humano, da profundidade azul brilhante verde para vermelho brilhante. Qualquer ponto no diagrama pode ser plotado com um valor de X e Y, conforme mostrado no gráfico acima.

Como visível no gráfico, há valores de X e Y que podem ser plotados no gráfico que seria fora do intervalo de visão humana e, como resultado, essas cores não podem ser reproduzidas por uma câmera.

A curva menor no gráfico acima é chamada de [Planckian Locus](http://en.wikipedia.org/wiki/Planckian_locus), que expressa a temperatura de cor (em graus kelvin) com números maiores no lado do azul (quanto mais quente) e inferior de números no lado do vermelho (mais frio). Eles são úteis para situações de iluminação típico.

Em condições de iluminação misto, os ajustes de balanço de branco serão necessário evitar o Planckian Locus para fazer as alterações necessárias. Nessas situações, que o ajuste será necessário ser alterado para verde ou vermelho/magenta lado a CIE dimensionar.

Dispositivos iOS compensam projeções de cores, aumentando o ganho de cor oposta. Por exemplo, se uma cena tem muito azul, o ganho de vermelho será ampliado para compensar. Esses valores são calibrados para dispositivos específicos para que sejam dependentes do dispositivo de ganho.

### <a name="existing-white-balance-controls"></a>Controles de balanço de branco existentes

iOS 7 e acima fornecido os seguintes controles de balanço de branco existentes por meio de `WhiteBalanceMode` propriedade:

-   `AVCapture WhiteBalance ModeLocked` – Exemplos cena uma vez e usar esses valores ao longo da cena.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` – A cena continuamente para garantir que ele é bem equilibrado de exemplos.


E o aplicativo pode monitorar o `AdjustingWhiteBalance` propriedade para ver quando exposição está sendo ajustada.

### <a name="new-white-balance-controls-in-ios-8"></a>Novos controles de saldo de branco do iOS 8

Além dos recursos já fornecidos pelo iOS 7 e posterior, os recursos a seguir agora estão disponíveis para o controle de balanço de branco no iOS 8:

-  Obtém controle totalmente manual do dispositivo RGB.
-  Get, Set e chave-valor observar obtiver o dispositivo RGB.
-  Suporte para o equilíbrio de branco usando um cartão de cinza.
-  Rotinas de conversão para e de espaços de cores independentes de dispositivo.


Para implementar os recursos acima, o `AVCaptureWhiteBalanceGain` estrutura foi adicionada com os seguintes membros:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


O ganho de balanço de branco máximo é atualmente quatro (4) e pode ser pronto do `MaxWhiteBalanceGain` propriedade. O intervalo válido é de um (1) `MaxWhiteBalanceGain` (4) no momento.

O `DeviceWhiteBalanceGains` propriedade pode ser usada para observar os valores atuais. Use `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` ajustar o equilíbrio obtiver quando a câmera está no modo de balanço de branco bloqueado.

#### <a name="conversion-routines"></a>Rotinas de conversão

Rotinas de conversão foram adicionadas para o iOS 8 para ajudar com a conversão para e de espaços de cores independentes de dispositivo. Para implementar as rotinas de conversão, o `AVCaptureWhiteBalanceChromaticityValues` estrutura foi adicionada com os seguintes membros:

-   `X` -é um valor de 0 a 1.
-   `Y` -é um valor de 0 a 1.


Um `AVCaptureWhiteBalanceTemperatureAndTintValues` estrutura também foi adicionada com os seguintes membros:

-   `Temperature` -é uma flutuante valor de ponto em graus Kelvin.
-   `Tint` -é um deslocamento de verde ou vermelho de 0 para 150 com valores positivos para a direção de verde e negativo na direção no magenta.


Use o `CaptureDevice.GetTemperatureAndTintValues`e `CaptureDevice.GetDeviceWhiteBalanceGains`métodos para converter entre temperatura e tonalidade, cromaticidade e RGB obtém espaços de cores.

> [!NOTE]
> As rotinas de conversão são mais precisas quanto o valor a ser convertido for o Planckian Locus.




#### <a name="gray-card-support"></a>Suporte a placa cinza

Apple usa o termo World cinza para referir-se para o suporte a cartões cinza incorporado iOS 8. Ele permite que o usuário a se concentrar em um cartão físico de cinza que abrange pelo menos 50% do centro do quadro e a usa para ajustar o equilíbrio de branco. A finalidade do cartão cinza é conseguir branco aparece neutro.

Isso pode ser implementado em um aplicativo, solicitando que o usuário coloque um cartão físico cinza na frente da câmera, monitoramento de `GrayWorldDeviceWhiteBalanceGains` propriedade e esperar até que os valores acomode.

O aplicativo, em seguida, pode bloquear os ganhos de balanço de branco para o `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` método usando os valores do `GrayWorldDeviceWhiteBalanceGains` propriedade para aplicar as alterações.

O dispositivo de captura deve ser bloqueado para a configuração antes de uma alteração no equilíbrio de branco pode ser feita.

### <a name="manual-white-balance-example"></a>Exemplo de proporção de branco manual

Com o código de configuração de captura de AV geral em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image18.png "Um UIViewController pode ser adicionado aos aplicativos de Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image18.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a alimentação de vídeo.
-  Um `UISegmentedControl` isso alterará o modo de foco de automática para bloqueado.
-  Dois `UISlider` controles que mostrará e atualizar a temperatura e tom.
-  Um `UIButton` usado para um espaço de cartão de cinza (cinza World) de exemplo e definir o equilíbrio de branco usando esses valores.


Faça o seguinte para transmissão-se o controlador de exibição para controle de saldo de branco Manual:


1. Adicione o seguinte usando instruções:

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
  
1. Adicione o seguinte método particular para definir o novo white equilibrar temperatura e tonalidade:

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
  
1. Salvar as alterações de código e executar o aplicativo.
1. Com a câmera no modo automático, os controles deslizantes moverá automaticamente como a câmera ajusta a proporção de branco:

    [![](intro-to-manual-camera-controls-images/image19.png "Os controles deslizantes moverá automaticamente como a câmera ajusta a proporção de branco")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. O segmento bloqueado de toque e arraste os controles deslizantes Temp e tonalidade para ajustar o equilíbrio de branco manualmente:

    [![](intro-to-manual-camera-controls-images/image20.png "Arraste os controles deslizantes Temp e tonalidade para ajustar o equilíbrio de branco manualmente")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Com o segmento bloqueado ainda selecionado, coloque um cartão físico cinza na frente da câmera e toque no botão de cartão de cinza para ajustar o equilíbrio de branco para o mundo cinza:

    [![](intro-to-manual-camera-controls-images/image21.png "Toque no botão de cartão de cinza para ajustar o equilíbrio de branco para o mundo cinza")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Interrompa o aplicativo.

O código acima mostra como monitorar as configurações de balanço de branco quando a câmera está no modo automático ou use os controles deslizantes para controlar o saldo em branco quando ele estiver no modo bloqueado.

## <a name="bracketed-capture"></a>Captura entre colchetes

A captura de colchetes é baseada nas configurações dos controles de câmera Manual apresentada acima e permite que o aplicativo capturar um ponto no tempo, em uma variedade de maneiras diferentes.

Simplificando, colchetes de captura é uma intermitência de imagens tiradas com uma variedade de configurações de imagem para a imagem.

[![](intro-to-manual-camera-controls-images/image22.png "Como funciona a captura de colchetes")](intro-to-manual-camera-controls-images/image22.png#lightbox)

Usando a captura de colchetes no iOS 8, um aplicativo pode predefinir uma série de controles da câmera Manual, emitir um único comando e ter cena atual retornar uma série de imagens para cada uma das predefinições manuais.

### <a name="bracketed-capture-basics"></a>Noções básicas de captura entre colchetes

Novamente, colchetes de captura é uma intermitência de imagens tiradas com diferentes configurações de imagem para imagem. Os tipos de colchetes capturar disponíveis são:

-  **Auto exposição colchete** – onde todas as imagens têm um valor de tendência variado.
-  **Colchete de exposição manual** – onde todas as imagens têm um variadas velocidade de redimensionamento (duração) e ISO quantidade.
-  **Colchete de intermitência simples** – uma série de imagens estáticas em sucessão rápida.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Novos colchetes capturar controles do iOS 8

Todos os comandos de colchetes capturar são implementados no `AVCaptureStillImageOutput` classe. Use o `CaptureStillImageBracket`método para obter uma série de imagens com a matriz de configurações fornecida.

Duas novas classes foram implementados para lidar com as configurações:

-   `AVCaptureAutoExposureBracketedStillImageSettings` – Ele tem uma propriedade, `ExposureTargetBias`, usado para definir a tendência para um colchete de exposição automática. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – Ele tem duas propriedades, `ExposureDuration` e `ISO`, usado para definir a velocidade de redimensionamento e ISO para um colchete de exposição manual. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Captura entre colchetes controla regras

#### <a name="dos"></a>Fazer

A seguir está uma lista de itens que deve ser feito quando o uso de colchetes Capture controla no iOS 8:

-  Preparar o aplicativo para a situação de captura pior chamando o `PrepareToCaptureStillImageBracket` método.
-  Suponha que os buffers de exemplo serão provenientes do mesmo pool compartilhado.
-  Para liberar a memória que foi alocada por uma chamada anterior de preparação, chamar `PrepareToCaptureStillImageBracket` novamente e enviá-lo em uma matriz de um objeto.


#### <a name="donts"></a>Não fazer

A seguir está uma lista de itens que não deve ser feito quando o uso de colchetes Capture controla no iOS 8:

-  Não misture colchetes capturar tipos de configurações em uma única captura.
-  Não solicitar mais de `MaxBracketedCaptureStillImageCount` imagens em uma única captura.


### <a name="bracketed-capture-details"></a>Detalhes de captura entre colchetes

Os seguintes detalhes devem ser levados em consideração ao trabalhar com colchetes capturar no iOS 8:

-  Substituem temporariamente as configurações entre colchetes a `AVCaptureDevice` configurações.
-  Configurações de estabilização de imagem Flash e ainda são ignoradas.
-  Todas as imagens devem usar o mesmo formato de saída (jpeg, png, etc.)
-  Visualização de vídeo pode descartar quadros.
-  Captura entre colchetes tem suporte em todos os dispositivos compatíveis com iOS 8.


Com essas informações em mente, vamos dar uma olhada em um exemplo do uso de colchetes capturar no iOS 8.

### <a name="bracket-capture-example"></a>Exemplo de captura de colchete

Com o código de configuração de captura de AV geral em vigor, um `UIViewController` podem ser adicionados ao Storyboard do aplicativo e configurados da seguinte maneira:

[![](intro-to-manual-camera-controls-images/image23.png "Um UIViewController pode ser adicionado aos aplicativos de Storyboard e configurado conforme mostrado aqui")](intro-to-manual-camera-controls-images/image23.png#lightbox)

O modo de exibição contém os seguintes elementos principais:

-  Um `UIImageView` que exibirá a alimentação de vídeo.
-  Três `UIImageViews` que exibirá os resultados da captura.
-  Um `UIScrollView` para hospedar o feed de vídeo e modos de exibição de resultado.
-  Um `UIButton` usada para tirar uma captura agrupado com algumas configurações predefinidas.


Faça o seguinte para transmissão-se o controlador de exibição para capturar colchetes:


1. Adicione o seguinte usando instruções:

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
  
1. Adicione o seguinte método particular para criar as exibições de imagem de saída necessária:

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
    
1. Salvar as alterações de código e executar o aplicativo.
1. Quadro uma cena e toque no botão de colchete capturar:

    [![](intro-to-manual-camera-controls-images/image24.png "Quadro uma cena e toque no botão de captura colchete")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Passe o dedo da direita para esquerda para ver as três imagens tomadas por colchetes captura:

    [![](intro-to-manual-camera-controls-images/image25.png "Passe o dedo da direita para esquerda para ver as três imagens tomadas por colchetes captura")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Interrompa o aplicativo.


O código acima mostra como configurar e levar um automática exposição colchetes capturar no iOS 8.

## <a name="summary"></a>Resumo

Neste artigo é coberto uma introdução para os novos controles de câmera Manual fornecida pelo iOS 8 e coberto os fundamentos da que fazem e como elas funcionam. Podemos dar exemplos de foco Manual, exposição Manual e equilíbrio de branco Manual. Por fim, fornecemos um exemplo tirar uma colchetes captura usando os controles de câmera Manual discutido anteriormente

## <a name="related-links"></a>Links relacionados

- [ManualCameraControls (exemplo)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
