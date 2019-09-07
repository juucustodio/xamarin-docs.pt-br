---
title: Compatibilidade com o iOS 9
description: Mesmo que você não pretenda adicionar recursos do iOS 9 ao seu aplicativo imediatamente, você deve recompilar seus aplicativos com a versão mais recente do Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 246653cee7917141ddd0f911a7c4d1b21f945360
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70751970"
---
# <a name="ios-9-compatibility"></a>Compatibilidade com o iOS 9

_Mesmo que você não pretenda adicionar recursos do iOS 9 ao seu aplicativo imediatamente, você deve recompilar seus aplicativos com a versão mais recente do Xamarin._

> [!IMPORTANT]
> As informações nesta página são para clientes com aplicativos já existentes na loja de aplicativos direcionados para o iOS 8 ou anterior, que ainda não enviaram atualizações para a compatibilidade com o iOS 9. Se você já estiver usando as versões mais recentes – Xcode 7 e Xamarin. iOS 9-para o desenvolvimento de seu aplicativo, visite a [introdução ao Ios 9](~/ios/platform/introduction-to-ios9/index.md).

Quando a primeira versão beta do iOS 9 apareceu, identificamos dois problemas com versões anteriores do Xamarin que manifestavam como aplicativos mais antigos que não conseguem iniciar no iOS 9.

Esses dois problemas (conforme [detalhado em nossos fóruns](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) foram:

- Aplicativos criados para iOS 8 ou anterior não podem ser iniciados em dispositivos de 32 bits (incluindo aplicativos criados com o [API unificada](~/cross-platform/macios/unified/index.md)).
- Falha de P/Invoke com o caminho completo não especificado.

Atualizar a instalação do Xamarin para a versão de canal estável mais recente e, em seguida, recompilar e reimplantar seus aplicativos, corrige esses dois problemas.

_Mesmo que você não esteja planejando atualizar seu aplicativo com os recursos do IOS 9 imediatamente, recomendamos que você recrie com a versão mais recente do Xamarin e envie novamente para a loja de aplicativos_.

Isso garantirá que seu aplicativo será executado no iOS 9 após a atualização dos clientes.
Você pode continuar a dar suporte ao iOS 8-a recriação com a versão mais recente não afeta a versão de destino do aplicativo.

Se você tiver problemas adicionais ao testar seus aplicativos existentes no iOS 9, leia a seção [melhorando a compatibilidade](#compat) abaixo.

### <a name="updating-with-visual-studio"></a>Atualizando com o Visual Studio

É recomendável verificar explicitamente se o Visual Studio foi atualizado para a versão estável mais recente.

## <a name="what-about-components-nugets-and-other-libraries"></a>E quanto a componentes, Nugets e outras bibliotecas?

Você **não** precisa esperar por novas versões de quaisquer componentes ou Nugets que esteja usando para resolver os dois problemas mencionados acima.
Esses problemas são corrigidos simplesmente recriando seu aplicativo com a versão estável mais recente do Xamarin. iOS.

Da mesma forma, os fornecedores de componentes e os autores do NuGet **não** precisam enviar novas compilações apenas para corrigir os dois problemas mencionados acima. No entanto, se qualquer componente ou NuGet `UICollectionView` usar ou carregar exibições de arquivos **XIB** , uma atualização *poderá* ser necessária para resolver os problemas de compatibilidade do IOS 9 mencionados abaixo.

<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Aprimorando a compatibilidade em seu código

Há alguns casos de padrões de código que *costumava* trabalhar em versões mais antigas da interrupção do iOS no Ios 9. Aqui estão alguns problemas possíveis (e suas soluções) que podem surgir durante o teste no iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView é nulo em construtores

**Falha** No Ios 9, `initWithFrame:` o Construtor agora é necessário, devido a alterações de comportamento no Ios 9 como os [Estados de documentação do UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se você registrou uma classe para o identificador especificado e uma nova célula deve ser criada, a célula agora é inicializada chamando `initWithFrame:` seu método.

**Soluciona** Adicione o `initWithFrame:` Construtor da seguinte maneira:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplos relacionados: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView falha ao iniciar com codificador ao carregar uma exibição de um XIB/NIB

**Falha** O `initWithCoder:` Construtor é o chamado ao carregar uma exibição de um arquivo interface Builder xib. Se esse construtor não for exportado, o código não gerenciado não poderá chamar nossa versão gerenciada dele. Anteriormente (por exemplo, no Ios 8), `IntPtr` o Construtor foi invocado para inicializar a exibição.

**Soluciona** Crie e exporte `initWithCoder:` o construtor da seguinte maneira:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplo relacionado: [Com](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

### <a name="dyld-message-no-cache-image-with-name"></a>Mensagem de dyld: nenhuma imagem de cache com o nome...

Você pode encontrar uma falha com as seguintes informações no log:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Falha** Esse é um bug no vinculador nativo da Apple, que acontece quando eles tornam uma estrutura privada pública (JavaScriptCore se tornou pública no iOS 7, antes de ser uma estrutura privada), e o destino de implantação do aplicativo é para uma versão do iOS quando a estrutura era privada. Nesse caso, o vinculador da Apple vinculará a versão particular da estrutura em vez da versão pública.

**Soluciona** Isso será abordado para o iOS 9, mas há uma solução alternativa fácil que você pode se aplicar nesse meio tempo: Basta direcionar para uma versão mais recente do iOS no seu projeto (você pode tentar o iOS 7 nesse caso). Outras estruturas podem apresentar problemas semelhantes, por exemplo, a estrutura WebKit tornou-se pública no iOS 8 (e, portanto, direcionar para o iOS 7 resultará nesse erro; você deve direcionar o iOS 8 para usar WebKit em seu aplicativo).

## <a name="related-links"></a>Links relacionados

- [informações de versão de compatibilidade do iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Atualizando seus aplicativos Xamarin. iOS para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
