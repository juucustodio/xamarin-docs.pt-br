---
title: Xamarin. iOS 9 – solução de problemas
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com o iOS 9 no Xamarin. iOS. As dicas abordam a análise de XML, simuladores, restrições de layout, problemas de rede e muitos outros tópicos.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 437698fcda6e85090cd7bdce90959300436e0bc2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031756"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. iOS 9 – solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com o iOS 9 em aplicativos Xamarin. iOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Houve um problema ao analisar o XML

O Xamarin iOS designer ainda não dá suporte aos recursos do Xcode 7. Os storyboards não serão carregados no designer com _"ocorreu um problema ao analisar o XML"_ ao tentar usar novos elementos do designer do IOS 9 (Xcode 7), como StackView.

o suporte ao designer do iOS para recursos do Xcode 7 destina-se à próxima versão do recurso do ciclo 6. A versão de visualização do ciclo 6 está disponível no canal alfa e tem suporte limitado para os novos recursos do Xcode 7.

Solução alternativa parcial para Visual Studio para Mac: clique com o botão direito do mouse no storyboard e escolha **abrir com** > **Xcode Interface Builder**.

## <a name="where-are-the-ios-8-simulators"></a>Onde estão os simuladores do iOS 8?

Se você tiver instalado o Xcode 7 (ou superior), ele substituirá automaticamente todos os simuladores do iOS 8 pelos simuladores do iOS 9 por padrão. Se você ainda precisar testar no iOS 8, poderá iniciar o Xcode, baixar e instalar os simuladores do iOS 8.

No Xcode, selecione o menu do **Xcode** e, em seguida, **preferências...**  > **downloads**:

[![](troubleshooting-images/ios8.png "iOS 8 Simulators Downloads")](troubleshooting-images/ios8.png#lightbox)

Clique no botão **verificar e instalar agora** para reinstalar os simuladores do IOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Restrição de layout com erros de atributo esquerdo/direito

No iOS 8 (e anteriores), os elementos da interface do usuário em storyboards poderiam usar uma combinação de atributos à **direita** & **à esquerda** (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) e os **principais** atributos de & **à direita** (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) no mesmo layout.

Se o mesmo storyboard em execução no iOS 9, ele resultará em uma exceção no seguinte formato:

> Encerrando o aplicativo devido à exceção não percebida ' NSInvalidArgumentException ', motivo: ' * * * + [NSLayoutConstraint constraintWithItem: atributo: relatedBy: toitem: atributo: multiplicador: Constant:]: não é possível estabelecer uma restrição entre uma entrelinha/à direita atributo e um atributo à direita/esquerda. Use à esquerda/à direita para ambos ou nenhum deles.

o iOS 9 impõe layouts para usar **os atributos à** **direita** & **esquerda** _ou_ **entrelinha** & , mas *não* ambos. Para corrigir esse problema, altere todas as restrições de layout para usar o mesmo conjunto de atributos dentro de seu arquivo de storyboard.

Para obter mais informações, consulte o [erro de restrição do IOS 9](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) Stack Overflow discussão.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ERRO ITMS-90535: chave de CFBundleExecutable inesperada

Depois de alternar para o iOS 9, de um aplicativo usa componentes de terceiros (especificamente nosso componente existente do Google Maps) que foi compilado e executado no iOS 8 (ou anterior), ao tentar enviar a nova compilação ao iTunes Connect, você pode obter um erro no formulário:

> ERRO ITMS-90535: chave de CFBundleExecutable inesperada. O pacote em ' Payload/app-Name. app/Component. Bundle ' não contém um executável de pacote...

Normalmente, esses problemas podem ser resolvidos encontrando o pacote nomeado no projeto, assim como a mensagem de erro sugere-editou o `Info.plist` que está no pacote removendo a chave de `CFBundleExecutable`. A chave de `CFBundlePackageType` deve ser definida como `BNDL` também.

Depois de fazer essas alterações, faça uma limpeza e reconstrua o projeto inteiro. Você deve ser capaz de enviar para o iTunes Connect sem problemas depois de fazer essas alterações.

Para obter mais informações, consulte esta discussão [Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) .

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake falhou (-9824) erro

Ao tentar se conectar à Internet, seja diretamente ou de uma exibição da Web no iOS 9, você pode receber um erro no formato:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

Ou no formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

No iOS9, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e seu aplicativo. Além disso, o ATS exige a comunicação usando o protocolo de `HTTPS` e a comunicação de API de alto nível a ser criptografada usando TLS versão 1,2 com sigilo de encaminhamento.

Como o ATS está habilitado por padrão em aplicativos criados para iOS 9 e OS X 10,11 (El Capitan), todas as conexões que usam `NSURLConnection`, `CFURL` ou `NSURLSession` estarão sujeitas a requisitos de segurança de ATS. Se suas conexões não atenderem a esses requisitos, elas falharão com uma exceção.

Consulte a seção [recusando o ATS](~/ios/app-fundamentals/ats.md) do nosso guia de [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md) para obter informações sobre como resolver esse problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Meus aplicativos existentes não são executados no iOS 9

Consulte nossas [informações de compatibilidade do IOS 9](~/ios/platform/introduction-to-ios9/ios9.md) para obter instruções sobre como criar novamente e reimplantar seus aplicativos existentes para execução no Ios 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView é nulo em construtores

**Motivo:** No iOS 9, o construtor de `initWithFrame:` agora é necessário, devido a alterações de comportamento no iOS 9 como os [Estados de documentação do UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Se você registrou uma classe para o identificador especificado e uma nova célula deve ser criada, a célula agora é inicializada chamando seu método `initWithFrame:`.

**Correção:** Adicione o Construtor `initWithFrame:` como este:

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplos relacionados: [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView falha ao iniciar com codificador ao carregar uma exibição de um XIB/NIB

**Motivo:** O construtor de `initWithCoder:` é o chamado ao carregar uma exibição de um arquivo de Interface Builder xib. Se esse construtor não for exportado, o código não gerenciado não poderá chamar nossa versão gerenciada dele. Anteriormente (por exemplo, no iOS 8), o construtor de `IntPtr` foi invocado para inicializar a exibição.

**Correção:** Crie e exporte o Construtor `initWithCoder:` como este:

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemplo relacionado: [chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

## <a name="dyld-message-no-cache-image-with-name"></a>Mensagem de dyld: nenhuma imagem de cache com o nome...

Você pode encontrar uma falha com as seguintes informações no log:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Esse é um bug no vinculador nativo da Apple, que acontece quando eles tornam uma estrutura privada pública (JavaScriptCore se tornou pública no iOS 7, antes de ser uma estrutura privada), e o destino de implantação do aplicativo é para uma versão do iOS quando a estrutura era privada. Nesse caso, o vinculador da Apple vinculará a versão particular da estrutura em vez da versão pública.

**Correção:** Isso será abordado para o iOS 9, mas há uma solução alternativa fácil que você pode se aplicar nesse meio tempo: Basta direcionar para uma versão mais recente do iOS no seu projeto (você pode tentar o iOS 7 nesse caso). Outras estruturas podem apresentar problemas semelhantes, por exemplo, a estrutura WebKit tornou-se pública no iOS 8 (e, portanto, direcionar para o iOS 7 resultará nesse erro; você deve direcionar o iOS 8 para usar WebKit em seu aplicativo).

## <a name="untrusted-enterprise-developer"></a>Desenvolvedor empresarial não confiável

Ao tentar executar a versão iOS 9 do seu aplicativo Xamarin. iOS em um hardware iOS real, você poderá receber uma mensagem informando que sua conta de desenvolvedor não é confiável no dispositivo. Por exemplo:

[![](troubleshooting-images/untrusted01.png "Untrusted Enterprise Developer alert")](troubleshooting-images/untrusted01.png#lightbox)

Para resolver esse problema, faça o seguinte:

1. Inicie o Xcode (a versão beta mais recente) no Mac de desenvolvimento.
2. Selecione **dispositivos** no menu **janela** para abrir a janela dispositivos: 

    [![](troubleshooting-images/untrusted02.png "The Devices Window")](troubleshooting-images/untrusted02.png#lightbox)
3. No painel do lado dos **dispositivos** , selecione seu dispositivo, clique com o botão direito do mouse e selecione **Mostrar perfis de provisionamento...** : 

    [![](troubleshooting-images/untrusted03.png "SShow Provisioning Profiles")](troubleshooting-images/untrusted03.png#lightbox)
4. Selecione cada perfil de provisionamento atualmente no dispositivo e clique no botão **-** para excluí-lo: 

    [![](troubleshooting-images/untrusted04.png "Deleting a provisioning profile")](troubleshooting-images/untrusted04.png#lightbox)
5. No menu do **Xcode** , selecione **preferências...** e **contas**: 

    [![](troubleshooting-images/untrusted05.png "Xcode account preferences")](troubleshooting-images/untrusted05.png#lightbox)
6. Clique no botão **Exibir detalhes...** e, em seguida, clique no botão **baixar tudo** : 

    [![](troubleshooting-images/untrusted06.png "Download all profiles")](troubleshooting-images/untrusted06.png#lightbox)
7. Quando a atualização da lista for concluída, clique no botão **concluído** e feche a janela preferências.
8. Remova a versão existente do aplicativo Xamarin. iOS que você estava tentando testar do dispositivo iOS.
9. Retorne ao Visual Studio para Mac, faça uma compilação limpa e tente executar novamente o aplicativo no dispositivo.

Talvez seja necessário parar e reiniciar Visual Studio para Mac antes que os novos perfis de provisionamento carregados pelo Xcode sejam vistos. Você também pode precisar ajustar as opções de **assinatura do pacote do IOS** para seu aplicativo Xamarin. Ios para selecionar os novos perfis de provisionamento.

## <a name="launch-screen-issues"></a>Problemas de tela de inicialização

Agora, o iOS 9 impõe os requisitos da tela de inicialização para que a mesma imagem de inicialização não possa mais ser reutilizada para dar suporte a orientações de interface diferentes. Consulte a [referência do UILanchImage](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) da Apple para obter mais informações.

Opcionalmente, você pode usar um arquivo de storyboard para apresentar a tela de inicialização do seu aplicativo em vez de usar um conjunto de arquivos de imagem **. png** . Agora, essa é a maneira preferida da Apple de apresentar telas de inicialização. Consulte nosso guia [de introdução ao storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) para obter mais informações.

Por fim, seu aplicativo deve usar um arquivo de storyboard para sua tela de inicialização e dar suporte a todas as quatro orientações de interface (retrato, retrato de cabeça para baixo, paisagem para a esquerda e paisagem para a direita) a serem consideradas para execução em um slide sobre o painel ou no modo de exibição de divisão. Para saber mais sobre as novas capacidades de multitarefa do iOS 9, consulte nosso guia [multitarefa para iPad](~/ios/platform/multitasking.md) .

## <a name="nsinternalinconsistencyexception-exception"></a>Exceção NSInternalInconsistencyException

Ao compilar e executar um aplicativo Xamarin. iOS existente para iOS 9, você pode receber um erro no formato:

> Exceção de Objective-C lançada.  Nome: NSInternalInconsistencyException motivo: espera-se que as janelas de aplicativo tenham um controlador de exibição raiz no final da inicialização do aplicativo

Esse erro está sendo gerado porque espera-se que as janelas de aplicativo tenham um controlador de exibição raiz no final da inicialização do aplicativo e seu aplicativo existente não.

Há pelo menos duas soluções alternativas possíveis para esse problema:

1. Atualize o aplicativo para usar o arquivo de storyboard em vez de `xib` arquivos para definir sua interface do usuário. Isso requer muito tempo, dependendo do tamanho do seu aplicativo e do conhecimento de como usar o designer do iOS (ou o Interface Builder do Xcode) para o layout de storyboards. Para obter mais informações, consulte nossa [introdução à documentação de storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .
2. Configure `RootViewController` propriedade da janela do aplicativo no método `FinishedLaunching` na classe `AppDelegate` para apontar para um controlador de exibição na interface do usuário do seu aplicativo.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quando inicializar exibições e controladores de exibição

Com o Xamarin. iOS, é possível tornar a inicialização do controlador View ou View dentro de construtores que são chamados quando algo é exposto em código gerenciado, mas quebra o design do iOS.

Em geral, você não deve inicializar nada que possa chamar o código de Objective-C do Construtor, uma vez que não é possível ter certeza de que ele será chamado. Isso também significa que há um local melhor (outro. ctor) ou chamadas a serem substituídas (já que Objective-C não tem eventos) em que essa inicialização deve ser feita.

## <a name="related-links"></a>Links relacionados

- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Atualizando seus aplicativos Xamarin. iOS para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
