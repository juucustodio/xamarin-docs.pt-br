---
title: Guia de Introdução ao macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3620312ff3fbf9d7aa879ae6d318f0b39eec386a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-macos"></a>Guia de Introdução ao macOS

## <a name="what-you-will-need"></a>Você precisará

* Siga as instruções no [guia de Introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guia.

## <a name="hello-world"></a>Hello world

Primeiro, crie um exemplo do mundo Olá simples em c#.

### <a name="create-c-sample"></a>Criar o exemplo em c#

Abra o Visual Studio para Mac, crie um novo projeto de biblioteca de classes do Mac denominado **saudação do csharp**e salvá-lo para **~/Projects/hello-from-csharp**.

Substitua o código no **MyClass.cs** arquivo com o trecho a seguir:

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

Uma vez que um assembly gerenciado, associá-lo invocando a incorporação de .NET.

Conforme descrito no [instalação](~/tools/dotnet-embedding/get-started/install/install.md) guia, isso pode ser feito como etapa de pós-compilação do seu projeto, com um destino personalizado do MSBuild ou manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

A estrutura será colocada em **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Use a saída gerada em um projeto no Xcode

Abra o Xcode e crie um novo aplicativo Cocoa. Nomeie- **saudação do csharp** e selecione o **Objective-C** idioma.

Abra o **~/Projects/hello-from-csharp/output** diretório no localizador, selecione **saudação do csharp.framework**, arraste-o para o projeto Xcode e solte-o imediatamente acima a **saudação do csharp**  pasta do projeto.

![Arrastar e soltar do framework](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Certifique-se de **copiar itens se necessário** estiver marcada na caixa de diálogo pop-up e, em seguida, clique em **concluir**.

![Copiar itens se necessário](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Selecione o **saudação do csharp** do projeto e navegue até o **saudação do csharp** do destino **geral** guia. No **incorporado binário** seção, adicione **saudação do csharp.framework**.

![Binários inseridos](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Abra **ViewController.m**e substitua o conteúdo com:

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

Por fim, execute o projeto Xcode e semelhante a esta será exibida:

![Saudação de exemplo em c# em execução no simulador](macos-images/hello-from-csharp-mac.png)

Um exemplo mais completo e um [estão disponíveis aqui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
