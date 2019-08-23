---
title: Migrar uma associação para a API unificada
description: Este artigo aborda as etapas necessárias para atualizar um projeto de associação do Xamarin existente para dar suporte às APIs unificadas para aplicativos Xamarin. IOS e Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 90ef47d4e105dc401369c92e9196111c060314e3
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976416"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migrar uma associação para a API unificada

_Este artigo aborda as etapas necessárias para atualizar um projeto de associação do Xamarin existente para dar suporte às APIs unificadas para aplicativos Xamarin. IOS e Xamarin. Mac._

## <a name="overview"></a>Visão geral

A partir de 1º de fevereiro, a Apple 2015 requer que todos os novos envios para o iTunes e o Mac App Store sejam aplicativos de 64 bits. Como resultado, qualquer novo aplicativo Xamarin. iOS ou Xamarin. Mac precisará usar o novo API Unificada em vez das APIs clássicas de MonoTouch e MonoMac existentes para dar suporte a 64 bits.

Além disso, qualquer projeto de associação do Xamarin também deve dar suporte às novas APIs unificadas a serem incluídas em um projeto do Xamarin. iOS ou Xamarin. Mac de 64 bits. Este artigo abordará as etapas necessárias para atualizar um projeto de associação existente para usar o API Unificada.

## <a name="requirements"></a>Requisitos

O seguinte é necessário para concluir as etapas apresentadas neste artigo:

- **Visual Studio para Mac** -a versão mais recente do Visual Studio para Mac instalada e configurada no computador de desenvolvimento.
- **Apple Mac** – um Apple Mac é necessário para criar projetos de associação para IOS e Mac.

Os projetos de associação não têm suporte no Visual Studio em um computador Windows.

## <a name="modify-the-using-statements"></a>Modificar as instruções using

As APIs unificadas facilitam ainda mais o compartilhamento de código entre Mac e iOS, além de permitir que você dê suporte a aplicativos de 32 e 64 bits com o mesmo binário. Ao descartar os prefixos _MonoMac_ e MonoTouch dos namespaces, o compartilhamento mais simples é obtido em projetos de aplicativos Xamarin. Mac e xamarin. Ios.

Como resultado, precisaremos modificar qualquer um dos nossos contratos de ligação (e outros `.cs` arquivos em nosso projeto de associação) para remover os prefixos _MonoMac_ e MonoTouch `using` de nossas instruções.

Por exemplo, considerando as seguintes instruções using em um contrato de associação:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Podemos remover o `MonoTouch` prefixo, resultando no seguinte:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Novamente, precisaremos fazer isso para qualquer `.cs` arquivo em nosso projeto de associação. Com essa alteração em vigor, a próxima etapa é atualizar nosso projeto de associação para usar os novos tipos de dados nativos.

Para obter mais informações sobre o API Unificada, consulte a documentação do [API unificada](~/cross-platform/macios/unified/index.md) . Para obter mais informações sobre como dar suporte a aplicativos de 32 e 64 bits, e sobre as estruturas, consulte a documentação considerações sobre a [plataforma de 32 e 64 bits](~/cross-platform/macios/32-and-64/index.md) .

## <a name="update-to-native-data-types"></a>Atualizar para tipos de dados nativos

Objective-C mapeia `NSInteger` o tipo de `int32_t` dados para sistemas de 32 bits `int64_t` e para sistemas de 64 bits. Para corresponder a esse comportamento, o novo API unificada substitui os usos anteriores `int` de (que, no .net, é definido `System.Int32`como sempre sendo) para um novo `System.nint`tipo de dados:.

Junto com o novo `nint` tipo de dados, o API unificada introduz `nuint` os `nfloat` tipos e, para mapeamento para `NSUInteger` os `CGFloat` tipos e também.

Considerando o acima, precisamos examinar nossa API e garantir que `NSInteger`qualquer instância do, `NSUInteger` e `CGFloat` que mapeamos `int`anteriormente, `uint` e `float` seja atualizada para o novo `nint`, `nuint` tipos e`nfloat` .

Por exemplo, dada uma definição de método Objective-C de:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Se o contrato de Associação anterior tivesse a seguinte definição:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Atualizaremos a nova associação para:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

Se estivermos mapeando para uma biblioteca de terceiros de versão mais recente do que a que tínhamos inicialmente vinculada, precisamos `.h` examinar os arquivos de cabeçalho da biblioteca e ver se algum deles está saindo, `int`chamadas explícitas `uint32_t` para, `int32_t`, `unsigned int`ou foram atualizados para serem um `NSInteger`, `NSUInteger` ou um `CGFloat`. `float` Nesse caso, as mesmas modificações nos `nint`tipos, `nuint` e `nfloat` também precisarão ser feitas em seus mapeamentos.

Para saber mais sobre essas alterações de tipo de dados, consulte o documento [tipos nativos](~/cross-platform/macios/nativetypes.md) .

## <a name="update-the-coregraphics-types"></a>Atualizar os tipos de CoreGraphics

Os tipos de dados ponto, tamanho e retângulo que são usados `CoreGraphics` com o usam 32 ou 64 bits, dependendo do dispositivo em que estão sendo executados. Quando o Xamarin limitava originalmente as APIs de Ios e Mac, usamos estruturas de dados existentes que ocorreram para `System.Drawing` corresponder`RectangleF` os tipos de dados em (por exemplo).

Devido aos requisitos para dar suporte a 64 bits e novos tipos de dados nativos, os seguintes ajustes precisarão ser feitos ao código existente ao chamar `CoreGraphic` métodos:

- **CGRect** – use `CGRect` em vez `RectangleF` de ao definir regiões retangulares de ponto flutuante.
- **CGSize** – use `CGSize` em vez `SizeF` de ao definir tamanhos de ponto flutuante (largura e altura).
- **CGPoint** – use `CGPoint` em vez `PointF` de ao definir um local de ponto flutuante (coordenadas X e Y).

Considerando o acima, precisaremos examinar nossa API `CGRect`e garantir que qualquer instância de, `CGSize` ou `CGPoint` que tenha sido `RectangleF`associada `SizeF` anteriormente ou `PointF` seja alterada para o tipo `CGRect`nativo, `CGSize` ou`CGPoint` diretamente.

Por exemplo, dado um inicializador Objective-C de:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Se nossa ligação anterior incluía o seguinte código:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Atualizaremos esse código para:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Com todas as alterações de código agora em vigor, precisamos modificar nosso projeto de associação ou fazer com que o arquivo seja associado às APIs unificadas.

## <a name="modify-the-binding-project"></a>Modificar o projeto de associação

Como a etapa final para atualizar nosso projeto de associação para usar as APIs unificadas, precisamos modificar o `MakeFile` que usamos para criar o projeto ou o tipo de projeto Xamarin (se estivermos ligando de dentro Visual Studio para Mac) e instruir _btouch_ a associar em relação às APIs unificadas em vez das clássicas.


### <a name="updating-a-makefile"></a>Atualizando um MakeFile

Se estivermos usando um makefile para criar nosso projeto de associação em um Xamarin. DLL, será necessário incluir a `--new-style` opção de linha de comando e chamar `btouch-native` em vez de. `btouch`

Portanto, dado o `MakeFile`seguinte:

<!--markdownlint-disable MD010 -->
```makefile
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
<!--markdownlint-enable MD010 -->

Precisamos mudar de chamada `btouch` para `btouch-native`, portanto, Ajustaremos nossa definição de macro da seguinte maneira:

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Atualizaremos a chamada para `btouch` e adicionaremos a opção da `--new-style` seguinte maneira:

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

Agora podemos executar nossa `MakeFile` como normal para criar a nova versão de 64 bits de nossa API.

### <a name="updating-a-binding-project-type"></a>Atualizando um tipo de projeto de associação

Se estivermos usando um modelo de projeto de associação de Visual Studio para Mac para criar nossa API, precisaremos atualizar para a nova versão de API Unificada do modelo de projeto de associação. A maneira mais fácil de fazer isso é iniciar um novo projeto de associação de API Unificada e copiar sobre todo o código e as configurações existentes.

Faça o seguinte:

1. Iniciar Visual Studio para Mac.
2. Selecione **arquivo** > novasolução >  **...**
3. Na caixa de diálogo nova solução, selecione **Ios** > **API unificada** > o**projeto de associação Ios**: 

    [![](update-binding-images/image01new.png "Na caixa de diálogo nova solução, selecione projeto de associação iOS/API Unificada/iOS")](update-binding-images/image01new.png#lightbox)
4. Na caixa de diálogo ' configurar seu novo projeto ', insira um **nome** para o novo projeto de associação e clique no botão **OK** .
5. Inclua a versão de 64 bits da biblioteca Objective-C para a qual você vai criar associações.
6. Copie o código-fonte do seu projeto de associação de API clássica de 32 bits existente ( `ApiDefinition.cs` como `StructsAndEnums.cs` os arquivos e).
7. Faça as alterações indicadas acima nos arquivos de código-fonte.

Com todas essas alterações em vigor, você pode criar a nova versão de 64 bits da API da mesma forma que a versão de 32 bits.

## <a name="summary"></a>Resumo

Neste artigo, mostramos as alterações que precisam ser feitas em um projeto de associação do Xamarin existente para dar suporte às novas APIs unificadas e dispositivos de 64 bits e as etapas necessárias para criar a nova versão compatível com 64 bits de uma API.



## <a name="related-links"></a>Links relacionados

- [Mac e iOS](~/cross-platform/macios/index.md)
- [API Unificada](~/cross-platform/macios/nativetypes.md)
- [Considerações sobre a plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md)
- [Atualizando aplicativos iOS existentes](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API Unificada](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
