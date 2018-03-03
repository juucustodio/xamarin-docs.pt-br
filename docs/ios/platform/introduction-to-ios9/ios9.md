---
title: iOS 9 compatibilidade
description: "Mesmo se você não planeja adicionar recursos do iOS 9 no seu aplicativo imediatamente, você deve recompilar seus aplicativos com a versão mais recente do Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bfdf0c73226eec472eb671d5543f5ce124919ab8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="ios-9-compatibility"></a>iOS 9 compatibilidade

_Mesmo se você não planeja adicionar recursos do iOS 9 no seu aplicativo imediatamente, você deve recompilar seus aplicativos com a versão mais recente do Xamarin._

> [!IMPORTANT]
> **Observação:** essas informações nessa página são para os clientes com aplicativos de loja de aplicativos direcionamento iOS 8 ou anterior, já que já não enviar atualizações para a compatibilidade do iOS 9. Se você já estiver usando as versões mais recentes - Xcode 7 e 9 xamarin - para o desenvolvimento de aplicativos, visitem o [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Quando apareceram as primeiro betas iOS 9, identificamos dois problemas com versões anteriores do Xamarin declarado como sendo não é possível iniciar no iOS 9 de aplicativos mais antigos.

Esses dois problemas (como [detalhadas em nossos fóruns](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) foram:

- Compilar aplicativos para iOS 8 ou anterior não conseguir iniciar em dispositivos de 32 bits (incluindo aplicativos compilados com o [API unificada](~/cross-platform/macios/unified/index.md)).
- P/Invoke falhando com o caminho completo não está especificado.

Atualizar a instalação do Xamarin para o mais recente versão estável de canal, recompilar e reimplantar seus aplicativos, corrige esses dois problemas.

_Mesmo se você não estiver planejando atualizar seu aplicativo com os recursos do iOS 9 imediatamente, recomendamos que você compilar novamente com a última versão do Xamarin e envie-o novamente para a loja de aplicativos_.



Isso garantirá que o aplicativo será executado no iOS 9, depois de atualizar seus clientes.
Você pode continuar a oferecer suporte a iOS 8 - recriação com a versão mais recente não afeta a versão de destino do aplicativo.

Se você tiver problemas adicionais ao testar seus aplicativos existentes no iOS 9, leia o [melhorando compatibilidade](#compat) seção abaixo.


### <a name="updating-with-visual-studio"></a>Atualizando com o Visual Studio

É recomendável que você verifique explicitamente que o Visual Studio é atualizado para a versão estável mais recente.

## <a name="what-about-components-nugets-and-other-libraries"></a>E outras bibliotecas de componentes e Nugets?

Fazer **não** precisa esperar para novas versões de quaisquer componentes ou Nugets que você está usando para resolver os dois problemas mencionados acima.
Esses problemas forem corrigidos simplesmente criando novamente seu aplicativo com a versão estável mais recente do xamarin.

Da mesma forma, os fornecedores de componentes e os autores do Nuget são **não** necessário para enviar novas compilações apenas para corrigir os dois problemas mencionados acima. No entanto, se houver um componente ou Nuget usa `UICollectionView` ou carregar modos de exibição de **Xib** arquivos, uma atualização *pode* ser necessárias para resolver os problemas de compatibilidade do iOS 9 mencionados abaixo.


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Melhorando a compatibilidade em seu código

Há alguns casos de código padrões que *usado* para trabalhar em versões anteriores do iOS recentes no iOS 9. Aqui estão alguns possíveis problemas (e suas soluções) que podem surgir durante o teste no iOS 9:

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView é nulo em construtores

**Motivo:** no iOS 9 a `initWithFrame:` construtor agora é necessária devido a alterações de comportamento no iOS 9, como o [UICollectionView documentação declara](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se você registrou uma classe para o identificador especificado e deve ser criada uma nova célula, a célula agora é inicializada chamando seu `initWithFrame:` método.

**Correção:** adicionar o `initWithFrame:` construtor assim:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplos relacionados ao: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView Falha ao inicializar com o codificador ao carregar uma exibição de um Xib/Nib

**Motivo:** o `initWithCoder:` construtor é a chamada ao carregar uma exibição de um arquivo Xib de construtor da Interface. Se este construtor não é exportado o código não gerenciado não é possível chamar nossa versão gerenciada dele. Anteriormente (por exemplo. no iOS 8) o `IntPtr` construtor foi chamado para inicializar o modo de exibição.

**Correção:** criar e exportar o `initWithCoder:` construtor assim:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplo relacionado: [bate-papo](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Mensagem Dyld: nenhuma imagem de cache com o nome...

Você pode enfrentar uma falha com as seguintes informações no log de:

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** esse é um bug no vinculador nativo da Apple, que acontece quando eles fizerem uma estrutura particular público (JavaScriptCore se tornou público no iOS 7, antes que fosse uma estrutura particular), e é o destino de implantação do aplicativo para uma versão do iOS quando o estrutura foi privada. Nesse caso o vinculador da Apple vinculará com a versão particular do framework em vez da versão pública.

**Correção:** isso será resolvido para iOS 9, mas não há uma solução fácil aplicar-se enquanto: apenas uma versão iOS mais recente em seu projeto (você pode tentar iOS 7 nesse caso) de destino. Outras estruturas podem apresentar problemas semelhantes, por exemplo o framework WebKit foi publicado no iOS 8 (e para que direcionamento iOS 7 resultará nesse erro; você deve visar iOS 8 use WebKit em seu aplicativo).



## <a name="related-links"></a>Links relacionados

- [informações de versão de compatibilidade do iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introdução ao iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Atualizar seus aplicativos xamarin para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
