---
title: Migrar uma associação para a API unificada
description: Este artigo aborda as etapas necessárias para atualizar um projeto de associação de Xamarin existente para dar suporte a APIs unificadas para aplicativos xamarin. IOS e xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57f27bf0d3aaa2a7ba14f23481a8f2bb2d87f2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261137"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migrar uma associação para a API unificada

_Este artigo aborda as etapas necessárias para atualizar um projeto de associação de Xamarin existente para dar suporte a APIs unificadas para aplicativos xamarin. IOS e xamarin. Mac._

## <a name="overview"></a>Visão geral

Começando em 1º de fevereiro de 2015 a Apple exige que todos os envios de novo para o iTunes e o Mac App Store devem ser aplicativos de 64 bits. Como resultado, qualquer novo aplicativo xamarin. IOS ou xamarin. Mac precisará estar usando a nova API unificada em vez das APIs de MonoMac e o MonoTouch clássica existente para dar suporte a 64 bits.

Além disso, qualquer projeto de associação do Xamarin também deve oferecer suporte a novas APIs unificado a serem incluídos em um projeto de xamarin. Mac ou o xamarin. IOS de 64 bits. Este artigo abordará as etapas necessárias para atualizar um projeto de associação existente para usar a API unificada.

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para concluir as etapas apresentadas neste artigo:

 -  **O Visual Studio para Mac** -a versão mais recente do Visual Studio para Mac instalado e configurado no computador de desenvolvimento.
 -  **Apple Mac** - uma Apple mac é necessário para compilar projetos de associação para iOS e Mac.

Não há suporte para projetos de associação no Visual studio em um computador Windows.

## <a name="modify-the-using-statements"></a>Modifique as instruções Using

As APIs do Unified torna mais fácil compartilhar código entre o Mac e iOS, bem como permitindo que você dê suporte a aplicativos de bit 32 e 64 com o mesmo binário. Descartando o _MonoMac_ e _MonoTouch_ prefixos de namespaces, mais simples de compartilhamento é obtido em projetos de aplicativo xamarin. Mac e xamarin. IOS.

Como resultado, precisaremos modifique qualquer um dos nossos contratos de associação (e outras `.cs` arquivos em nosso projeto de vinculação) para remover o _MonoMac_ e _MonoTouch_ prefixos de nosso `using` instruções.

Por exemplo, considerando o seguinte usando as instruções em um contrato de associação:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Podemos seria removem o `MonoTouch` prefixo resultando no seguinte:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Novamente, precisamos fazer isso para qualquer `.cs` arquivo em nosso projeto de associação. Com essa alteração em vigor, a próxima etapa é atualizar nosso projeto de associação para usar os novos tipos de dados nativos.

Para obter mais informações sobre a API unificada, consulte o [API unificada](~/cross-platform/macios/unified/index.md) documentação. Para obter mais informações sobre suporte a 32 e 64 bits de aplicativos e informações sobre estruturas, consulte o [considerações sobre a plataforma de 32 e 64 bits](~/cross-platform/macios/32-and-64/index.md) documentação.

## <a name="update-to-native-data-types"></a>Atualização para tipos de dados nativos

Objective-C mapeia o `NSInteger` tipo de dados `int32_t` em sistemas de 32 bits e, ao `int64_t` em sistemas de 64 bits. Para corresponder a esse comportamento, a nova API unificada substitui os usos anteriores `int` (que no .NET é definida como sempre sendo `System.Int32`) para um novo tipo de dados: `System.nint`.

Juntamente com o novo `nint` apresenta o tipo de dados, a API unificada o `nuint` e `nfloat` tipos de mapeamento para o `NSUInteger` e `CGFloat` tipos também.

Devido ao acima mencionado, precisamos examinar nossa API e certifique-se de que qualquer instância do `NSInteger`, `NSUInteger` e `CGFloat` que Mapeamos anteriormente ao `int`, `uint` e `float` são atualizados para o novo `nint`, `nuint` e `nfloat` tipos.

Por exemplo, dada uma definição de método de Objective-C:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Se o contrato de ligação anterior tinha a seguinte definição:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Gostaríamos de atualizar a nova associação ser:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Se nós estiver mapeando para uma mais recente versão 3ª biblioteca de terceiros que o que podemos inicialmente tinha vinculado à, precisamos examinar o `.h` arquivos de cabeçalho para a biblioteca e ver se qualquer existentes, explícitas chamadas para `int`, `int32_t`, `unsigned int`, `uint32_t` ou `float` foi atualizado para ser um `NSInteger`, `NSUInteger` ou um `CGFloat`. Nesse caso, as mesmas modificações a `nint`, `nuint` e `nfloat` tipos precisam ser feitas para seus mapeamentos bem.

Para saber mais sobre essas alterações de tipo de dados, consulte o [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

## <a name="update-the-coregraphics-types"></a>Atualizar os tipos de CoreGraphics

Os tipos de dados de ponto, o tamanho e o retângulo que são usados com `CoreGraphics` usam 32 ou 64 bits, dependendo do dispositivo são executados em. Quando Xamarin vinculado originalmente as APIs de Mac e iOS usamos as estruturas de dados existentes que ocorreram corresponder os tipos de dados no `System.Drawing` (`RectangleF` por exemplo).

Por causa dos requisitos para dar suporte a novos tipos de dados nativos e de 64 bits, os seguintes ajustes precisará ser feitas ao código existente ao chamar `CoreGraphic` métodos:

- **CGRect** -Use `CGRect` em vez de `RectangleF` quando definindo flutuante ponto regiões retangulares.
- **CGSize** -Use `CGSize` em vez de `SizeF` ao definir os tamanhos de ponto flutuante (largura e altura).
- **CGPoint** -Use `CGPoint` em vez de `PointF` quando definindo flutuante ponto local (coordenadas X e Y).

Devido ao acima mencionado, precisamos examinar nossa API e certifique-se de que qualquer instância do `CGRect`, `CGSize` ou `CGPoint` que foi associado anteriormente a `RectangleF`, `SizeF` ou `PointF` ser alterado para o tipo nativo `CGRect`, `CGSize` ou `CGPoint` diretamente.

Por exemplo, dado um inicializador de Objective-C do:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Se nossa associação anterior incluiu o código a seguir:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Gostaríamos de atualizar esse código para:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Com todas as alterações de código já em vigor, precisamos modificar nosso projeto de associação ou tornar o arquivo ligar as APIs de Unificação.

## <a name="modify-the-binding-project"></a>Modificar o projeto de associação

Como a etapa final para atualizar nosso projeto de associação para usar as APIs de Unificação, precisamos modificar a `MakeFile` que usamos para compilar o projeto ou o tipo de projeto do Xamarin (se estamos associando de dentro do Visual Studio para Mac) e instruir _btouch_  ligar as APIs de Unificação em vez de clássico aqueles.


### <a name="updating-a-makefile"></a>Atualizando um MakeFile

Se estivermos utilizando um makefile para compilar nosso projeto de associação em um Xamarin. DLL, será necessário incluir o `--new-style` opção de linha de comando e chame `btouch-native` em vez de `btouch`.

Portanto, considerando a seguinte `MakeFile`:

```csharp
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```

Precisamos alternar de chamar `btouch` para `btouch-native`, portanto, podemos seria ajustado nossa definição de macro da seguinte maneira:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Gostaríamos de atualizar a chamada para `btouch` e adicione o `--new-style` opção da seguinte maneira:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Agora podemos executar nossa `MakeFile` normalmente para criar a nova versão de 64 bits de nossa API.

### <a name="updating-a-binding-project-type"></a>Atualizando um tipo de projeto de associação

Se estivermos utilizando um Visual Studio para Mac associação de modelo de projeto para criar nossa API, precisaremos atualizar para a nova versão de API unificada do modelo de projeto de associação. A maneira mais fácil de fazer isso é iniciar um novo projeto de associação de API unificada e cópia sobre todas as configurações e o código existente.

Faça o seguinte:

1. Inicie o Visual Studio para Mac.
2. Selecione **arquivo** > **nova** > **solução...**
3. Na caixa de diálogo nova solução, selecione **iOS** > **API unificada** > **associando projeto iOS**: 

    [![](update-binding-images/image01new.png "Na caixa de diálogo nova solução, selecione iOS / API unificada / projeto de associação do iOS")](update-binding-images/image01new.png#lightbox)
4. Na caixa de diálogo 'Configurar seu novo projeto' Insira um **nome** para o novo projeto de associação e um clique a **Okey** botão.
5. Inclua a versão de 64 bits da biblioteca do Objective-C que você vai criar associações para.
6. Copie o código-fonte do seu projeto de associação de API clássica de 32 bits existente (como o `ApiDefinition.cs` e `StructsAndEnums.cs` arquivos).
7. Verifique as alterações indicadas acima para os arquivos de código-fonte.

Com todas essas alterações realizadas, você pode criar a nova versão de 64 bits da API, como você faria com a versão de 32 bits.

## <a name="summary"></a>Resumo

Neste artigo, mostramos as alterações que precisam ser feitas a um projeto de associação do Xamarin existente para dar suporte a novas APIs unificadas e dispositivos de 64 bits e as etapas necessárias para criar a nova versão compatível com 64 bits de uma API.



## <a name="related-links"></a>Links relacionados

- [Mac e iOS](~/cross-platform/macios/index.md)
- [API Unificada](~/cross-platform/macios/nativetypes.md)
- [Considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md)
- [Atualizando aplicativos iOS existentes](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API Unificada](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
