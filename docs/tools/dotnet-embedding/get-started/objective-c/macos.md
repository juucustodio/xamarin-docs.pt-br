---
title: Introdução ao macOS
description: Este documento descreve como começar a usar a incorporação do .NET com macOS. Ele discute os requisitos e apresenta um aplicativo de exemplo para demonstrar como associar o assembly gerenciado e usar a saída gerada em um projeto do Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 3de47aa57df29f52f71508977ae017dff009c282
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121562"
---
# <a name="getting-started-with-macos"></a>Introdução ao macOS

## <a name="what-you-will-need"></a>O que você precisa

* Siga as instruções na [Introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guia.

## <a name="hello-world"></a>Hello world

Primeiro, crie um exemplo simples de Olá do mundo em c#.

### <a name="create-c-sample"></a>Criar o exemplo de c#

Abra o Visual Studio para Mac, crie um novo projeto de biblioteca de classes do Mac chamado **hello-de-csharp**e salvá-lo no **~/Projects/hello-from-csharp**.

Substitua o código na **MyClass.cs** arquivo com o trecho a seguir:

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

Compile o projeto. O assembly resultante será salvo como **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associar o assembly gerenciado

Quando você tiver um assembly gerenciado, associá-lo ao invocar a incorporação do .NET.

Conforme descrito na [instalação](~/tools/dotnet-embedding/get-started/install/install.md) guia, isso pode ser feito como etapa de pós-compilação no seu projeto, com um destino MSBuild personalizado, ou manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

A estrutura será colocada na **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar a saída gerada em um projeto do Xcode

Abra o Xcode e crie um novo aplicativo Cocoa. Denomine **hello-de-csharp** e selecione o **Objective-C** idioma.

Abra o **~/Projects/hello-from-csharp/output** diretório no Finder, selecione **hello de csharp.framework**, arraste-o para o projeto do Xcode e solte-o imediatamente acima a **hello-de-csharp**  pasta do projeto.

![Arrastar e soltar do framework](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Certifique-se **copiar itens se necessário** está marcada na caixa de diálogo pop-up e clique em **concluir**.

![Copiar itens se necessário](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Selecione o **hello-de-csharp** do projeto e navegue até a **hello-de-csharp** do destino **geral** guia. No **binários inseridos** seção, adicione **hello de csharp.framework**.

![Binários inseridos](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Abra **Viewcontroller**e substitua o conteúdo com:

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

Por fim, execute o projeto do Xcode e semelhante ao seguinte será exibida:

![Olá do exemplo de c# em execução no simulador](macos-images/hello-from-csharp-mac.png)

Um exemplo mais completo e entrando [está disponível aqui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
