---
title: Introdução ao macOS
description: Este documento descreve como começar a usar a inserção do .NET com o macOS. Ele aborda os requisitos e apresenta um aplicativo de exemplo para demonstrar como associar o assembly gerenciado e usar a saída gerada em um projeto do Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: f0e2128bca5d2965395647353cd5a95a4030439f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292857"
---
# <a name="getting-started-with-macos"></a>Introdução ao macOS

## <a name="what-you-will-need"></a>O que será necessário

* Siga as instruções no guia [introdução ao Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) .

## <a name="hello-world"></a>Olá, Mundo

Primeiro, crie um exemplo simples do Hello World C#no.

### <a name="create-c-sample"></a>Criar C# exemplo

Abra Visual Studio para Mac, crie um novo projeto de biblioteca de classes Mac chamado **Hello-from-Csharp**e salve-o em **~/Projects/Hello-from-Csharp**.

Substitua o código no arquivo **MyClass.cs** pelo seguinte trecho:

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

Compile o projeto. O assembly resultante será salvo como **~/Projects/Hello-from-Csharp/Hello-from-Csharp/bin/Debug/Hello-from-Csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associar o assembly gerenciado

Quando você tiver um assembly gerenciado, associe-o invocando a inserção do .NET.

Conforme descrito no guia de [instalação](~/tools/dotnet-embedding/get-started/install/install.md) , isso pode ser feito como etapa de pós-compilação em seu projeto, com um destino do MSBuild personalizado ou manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

A estrutura será colocada em **~/Projects/Hello-from-Csharp/output/Hello-from-Csharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usar a saída gerada em um projeto do Xcode

Abra o Xcode e crie um novo aplicativo Cocoa. Nomeie-o **Hello-from-Csharp** e selecione a linguagem **Objective-C** .

Abra o diretório **~/Projects/Hello-from-Csharp/output** no Finder, selecione **Hello-from-Csharp. Framework**, arraste-o para o projeto Xcode e solte-o logo acima da pasta **Hello-from-Csharp** no projeto.

![Arrastar e soltar estrutura](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Verifique se a opção **copiar itens se necessário** está marcada na caixa de diálogo que aparece e clique em **concluir**.

![Copiar itens, se necessário](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Selecione o projeto **Hello-from-Csharp** e navegue até a guia **geral** do destino **Hello-from-Csharp** . Na seção **binário inserido** , adicione **Hello-from-Csharp. Framework**.

![Binários inseridos](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Abra **ViewController. m**e substitua o conteúdo por:

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

Por fim, execute o projeto Xcode e algo assim será exibido:

![Olá do C# exemplo em execução no simulador](macos-images/hello-from-csharp-mac.png)
