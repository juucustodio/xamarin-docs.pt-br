---
title: Xamarin. IOS 9 – solução de problemas
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com iOS 9 no xamarin. IOS. Dicas de abordam a análise de XML, simuladores, restrições de layout, problemas de rede e muitos outros tópicos.
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: f8fae79af654339b54a8df0d2ea32eef38f34adb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425168"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin. IOS 9 – solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com iOS 9 em aplicativos xamarin. IOS._

## <a name="there-was-a-problem-parsing-the-xml"></a>Houve um problema ao analisar o XML

O Designer do iOS Xamarin ainda não dá suporte para recursos do Xcode 7. Storyboards não carregarão no designer com _"Ocorreu um problema ao analisar o XML"_ ao tentar usar o novo iOS 9 (Xcode 7) elementos de designer, como StackView.

suporte para recursos do Xcode 7 do Designer do iOS é direcionado para a próxima versão do recurso de 6 de ciclo. A versão prévia do ciclo de 6 está disponível atualmente no canal alfa e tem suporte limitado para os novos recursos do Xcode 7.

Solução alternativa parcial para o Visual Studio para Mac: O storyboard com o botão direito e escolha **abrir com** > **Xcode Interface Builder**.

## <a name="where-are-the-ios-8-simulators"></a>Onde estão o iOS 8 simuladores?

Se você tiver instalado o Xcode 7 (ou superior)-lo automaticamente substituirá todos os simuladores de iOS 8 com simuladores de iOS 9 por padrão. Se você ainda precisa testar no iOS 8, você pode iniciar o Xcode, em seguida, baixe e instale os simuladores de iOS 8.

No Xcode, selecione a **Xcode** menu, em seguida, **preferências...**   >  **Downloads**:

[![](troubleshooting-images/ios8.png "Downloads de simuladores de iOS 8")](troubleshooting-images/ios8.png#lightbox)

Clique o **verificar e instalar agora** botão reinstalar os simuladores de iOS 8.

## <a name="layout-constraint-with-leftright-attribute-errors"></a>Restrição de layout com erros de atributo de esquerda/direita

No iOS 8 (e anterior), elementos de interface do usuário em Storyboards poderia usar uma mistura de ambos **direita** & **esquerda** atributos (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) e  **Levando** & **à direita** atributos (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) no mesmo layout.

Se o Storyboard mesmo em executar no iOS 9, isso resultará em uma exceção no seguinte formato:

> Encerrando o aplicativo devido a exceção não percebida 'NSInvalidArgumentException', motivo: ' * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: Uma restrição não pode ser feita entre um atributo à esquerda/direita e um atributo de direita/esquerda. Use à esquerda/direita para ambos ou nenhum dos dois.'

iOS 9 impõe layouts para usar um **direita** & **esquerda** _ou_ **levando**  &   **À direita** atributos, mas *não* ambos. Para corrigir esse problema, altere todas as restrições de layout para usar o mesmo atributo definido dentro do arquivo do Storyboard.

Para obter mais informações, consulte o [erro de restrição do iOS 9](https://stackoverflow.com/questions/32692841/ios-9-constraint-error) discussão de Stack Overflow.

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>ERROR ITMS-90535: Chave de CFBundleExecutable inesperado

Depois de alternar para o iOS 9, de um aplicativo usa componentes de terceiros 3ª (especificamente nosso componente Google Maps existente) que foi compilado e executado no iOS 8 (ou anterior), ao tentar enviar a nova compilação para o iTunes Connect, você pode receber um erro no formulário:

> ERROR ITMS-90535: Chave de CFBundleExecutable inesperado. O pacote no 'Payload/app-name.app/component.bundle' não contém um executável do pacote...

Esse problemas podem geralmente ser resolvido, localizando o pacote nomeado no projeto e em seguida, - editado assim como a mensagem de erro sugere - a `Info.plist` que está no grupo, removendo o `CFBundleExecutable` chave. O `CFBundlePackageType` chave deve ser definida como `BNDL` também.

Depois de fazer essas alterações, fazer uma limpeza e recompile o projeto inteiro. Você deve ser capaz de enviar para o iTunes Connect sem problemas depois de fazer essas alterações.

Para obter mais informações, consulte este [Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key) discussão.

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>Falha de CFNetwork SSLHandshake erro (-9824)

Ao tentar se conectar à internet, seja diretamente ou de uma exibição da web no iOS 9, você poderá receber um erro no formulário:

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

Ou, no formato:

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

IOS9, segurança de transporte de aplicativo (ATS) impõe a conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e seu aplicativo. Além disso, o ATS requer comunicação usando o `HTTPS` protocolo e a comunicação de alto nível API a ser criptografado usando TLS versão 1.2 com sigilo.

Uma vez que o ATS é habilitado por padrão nos aplicativos criados para o iOS 9 e OS X 10.11 (El Capitan), todas as conexões usando `NSURLConnection`, `CFURL` ou `NSURLSession` estará sujeito aos requisitos de segurança ATS. Se suas conexões não atenderem a esses requisitos, ele falhará com uma exceção.

Consulte a [Opting-Out de ATS](~/ios/app-fundamentals/ats.md) seção do nosso [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) guia para obter informações sobre como resolver esse problema.

## <a name="my-existing-apps-dont-run-on-ios-9"></a>Meus aplicativos existentes não são executados no iOS 9

Consulte nosso [informações de compatibilidade do iOS 9](~/ios/platform/introduction-to-ios9/ios9.md) para obter instruções sobre como recompilar e reimplantar os aplicativos existentes ser executado no iOS 9.

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView é Null em construtores

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

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView Falha ao Init com o codificador ao carregar um modo de exibição de um Xib/Nib

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

## <a name="dyld-message-no-cache-image-with-name"></a>Mensagem Dyld: Nenhuma imagem de Cache com o nome...

Você pode enfrentar uma falha com as seguintes informações no log:

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Motivo:** Este é um bug no vinculador nativo da Apple, que acontece quando eles fizerem uma estrutura privada pública (JavaScriptCore se tornou público no iOS 7, antes que ele era uma estrutura privada), e o destino da implantação do aplicativo é para uma versão iOS quando o framework era privado. Nesse caso, vinculador da Apple será vinculada à versão particular do framework em vez da versão pública.

**Correção:** Isso será abordado para iOS 9, mas não há uma solução fácil aplicar por conta própria nesse: apenas uma versão iOS posterior em seu projeto (você pode tentar iOS 7 neste caso) de destino. Outras estruturas podem apresentar problemas semelhantes, por exemplo o framework do WebKit se tornou público no iOS 8 (e para direcionamento ao iOS 7 irá resultar nesse erro; você deve ter como destino o iOS 8 para usar WebKit em seu aplicativo).

## <a name="untrusted-enterprise-developer"></a>Desenvolvedor corporativo não confiável

Ao tentar executar a versão do iOS 9 do seu aplicativo xamarin. IOS em hardware de iOS reais, você poderá receber uma mensagem informando que sua conta de desenvolvedor não tem sido confiável no dispositivo. Por exemplo:

[![](troubleshooting-images/untrusted01.png "Alerta de desenvolvedor corporativo não confiável")](troubleshooting-images/untrusted01.png#lightbox)

Para resolver esse problema, faça o seguinte:

1. Inicie o Xcode (a versão beta mais recente) sobre o desenvolvimento do Mac.
2. Selecione **dispositivos** da **janela** menu para abrir a janela de dispositivos: 

    [![](troubleshooting-images/untrusted02.png "A janela de dispositivos")](troubleshooting-images/untrusted02.png#lightbox)
3. Sob o **dispositivos** do lado do painel, selecione seu dispositivo, o botão direito do mouse e selecione **Mostrar perfis de provisionamento...** : 

    [![](troubleshooting-images/untrusted03.png "Perfis de provisionamento SShow")](troubleshooting-images/untrusted03.png#lightbox)
4. Selecione cada perfil de provisionamento no momento no dispositivo e clique no **-** botão para excluí-lo: 

    [![](troubleshooting-images/untrusted04.png "Excluir um perfil de provisionamento")](troubleshooting-images/untrusted04.png#lightbox)
5. Dos **Xcode** menu, selecione **preferências...**  e **contas**: 

    [![](troubleshooting-images/untrusted05.png "Preferências de conta do Xcode")](troubleshooting-images/untrusted05.png#lightbox)
6. Clique o **exibir detalhes...**  e clique em de **baixar todos os** botão: 

    [![](troubleshooting-images/untrusted06.png "Baixar todos os perfis")](troubleshooting-images/untrusted06.png#lightbox)
7. Quando a lista concluiu a atualização, clique no **feito** botão e fechar a janela de preferências.
8. Remova a versão existente do aplicativo xamarin. IOS que você está tentando testar do dispositivo iOS.
9. Retorne ao Visual Studio para Mac, siga uma compilação limpa e tente executar novamente o aplicativo no dispositivo.

Você terá que parar e reiniciar o Visual Studio para Mac antes de novos perfis de provisionamento carregados pelo Xcode são vistos. Você talvez precise ajustar as **assinatura do pacote iOS** opções para seu aplicativo xamarin. IOS selecionar os novos perfis de provisionamento.

## <a name="launch-screen-issues"></a>Inicie os problemas de tela

Agora, o iOS 9 impõe os requisitos de tela de inicialização para que a mesma imagem de inicialização não pode ser reutilizada para dar suporte a orientações de interface diferente. Consulte da Apple [UILanchImage referência](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28) para obter mais informações.

Opcionalmente, você pode usar um arquivo de storyboard para apresentar a tela de inicialização do seu aplicativo em vez de usar um conjunto de **PNG** arquivos de imagem. Isso é agora Apple preferencial apresentar as telas de inicialização. Consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guia para obter mais informações.

Por fim, seu aplicativo deve usar um arquivo de storyboard para sua tela de inicialização e dar suporte a todas as orientações de interface de quatro (retrato, retrato de cabeça para baixo, esquerda paisagem e paisagem direita) para ser considerado para em execução em um Slide sobre painel ou no modo de exibição de divisão. Para obter mais informações sobre as novas capacidades de multitarefa do iOS 9, consulte nosso [multitarefa para iPad](~/ios/platform/multitasking.md) guia.

## <a name="nsinternalinconsistencyexception-exception"></a>Exceção NSInternalInconsistencyException

Ao compilar e executar um aplicativo existente do xamarin. IOS para iOS 9, você poderá receber um erro no formulário:

> Exceção Objective-C é gerada.  Nome: NSInternalInconsistencyException motivo: Janelas de aplicativos devem ter um controlador de exibição de raiz no final do aplicativo Iniciar

Isso é o erro está sendo gerado porque o aplicativo Windows devem ter um controlador de exibição de raiz no final da inicialização do aplicativo e não de seu aplicativo existente.

Há pelo menos duas soluções possíveis para esse problema:

1. Atualizar o aplicativo para usar o arquivo de storyboard, em vez de `xib` arquivos para definir sua Interface do usuário. Este requer muito tempo, dependendo do tamanho do seu aplicativo e o conhecimento de como usar o iOS Designer (ou Interface Builder do Xcode) aos storyboards de layout. Para obter mais informações, consulte nosso [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) documentação.
2. Programa de instalação `RootViewController` propriedade da janela do aplicativo no `FinishedLaunching` método na `AppDelegate` classe para apontar para um controlador de exibição na interface de usuário do seu aplicativo.

## <a name="when-to-initialize-views-and-view-controllers"></a>Quando inicializar exibições e controladores de exibição

É possível fazer a inicialização do modo de exibição ou controlador de exibição dentro dos construtores que são chamados quando algo é exposto em código gerenciado, mas ele interrompe o design do iOS com xamarin. IOS.

Em geral você deve inicializar tudo o que pode retornar a chamada código Objective-C do construtor, pois você não pode ter certeza de quando ele será chamado. Isso também significa que há um melhores locais (outras ctor) ou chamadas para substituir (como Objective-C não tiver eventos) em que essa inicialização deve ser feita.

## <a name="related-links"></a>Links relacionados

- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Atualizar seus aplicativos xamarin. IOS para iOS9 (vídeo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
