---
title: Compatibilidade com o iOS 9
description: Mesmo se você não planeja adicionar funcionalidades do iOS 9 ao seu aplicativo imediatamente, você deve recompilar seus aplicativos com a versão mais recente do Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6ade1c05c8e1cc64a4d24df1284d86175083ab80
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293638"
---
# <a name="ios-9-compatibility"></a>Compatibilidade com o iOS 9

_Mesmo se você não planeja adicionar funcionalidades do iOS 9 ao seu aplicativo imediatamente, você deve recompilar seus aplicativos com a versão mais recente do Xamarin._

> [!IMPORTANT]
> As informações nesta página são para clientes com aplicativos já na App Store destinados ao iOS 8 ou versões anteriores, que não já enviou iOS 9 compatibilidade de atualizações. Se você já estiver usando as versões mais recentes - Xcode 7 e 9 do xamarin. IOS - para o desenvolvimento de aplicativos, visitem o [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Quando os betas do iOS 9 primeiro apareceram, identificamos dois problemas com versões mais antigas do Xamarin manifestado como aplicativos mais antigos não ser capaz de iniciar no iOS 9.

Esses dois problemas (como [detalhadas em nossos fóruns](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) foram:

- Compilação de aplicativos para iOS 8 ou anterior não conseguir iniciar em dispositivos de 32 bits (incluindo os aplicativos criados com o [API unificada](~/cross-platform/macios/unified/index.md)).
- P/Invoke, falhando com o caminho completo não está especificado.

Atualizar sua instalação do Xamarin para a última versão do canal estável e, em seguida, recompilar e reimplantar seus aplicativos, corrige esses dois problemas.

_Mesmo se você não planeja atualizar seu aplicativo com iOS 9 recursos imediatamente, recomendamos que você crie novamente com a versão mais recente do Xamarin e envie-o novamente para o aplicativo Store_.



Isso garantirá que seu aplicativo será executado no iOS 9, depois de atualizar seus clientes.
Você pode continuar a dar suporte a iOS 8 - reconstruir com a versão mais recente não afeta a versão de destino do aplicativo.

Se você tiver problemas adicionais ao testar seus aplicativos existentes do iOS 9, leia as [melhorando compatibilidade](#compat) seção abaixo.


### <a name="updating-with-visual-studio"></a>Atualizando com o Visual Studio

É recomendável que você verificar explicitamente se o Visual Studio foi atualizado para a versão estável mais recente.

## <a name="what-about-components-nugets-and-other-libraries"></a>E outras bibliotecas de componentes e Nugets?

Você faz **não** precisa aguardar novas versões de quaisquer componentes ou Nugets que você está usando para resolver os dois problemas mencionados acima.
Esses problemas foram corrigidos simplesmente criando novamente seu aplicativo com a versão estável mais recente do xamarin. IOS.

Da mesma forma, os fornecedores de componentes e autores de Nuget são **não** necessárias para enviar novas compilações apenas para corrigir os dois problemas mencionados acima. No entanto, se houver um componente ou o Nuget usa `UICollectionView` ou carga de modos de exibição de **Xib** arquivos, uma atualização *pode* ser necessária para resolver os problemas de compatibilidade do iOS 9 mencionados abaixo.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Melhorando a compatibilidade em seu código

Há alguns casos de código que os padrões *usado* para funcionar em versões anteriores do iOS significativas no iOS 9. Aqui estão alguns problemas possíveis (e suas soluções) que podem surgir durante o teste no iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView é nulo em construtores

**Motivo:** No iOS 9 a `initWithFrame:` construtor agora é necessária devido a alterações de comportamento no iOS 9, como o [UICollectionView documentação declara](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se você registrou uma classe para o identificador especificado e deve ser criada uma nova célula, a célula agora é inicializada chamando seu `initWithFrame:` método.

**Correção:** Adicionar o `initWithFrame:` construtor como este:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Amostras relacionadas: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView Falha ao init com o codificador ao carregar um modo de exibição de um Xib/Nib

**Motivo:** O `initWithCoder:` construtor é a chamada quando o carregamento de um modo de exibição de um arquivo Xib do construtor de Interface. Se esse construtor não é exportado o código não gerenciado não é possível chamar nossa versão gerenciada dele. Anteriormente (por exemplo. no iOS 8) o `IntPtr` construtor foi invocado para inicializar o modo de exibição.

**Correção:** Criar e exportar o `initWithCoder:` construtor como este:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplo relacionado: [Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Mensagem Dyld: nenhuma imagem de cache com o nome...

Você pode enfrentar uma falha com as seguintes informações no log:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Este é um bug no vinculador nativo da Apple, que acontece quando eles fizerem uma estrutura privada pública (JavaScriptCore se tornou público no iOS 7, antes que ele era uma estrutura privada), e o destino da implantação do aplicativo é para uma versão iOS quando o framework era privado. Nesse caso, vinculador da Apple será vinculada à versão particular do framework em vez da versão pública.

**Correção:** Isso será abordado para iOS 9, mas não há uma solução fácil aplicar por conta própria nesse: apenas uma versão iOS posterior em seu projeto (você pode tentar iOS 7 neste caso) de destino. Outras estruturas podem apresentar problemas semelhantes, por exemplo o framework do WebKit se tornou público no iOS 8 (e para direcionamento ao iOS 7 irá resultar nesse erro; você deve ter como destino o iOS 8 para usar WebKit em seu aplicativo).



## <a name="related-links"></a>Links relacionados

- [informações de versão de compatibilidade do iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Atualizar seus aplicativos xamarin. IOS para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
