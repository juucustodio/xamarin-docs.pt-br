---
title: Migrando uma associação para a API unificada
description: Este artigo aborda as etapas necessárias para atualizar um projeto de vinculação de Xamarin existente para dar suporte a APIs unificado para aplicativos xamarin e Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57f27bf0d3aaa2a7ba14f23481a8f2bb2d87f2
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918481"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migrando uma associação para a API unificada

_Este artigo aborda as etapas necessárias para atualizar um projeto de vinculação de Xamarin existente para dar suporte a APIs unificado para aplicativos xamarin e Xamarin.Mac._

## <a name="overview"></a>Visão geral

Começando em 1º de fevereiro de 2015 Apple requer que todos os envios de novo a iTunes e Mac App Store devem ser aplicativos de 64 bits. Como resultado, qualquer novo aplicativo xamarin ou Xamarin.Mac precisará estar usando a nova API unificada em vez de MonoTouch clássico e MonoMac APIs existentes para dar suporte a 64 bits.

Além disso, qualquer projeto de associação Xamarin também deve dar suporte a novas APIs unificado devem ser incluídos em um projeto de Xamarin.Mac ou xamarin de 64 bits. Este artigo aborda as etapas necessárias para atualizar um projeto existente de associação para usar a API unificada.

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para concluir as etapas apresentadas neste artigo:

 -  **O Visual Studio para Mac** -a versão mais recente do Visual Studio para Mac instalado e configurado no computador de desenvolvimento.
 -  **Apple Mac** - uma Apple mac é necessário para criar projetos de associação para iOS e Mac.

Não há suporte para associação de projetos no Visual studio em um computador Windows.

## <a name="modify-the-using-statements"></a>Modifique as instruções Using

As APIs unificado torna mais fácil para compartilhar código entre Mac e iOS, bem como permitindo que você oferecer suporte a 32 e 64 bits aplicativos com os mesmos binários. Descartando o _MonoMac_ e _MonoTouch_ prefixos de namespaces, mais simples de compartilhamento é obtida em projetos de aplicativo Xamarin.Mac e xamarin.

Como resultado, terá de modificar qualquer um dos nossos contratos de associação (e outros `.cs` arquivos em nosso projeto de vinculação) para remover o _MonoMac_ e _MonoTouch_ prefixos de nosso `using` instruções.

Por exemplo, considerando o seguinte usando as instruções em um contrato de associação:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Podemos seria removem o `MonoTouch` prefixo resulta no seguinte:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Novamente, será necessário fazer isso para qualquer `.cs` arquivo em nosso projeto de associação. Com essa alteração no local, a próxima etapa é atualizar nosso projeto de vinculação para usar os novos tipos de dados nativos.

Para obter mais informações sobre a API unificado, consulte o [API unificada](~/cross-platform/macios/unified/index.md) documentação. Para obter mais informações sobre suporte de 32 e 64 bits aplicativos e informações sobre estruturas, consulte o [32 e 64 bits de considerações sobre a plataforma](~/cross-platform/macios/32-and-64/index.md) documentação.

## <a name="update-to-native-data-types"></a>Atualização para tipos de dados nativos

Objective-C mapeia o `NSInteger` tipo de dados `int32_t` em sistemas de 32 bits e para `int64_t` em sistemas de 64 bits. Para corresponder a esse comportamento, a nova API unificada substitui os usos anteriores `int` (que no .NET é definida como sendo sempre `System.Int32`) para um novo tipo de dados: `System.nint`.

Juntamente com o novo `nint` apresenta o tipo de dados, a API unificada o `nuint` e `nfloat` tipos de mapeamento para o `NSUInteger` e `CGFloat` tipos também.

Devido ao acima mencionado, precisamos examinar nossa API e certifique-se de que qualquer instância do `NSInteger`, `NSUInteger` e `CGFloat` que Mapeamos anteriormente para `int`, `uint` e `float` são atualizados para o novo `nint`, `nuint` e `nfloat` tipos.

Por exemplo, dada uma definição de método Objective-C de:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Se o contrato de associação anterior tivesse a seguinte definição:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Atualizaríamos a nova associação será:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Se nós está mapeando para uma biblioteca parte 3 de versão mais recente que o que é inicialmente tinha vinculados a, precisamos examinar o `.h` arquivos de cabeçalho para a biblioteca e veja se houver chamadas explícitas existentes para `int`, `int32_t`, `unsigned int`, `uint32_t` ou `float` foram atualizados para ser um `NSInteger`, `NSUInteger` ou `CGFloat`. Nesse caso, as mesmas modificações para o `nint`, `nuint` e `nfloat` tipos precisam ser feitas para seus mapeamentos bem.

Para saber mais sobre essas alterações de tipo de dados, consulte o [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

## <a name="update-the-coregraphics-types"></a>Atualizar os tipos de CoreGraphics

Os tipos de dados de ponto, o tamanho e o retângulo que são usados com `CoreGraphics` usam 32 ou 64 bits dependendo do dispositivo estão sendo executados em. Quando Xamarin vinculado originalmente o iOS e Mac APIs usamos estruturas de dados que correspondam a tipos de dados em `System.Drawing` (`RectangleF` por exemplo).

Por causa dos requisitos para dar suporte a 64 bits e os novos tipos de dados nativos, os seguintes ajustes precisa ser feita para o código existente ao chamar `CoreGraphic` métodos:

- **CGRect** -Use `CGRect` em vez de `RectangleF` quando definindo flutuante ponto regiões retangulares.
- **CGSize** -Use `CGSize` em vez de `SizeF` ao definir os tamanhos de ponto flutuante (largura e altura).
- **CGPoint** -Use `CGPoint` em vez de `PointF` quando definir um flutuante ponto local (coordenadas X e Y).

Devido ao acima mencionado, precisamos examinar nossa API e certifique-se de que qualquer instância do `CGRect`, `CGSize` ou `CGPoint` que foi associado anteriormente a `RectangleF`, `SizeF` ou `PointF` ser alterado para o tipo nativo `CGRect`, `CGSize` ou `CGPoint` diretamente.

Por exemplo, dado um inicializador Objective-C de:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Se nosso associação anterior incluía o código a seguir:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Atualizaríamos o código para:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Com todas as alterações de código já em vigor, é preciso modificar nosso projeto de associação ou tornar arquivo ligar as APIs unificado.

## <a name="modify-the-binding-project"></a>Modificar o projeto de vinculação

Como a etapa final para atualizar nosso projeto de vinculação para usar as APIs unificado, é preciso modificar o `MakeFile` que usamos para compilar o projeto ou o tipo de projeto Xamarin (se estamos associando de dentro do Visual Studio para Mac) e instruir _btouch_  ligar as APIs unificado em vez dos clássica.


### <a name="updating-a-makefile"></a>Atualizando um MakeFile

Se estiver usando um makefile para compilar nosso projeto de associação em um Xamarin. DLL, será necessário incluir o `--new-style` opção de linha de comando e chame `btouch-native` em vez de `btouch`.

Assim, considerando o seguinte `MakeFile`:

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

É necessário alternar da chamada `btouch` para `btouch-native`, portanto, vamos seria ajustar nossa definição de macro da seguinte maneira:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Atualizaríamos a chamada para `btouch` e adicione o `--new-style` opção da seguinte maneira:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Agora podemos executar nosso `MakeFile` normalmente para criar a nova versão de 64 bits da nossa API.

### <a name="updating-a-binding-project-type"></a>Atualizar um tipo de projeto de vinculação

Se estiver usando um Visual Studio para Mac associação de modelo de projeto para criar nossa API, precisaremos atualizar para a nova versão de API unificada a associação do modelo de projeto. A maneira mais fácil de fazer isso é iniciar um novo projeto de associação de API unificada e cópia sobre todas as configurações e o código existente.

Faça o seguinte:

1. Inicie o Visual Studio para Mac.
2. Selecione **arquivo** > **novo** > **solução...**
3. Na caixa de diálogo nova solução, selecione **iOS** > **API unificada** > **iOS projeto associação**: 

    [![](update-binding-images/image01new.png "Na caixa de diálogo nova solução, selecionar o iOS / API unificada / projeto de vinculação do iOS")](update-binding-images/image01new.png#lightbox)
4. Na caixa de diálogo 'Configurar seu novo projeto' Insira uma **nome** para o novo projeto de vinculação e clique o **Okey** botão.
5. Inclui a versão de 64 bits da biblioteca Objective-C que você pretende criar associações.
6. Copie o código-fonte do seu projeto existente de associação de API clássica de 32 bits (como o `ApiDefinition.cs` e `StructsAndEnums.cs` arquivos).
7. Faça as alterações de mencionado acima para os arquivos de código fonte.

Todas essas alterações em vigor, você pode criar a nova versão de 64 bits da API de como você faria com a versão de 32 bits.

## <a name="summary"></a>Resumo

Neste artigo, mostramos as alterações que precisam ser feitas a um projeto existente de associação Xamarin para dar suporte a novas APIs unificado e dispositivos de 64 bits e as etapas necessárias para criar a nova versão compatível do 64 bits de uma API.



## <a name="related-links"></a>Links relacionados

- [Mac e iOS](~/cross-platform/macios/index.md)
- [API Unificada](~/cross-platform/macios/nativetypes.md)
- [Considerações sobre a plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md)
- [Atualizando aplicativos do iOS existente](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API Unificada](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
