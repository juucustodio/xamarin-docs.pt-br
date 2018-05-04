---
title: Guia de Introdução do iOS
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: f3696ffa5bb3b3931bcea0f93343bb46d2f92ad5
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-ios"></a>Guia de Introdução do iOS

## <a name="requirements"></a>Requisitos

Além dos requisitos de nossos [guia de Introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) guia, você também precisará:

* [Xamarin 10.11](https://www.visualstudio.com/xamarin/) ou posterior

## <a name="hello-world"></a>Hello world

Primeiro, crie um exemplo do mundo Olá simples em c#.

### <a name="create-c-sample"></a>Criar o exemplo em c#

Abra o Visual Studio para Mac, crie um novo projeto de biblioteca de classes do iOS, nomeie-o **saudação do csharp**e salvá-lo para **~/Projects/hello-from-csharp**.

Substitua o código no **MyClass.cs** arquivo com o trecho a seguir:

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

Compile o projeto e o assembly resultante será salvo como **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associar o assembly gerenciado

Uma vez que um assembly gerenciado, associá-lo invocando a incorporação de .NET.

Conforme descrito no [instalação](~/tools/dotnet-embedding/get-started/install/install.md) guia, isso pode ser feito como etapa de pós-compilação do seu projeto, com um destino personalizado do MSBuild ou manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

A estrutura será colocada em **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Use a saída gerada em um projeto no Xcode

Abrir o Xcode, crie um único aplicativo de exibição do novo iOS, nomeie- **saudação do csharp**e selecione o **Objective-C** idioma.

Abra o **~/Projects/hello-from-csharp/output** diretório no localizador, selecione **saudação do csharp.framework**, arraste-o para o projeto Xcode e solte-o imediatamente acima a **saudação do csharp**  pasta do projeto.

! [Arrastar e soltar do framework] Images/Hello-from-CSharp-IOS-Drag-drop-Framework.png)

Certifique-se de **copiar itens se necessário** estiver marcada na caixa de diálogo pop-up e, em seguida, clique em **concluir**.

![Copiar itens se necessário](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selecione o **saudação do csharp** do projeto e navegue até o **saudação do csharp** do destino **guia geral**. No **incorporado binário** seção, adicione **saudação do csharp.framework**.

![Binários inseridos](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Abra **ViewController.m**e substitua o conteúdo com:

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

Inserindo .NET não suporta atualmente bitcode no iOS, que é habilitado para alguns modelos de projeto Xcode. 

Desabilite-o em suas configurações de projeto:

![Opção Bitcode](../../images/ios-bitcode-option.png)

Por fim, execute o projeto Xcode e semelhante a esta será exibida:

![Saudação de exemplo em c# em execução no simulador](ios-images/hello-from-csharp-ios.png)
