---
title: Xamarin 9 – solução de problemas
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com iOS 9 no xamarin. Dicas abordam a análise de XML, simuladores, restrições de layout, problemas de rede e muitos outros tópicos.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: c44d737efcf5092eb4b27d5311271005de65318b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787657"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin 9 – solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com iOS 9 em aplicativos xamarin._

## <a name="there-was-a-problem-parsing-the-xml"></a>Houve um problema ao analisar o XML

O Designer do Xamarin iOS ainda não suporta recursos Xcode 7. Storyboards não carregarão no designer com _"Ocorreu um problema ao analisar o XML"_ ao tentar usar o novo iOS 9 (Xcode 7) designers elementos como StackView.

suporte para o Designer de recursos do Xcode 7 iOS destina-se para a próxima versão do recurso de 6 de ciclo. A versão de visualização do ciclo de 6 está disponível atualmente no canal alfa e tem suporte limitado para os novos recursos do Xcode 7.

Solução alternativa parcial para o Visual Studio para Mac: clique o storyboard e escolha **abrir com** > **Xcode Interface construtor**.

## <a name="where-are-the-ios-8-simulators"></a>Onde estão os iOS 8 simuladores?

Se você instalou o Xcode 7 (ou superior) automaticamente substituirá todos os simuladores de iOS 8 com iOS 9 simuladores por padrão. Se você ainda precisa testar no iOS 8, você pode iniciar Xcode, em seguida, baixe e instale os simuladores de iOS 8.

No Xcode, selecione o **Xcode** , em seguida, o menu **preferências...**   >  **Downloads**:

[![](troubleshooting-images/ios8.png "Downloads de simuladores de iOS 8")](troubleshooting-images/ios8.png#lightbox)

Clique o **verificação e instalar agora** botão reinstalar os simuladores de iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Restrição de layout com erros de atributo esquerda/direita

No iOS 8 (e versões anteriores), elementos de interface do usuário em Storyboards podem usar uma combinação de ambos **direita** & **esquerda** atributos (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) e  **À esquerda** & **direita** atributos (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) no layout do mesmo.

Se o Storyboard mesmo em executar no iOS 9, resultará em uma exceção no seguinte formato:

> Finalizando o aplicativo devido uma exceção não percebida 'NSInvalidArgumentException', motivo: '*** + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: uma restrição não pode ser feita entre à esquerda/à direita atributo e um atributo de direita ou esquerda. Use à esquerda/à direita para ambos ou nenhum.'

iOS 9 impõe layouts para usar um **direita** & **esquerda** _ou_ **esquerda**  &   **À direita** atributos mas *não* ambos. Para corrigir esse problema, altere todas as restrições de layout para usar o mesmo atributo definido em seu arquivo de Storyboard.

Para obter mais informações, consulte o [erro de restrição do iOS 9](http://stackoverflow.com/questions/32692841/ios-9-constraint-error) discussão de estouro de pilha.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>Erro ITMS-90535: Chave de CFBundleExecutable inesperado

Depois de alternar para o iOS 9, de um aplicativo usa 3ª parte componentes (especificamente nosso componente existente do Google Maps) compilado e executaram no iOS 8 (ou anterior), ao tentar enviar a nova compilação para iTunes conectar, você pode obter um erro no formato:

> Erro ITMS-90535: Chave de CFBundleExecutable inesperado. O pacote em 'Payload/app-name.app/component.bundle' não contém um executável do pacote...

Esse problemas podem normalmente ser resolvido ao localizar o pacote nomeado no projeto e - assim como a mensagem de erro sugere - editado o `Info.plist` que está no pacote, removendo o `CFBundleExecutable` chave. O `CFBundlePackageType` chave deve ser definida como `BNDL` também.

Depois de fazer essas alterações, fazer uma limpeza e recompilar o projeto inteiro. Você deve ser capaz de enviar para conectar-se de iTunes sem problemas depois de fazer essas alterações.

Para obter mais informações, consulte esta [estouro de pilha](http://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) discussão.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Falha de CFNetwork SSLHandshake erro (-9824)

Ao tentar se conectar à internet, seja diretamente ou de uma exibição da web no iOS 9, você poderá receber um erro no formato:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

Ou, no formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

IOS9, segurança de transporte do aplicativo (ATS) estabelece conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e seu aplicativo. Além disso, ATS requer comunicação usando o `HTTPS` comunicação alto nível da API a ser criptografado usando TLS versão 1.2 com sigilo e protocolo.

Como ATS é habilitada por padrão em aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando `NSURLConnection`, `CFURL` ou `NSURLSession` estarão sujeitos às necessidades de segurança ATS. Se as conexões não atender a esses requisitos, ele falhará com uma exceção.

Consulte o [Opting-Out de ATS](~/ios/app-fundamentals/ats.md) seção do nosso [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md) guia para obter informações sobre como resolver esse problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Meus aplicativos existentes não são executados no iOS 9

Consulte nossa [informações de compatibilidade do iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) para obter instruções sobre como compilar novamente e reimplantar seus aplicativos existentes executar no iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView é Null em construtores

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

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView Falha ao inicializar com o codificador ao carregar uma exibição de um Xib/Nib

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

## <a name="dyld-message-no-cache-image-with-name"></a>Mensagem Dyld: Nenhuma imagem de Cache com nome...

Você pode enfrentar uma falha com as seguintes informações no log de:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** esse é um bug no vinculador nativo da Apple, que acontece quando eles fizerem uma estrutura particular público (JavaScriptCore se tornou público no iOS 7, antes que fosse uma estrutura particular), e é o destino de implantação do aplicativo para uma versão do iOS quando o estrutura foi privada. Nesse caso o vinculador da Apple vinculará com a versão particular do framework em vez da versão pública.

**Correção:** isso será resolvido para iOS 9, mas não há uma solução fácil aplicar-se enquanto: apenas uma versão iOS mais recente em seu projeto (você pode tentar iOS 7 nesse caso) de destino. Outras estruturas podem apresentar problemas semelhantes, por exemplo o framework WebKit foi publicado no iOS 8 (e para que direcionamento iOS 7 resultará nesse erro; você deve visar iOS 8 use WebKit em seu aplicativo).

## <a name="untrusted-enterprise-developer"></a>Desenvolvedor empresarial não confiável

Ao tentar executar a versão do iOS 9 do seu aplicativo xamarin no hardware real iOS, você pode obter uma mensagem informando que sua conta de desenvolvedor não foram confiável no dispositivo. Por exemplo:

[![](troubleshooting-images/untrusted01.png "Alerta de desenvolvedor empresarial não confiável")](troubleshooting-images/untrusted01.png#lightbox)

Para resolver esse problema, faça o seguinte:

1. Iniciar o Xcode (a versão mais recente do beta) no desenvolvimento de Mac.
2. Selecione **dispositivos** do **janela** menu para abrir a janela de dispositivos: 

    [![](troubleshooting-images/untrusted02.png "A janela de dispositivos")](troubleshooting-images/untrusted02.png#lightbox)
3. Sob o **dispositivos** do lado do painel, selecione seu dispositivo, o botão direito do mouse e selecione **Mostrar perfis de provisionamento...** : 

    [![](troubleshooting-images/untrusted03.png "Perfis de provisionamento de SShow")](troubleshooting-images/untrusted03.png#lightbox)
4. Selecione cada perfil de provisionamento no momento no dispositivo e clique no **-** botão para excluí-lo: 

    [![](troubleshooting-images/untrusted04.png "Excluir um perfil de provisionamento")](troubleshooting-images/untrusted04.png#lightbox)
5. Do **Xcode** menu, selecione **preferências...**  e **contas**: 

    [![](troubleshooting-images/untrusted05.png "Preferências da conta Xcode")](troubleshooting-images/untrusted05.png#lightbox)
6. Clique o **exibir detalhes...**  botão e, em seguida, clique no **baixar todos os** botão: 

    [![](troubleshooting-images/untrusted06.png "Baixar todos os perfis")](troubleshooting-images/untrusted06.png#lightbox)
7. Quando a lista concluiu a atualização, clique no **feito** botão e feche a janela de preferências.
8. Remova a versão existente do aplicativo xamarin que você está tentando testar do dispositivo iOS.
9. Retorne ao Visual Studio para Mac, não uma compilação limpa e tente executar novamente o aplicativo no dispositivo.

Você terá que parar e reiniciar o Visual Studio para Mac antes de novos perfis de provisionamento carregados pelo Xcode são vistos. Você também pode precisar ajustar a **iOS de assinatura de pacote** opções para seu aplicativo xamarin selecionar os perfis de provisionamento de novo.

## <a name="launch-screen-issues"></a>Inicie os problemas de tela

iOS 9 agora impõe os requisitos da tela Iniciar para que a mesma imagem de inicialização não pode ser reutilizada para dar suporte a orientações de interface diferente. Consulte da Apple [UILanchImage referência](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) para obter mais informações.

Opcionalmente, você pode usar um arquivo de storyboard para apresentar a tela Iniciar do seu aplicativo em vez de usar um conjunto de **. PNG** arquivos de imagem. Isso é agora Apple preferencial apresentar as telas de inicialização. Confira nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guia para obter mais informações.

Por fim, seu aplicativo deve usar um arquivo de storyboard para a tela Iniciar e dar suporte a todas as orientações de quatro interface (retrato, cabeça para baixo retrato, paisagem esquerda e direita paisagem) a serem considerados para execução em um painel de Slide sobre ou no modo de exibição de divisão. Para obter mais informações sobre a nova capacidade de multitarefa do iOS 9, consulte nosso [multitarefa para iPad](~/ios/platform/multitasking.md) guia.

## <a name="nsinternalinconsistencyexception-exception"></a>Exceção NSInternalInconsistencyException

Ao compilar e executar um aplicativo xamarin existente para iOS 9, você poderá receber um erro no formato:

> Objective-C exceção é gerada.  Nome: NSInternalInconsistencyException motivo: aplicativo windows devem ter um controlador de exibição de raiz no final de iniciar o aplicativo

Este é o erro está sendo gerado porque o aplicativo Windows devem ter um controlador de exibição de raiz no final de iniciar o aplicativo e não de seu aplicativo existente.

Há pelo menos duas soluções possíveis para esse problema:

1. Atualização de aplicativo para usar o arquivo de storyboard, em vez de `xib` arquivos para definir sua Interface do usuário. Esse layout storyboards requer muito tempo, dependendo do tamanho do aplicativo e o conhecimento de como usar o Designer (ou o construtor de Interface do Xcode) do iOS. Para obter mais informações, consulte nosso [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.
2. Instalação `RootViewController` propriedade da janela do aplicativo em `FinishedLaunching` método `AppDelegate` classe para apontar para um controlador de exibição na interface de usuário do seu aplicativo.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quando inicializar exibições e controladores de exibição

Com xamarin, é possível fazer a inicialização de exibição ou controlador de exibição dentro dos construtores que são chamados quando algo é exposto em código gerenciado, mas quebras iOS design.

Em geral você deve inicializar tudo o que pode chamar novamente código Objective-C do construtor desde que você não pode ter certeza de que não quando ele será chamado. Que também significa que há um melhores locais (outros ctor) ou chamadas para substituir (como Objective-C não possui eventos) onde essa inicialização deve ser feita.

## <a name="related-links"></a>Links relacionados

- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Atualizar seus aplicativos xamarin para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
