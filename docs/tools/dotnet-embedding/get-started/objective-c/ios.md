---
title: Introdução ao iOS
description: Este documento descreve como começar a usar a incorporação do .NET com o iOS. Ele discute os requisitos e apresenta um aplicativo de exemplo para demonstrar como associar um assembly gerenciado e usar a saída em um projeto do Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 009772ac88ad57bab53fb71c9705b71f0f8acc8b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118279"
---
# <a name="getting-started-with-ios"></a>Introdução ao iOS

## <a name="requirements"></a>Requisitos

Além dos requisitos de nossos [Introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guia, você também precisará:

* [Xamarin. IOS 10.11](https://visualstudio.microsoft.com/xamarin/) ou posterior

## <a name="hello-world"></a>Hello world

Primeiro, crie um exemplo simples de Olá do mundo em c#.

### <a name="create-c-sample"></a>Criar o exemplo de c#

Abra o Visual Studio para Mac, crie um novo projeto de biblioteca de classes do iOS, nomeie- **hello-de-csharp**e salvá-lo no **~/Projects/hello-from-csharp**.

Substitua o código na **MyClass.cs** arquivo com o trecho a seguir:

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

Compilar o projeto e o assembly resultante será salvo como **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associar o assembly gerenciado

Quando você tiver um assembly gerenciado, associá-lo ao invocar a incorporação do .NET.

Conforme descrito na [instalação](~/tools/dotnet-embedding/get-started/install/install.md) guia, isso pode ser feito como etapa de pós-compilação no seu projeto, com um destino MSBuild personalizado, ou manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

A estrutura será colocada na **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar a saída gerada em um projeto do Xcode

Abra o Xcode, crie um novo aplicativo de exibição única do iOS, nomeie- **hello-de-csharp**e selecione o **Objective-C** idioma.

Abra o **~/Projects/hello-from-csharp/output** diretório no Finder, selecione **hello de csharp.framework**, arraste-o para o projeto do Xcode e solte-o imediatamente acima a **hello-de-csharp**  pasta do projeto.

![Arrastar e soltar do framework](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Certifique-se **copiar itens se necessário** está marcada na caixa de diálogo pop-up e clique em **concluir**.

![Copiar itens se necessário](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selecione o **hello-de-csharp** do projeto e navegue até a **hello-de-csharp** do destino **guia geral**. No **binários inseridos** seção, adicione **hello de csharp.framework**.

![Binários inseridos](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Abra **Viewcontroller**e substitua o conteúdo com:

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

Incorporação do .NET não suporta atualmente bitcode no iOS, que é habilitado para alguns modelos de projeto do Xcode. 

Para desabilitá-lo nas configurações do projeto:

![Opção de Bitcode](../../images/ios-bitcode-option.png)

Por fim, execute o projeto do Xcode e semelhante ao seguinte será exibida:

![Olá do exemplo de c# em execução no simulador](ios-images/hello-from-csharp-ios.png)
