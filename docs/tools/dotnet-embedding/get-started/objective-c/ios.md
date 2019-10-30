---
title: Introdução ao iOS
description: Este documento descreve como começar a usar a inserção do .NET com o iOS. Ele aborda os requisitos e apresenta um aplicativo de exemplo para demonstrar como associar um assembly gerenciado e usar a saída em um projeto do Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5697d20077b746d9d33db985111c2d04908d5d01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029757"
---
# <a name="getting-started-with-ios"></a>Introdução ao iOS

## <a name="requirements"></a>Requisitos

Além dos requisitos de nossa introdução ao guia [Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) , você também precisará de:

* [Xamarin. iOS 10,11](https://visualstudio.microsoft.com/xamarin/) ou posterior

## <a name="hello-world"></a>Hello world

Primeiro, crie um exemplo simples do Hello World C#no.

### <a name="create-c-sample"></a>Criar C# exemplo

Abra Visual Studio para Mac, crie um novo projeto de biblioteca de classes do iOS, nomeie-o **Hello-from-Csharp**e salve-o em **~/Projects/Hello-from-Csharp**.

Substitua o código no arquivo **MyClass.cs** pelo seguinte trecho:

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

Compile o projeto e o assembly resultante será salvo como **~/Projects/Hello-from-Csharp/Hello-from-Csharp/bin/Debug/Hello-from-Csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associar o assembly gerenciado

Quando você tiver um assembly gerenciado, associe-o invocando a inserção do .NET.

Conforme descrito no guia de [instalação](~/tools/dotnet-embedding/get-started/install/install.md) , isso pode ser feito como etapa de pós-compilação em seu projeto, com um destino do MSBuild personalizado ou manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

A estrutura será colocada em **~/Projects/Hello-from-Csharp/output/Hello-from-Csharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar a saída gerada em um projeto do Xcode

Abra o Xcode, crie um novo aplicativo de exibição única do iOS, nomeie-o de **Hello-from-Csharp**e selecione a linguagem **Objective-C** .

Abra o diretório **~/Projects/Hello-from-Csharp/output** no Finder, selecione **Hello-from-Csharp. Framework**, arraste-o para o projeto Xcode e solte-o logo acima da pasta **Hello-from-Csharp** no projeto.

![Arrastar e soltar estrutura](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Verifique se a opção **copiar itens se necessário** está marcada na caixa de diálogo que aparece e clique em **concluir**.

![Copiar itens, se necessário](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selecione o projeto **Hello-from-Csharp** e navegue até a **guia Geral**do destino **Hello-from-Csharp** . Na seção **binário inserido** , adicione **Hello-from-Csharp. Framework**.

![Binários inseridos](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Abra **ViewController. m**e substitua o conteúdo por:

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

Atualmente, a inserção do .NET não dá suporte ao BitCode no iOS, que está habilitado para alguns modelos de projeto do Xcode. 

Desabilite-o nas configurações do projeto:

![Opção BitCode](../../images/ios-bitcode-option.png)

Por fim, execute o projeto Xcode e algo assim será exibido:

![Olá do C# exemplo em execução no simulador](ios-images/hello-from-csharp-ios.png)
