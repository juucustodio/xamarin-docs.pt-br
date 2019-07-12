---
title: Dicas de solução de problemas para xamarin. IOS
description: Este documento fornece várias dicas úteis para solução de problemas durante o desenvolvimento de aplicativos xamarin. IOS. Ele descreve mensagens de erro específicas, bem como outros problemas potenciais.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2018
ms.openlocfilehash: 38c0ece3e8f0361f3c891713e53b033351512f94
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829916"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Dicas de solução de problemas para xamarin. IOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin. IOS não é possível resolver a System. valuetuple

Esse erro ocorre devido a uma incompatibilidade com o Visual Studio.

- **Visual Studio 2017 atualização 1** (versão 15.1 ou anterior) só é compatível com **valuetuple NuGet 4.3.0** (ou anterior).

- **Visual Studio 2017 atualização 2** (versão 15.2 ou mais recente) só é compatível com o **valuetuple NuGet 4.3.1** ou mais recente.

Escolha o NuGet de System. valuetuple correta que corresponde à sua instalação do Visual Studio 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Recebendo a mensagem de erro 'Erro ao recuperar informações de atualização'

Quando a tentativa de atualizar o software e essa mensagem de erro aparece, tente reiniciar o seu IDE e fazer logoff e volta em sua conta no IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Como criar ações ou meios de comunicação com o Interface Builder?

Com a introdução do Designer de Xamarin para iOS no Visual Studio para Mac e Visual studio, os desenvolvedores do xamarin. IOS agora podem levar vantagem de criar uma interface do usuário por meio de .xibs e Storyboards. Consulte a [Hello, iOS](~/ios/get-started/hello-ios/index.md) guias para obter mais informações sobre como usar o designer.

Você também pode consultar da Apple [tomada](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) e [ações](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) guias para obter mais informações sobre como usar saídas e ações em IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding gerará a NotSupportedException

Você pode estar usando uma codificação que não é adicionado por padrão. Verifique as [internacionalização](~/ios/app-fundamentals/localization/index.md) página para saber como adicionar suporte para a codificação mais.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (tudo)

O membro provavelmente foi removido pelo vinculador e, portanto, não existe no assembly em tempo de execução.  Há várias soluções para isso:

- Adicione a [ `[Preserve]` ](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) ao membro de atributo.  Isso impedirá que o vinculador removê-lo.
- Ao invocar [ **mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), use o **- nolink** ou **- linksdkonly** opções:
  - **-nolink** desabilita todas as vinculações.
  - **-linksdkonly** apenas vincular assemblies fornecidos pelo xamarin. IOS, como **xamarin**, preservando todos os tipos em assemblies criados pelo usuário (ie. seus projetos de aplicativo).

Observe que os assemblies são vinculados para que o executável resultante é menor; Portanto, desabilitar a vinculação pode resultar em um executável maior do que é desejável.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Você está obtendo um ModelNotImplementedException

Se você estiver recebendo essa exceção, isso significa que você está chamando base. Método () em uma classe que substitui um modelo. Você não precisa chamar o método base em uma classe para modelos (essas são classes que são marcadas com o atributo [modelo]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Esta classe não é um valor de chave de codificação compatíveis com a chave XXXX

Se você receber esse erro ao carregar um arquivo NIB significa que o valor que xxxx não foi encontrado na classe gerenciada. Isso significa que você não tem uma declaração como esta:

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

A definição acima é gerada automaticamente pelo Visual Studio para Mac para todos os arquivos XIB que você adiciona ao Visual Studio para Mac no `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` arquivo.

Além disso, os tipos que contém o código acima devem ser uma subclasse de [NSObject](xref:Foundation.NSObject).  Se o tipo recipiente estiver dentro de um namespace, ela também deve ter uma [[registrar]](xref:Foundation.RegisterAttribute) atributo que fornece um nome de tipo sem um namespace (como o construtor de Interface não dá suporte a namespaces em tipos):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe desconhecido XXXX no arquivo do construtor de Interface

Esse erro é gerado se você define uma classe em seus arquivos do construtor de interface, mas você não fornecer a implementação real para ele no seu C# código.

Você precisará adicionar um código como este:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException: Nenhum construtor encontrado para Foo.Bar::ctor(System.IntPtr)

Esse erro é gerado em tempo de execução quando o código tentar instanciar uma instância das classes que são referenciados do seu arquivo de Interface Builder. Isso significa que você se esqueceu de adicionar um construtor que aceita um IntPtr único como um parâmetro.

O construtor com um identificador IntPtr é usado para associar objetos gerenciados por suas representações não gerenciadas.

Para corrigir isso, adicione a seguinte linha de código à classe Bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Tipo {Foo} não contém uma definição para `GetNativeField` e nenhum método de extensão `GetNativeField` do tipo {Foo} foi encontrado

Se você receber esse erro nos arquivos gerados designer (*. xib.designer.cs), isso significa que uma das duas coisas:

 **1) ausente classe parcial ou classe base**

As classes parciais gerado pelo designer devem ter correspondentes classes parciais no código do usuário que herdam de alguma subclasse de `NSObject`, muitas vezes `UIViewController`. Certifique-se de que você tenha essa classe para o tipo que está fornecendo o erro.

 **2) namespaces de padrão alterados**

Os arquivos de designer são gerados usando configurações de namespace padrão do seu projeto. Se você tiver alterado a essas configurações ou renomeado o projeto, as classes parciais geradas podem não estar no mesmo namespace que suas contrapartes do código do usuário.

Configurações de Namespace podem ser encontradas na caixa de diálogo Opções do projeto. O namespace padrão é encontrado na **geral -> configurações de Main** seção. Se ele estiver em branco, o nome do seu projeto é usado como o padrão. As configurações mais avançadas do namespace podem ser encontradas na **código-fonte -> políticas de nomenclatura .NET** seção.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Aviso para ações: O método particular 'Foo' nunca é usado. (CS0169)

Ações para arquivos do construtor de interface são conectadas os widgets por reflexão em tempo de execução, portanto, esse aviso é esperado.

Você pode usar o "aviso #pragma desabilitar 0169" "habilitar o aviso #pragma 0169" em torno de suas ações para suprimir este aviso apenas para esses métodos, ou adicionar 0169 ao campo de "Ignorar avisos" em Opções do compilador se você quiser desabilitá-lo para todo o projeto (não recomendado).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch falhou com a seguinte mensagem: Não é possível abrir o assembly ' / path/to/yourproject.exe'

Se você vir essa mensagem de erro, geralmente, o problema é que o caminho absoluto para seu projeto contém um espaço. Isso será corrigido em uma versão futura do xamarin. IOS, mas você pode contornar o problema, movendo o projeto para uma pasta sem espaços.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Sua versão do sqlite3 é antigo –, atualize para pelo menos v3.5.0!

Isso acontece quando você faz o seguinte:

1.  Usar Mono.Data.Sqlite
1.  Usar o Mac OS X Leopard (10.5)
1.  Execute seu aplicativo no simulador.


O problema é que o Mono é pegar o OS X `libsqlite3.dylib`, do não o iPhoneSimulator `libsqlite3.dylib` arquivo. Seu aplicativo *serão* funcionam no dispositivo, mas não o simulador.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Implante o dispositivo falhará com o System. Exception: AMDeviceInstallApplication retornado 3892346901

Esse erro significa que a configuração de assinatura de código para sua id do certificado/pacote não coincide com o perfil de provisionamento instalado em seu dispositivo.  Confirme se você tiver o certificado apropriado selecionado nas opções de projeto -> assinatura do pacote do iPhone e a id do pacote correto especificada nas opções de projeto -> aplicativo de iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Conclusão de código não está funcionando no Visual Studio para Mac

Certifique-se de que você estiver usando a versão mais recente do Visual Studio para Mac e xamarin. IOS

Se o problema ainda estiver presente, por favor [arquivar um bug](http://monodevelop.com/Developers#Reporting_Bugs), anexando o **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componentes-{TIMESTAMP}. log** arquivos de log.

Se todo o resto falhar, você pode tentar remover o cache de conclusão de código para que ele for gerado novamente:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Certifique-se de que você digitar esse comando corretamente ou acidentalmente, você pode remover arquivos importantes.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>O Visual Studio para Mac Falha ao copiar texto

Os utilitários populares do Mac QuickSilver, a barra de ferramentas do Google e a barra inicial têm recursos de área de transferência que corromper o Visual Studio para a memória do Mac. Em suas opções, você pode listar o Visual Studio para Mac como um processo que eles não devem interferir com.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>O Visual Studio para Mac reclama sobre 2.4 Mono necessária

Se você atualizou o Visual Studio para Mac devido a uma atualização recente e, quando você tentar iniciá-lo novamente ele reclama sobre 2.4 Mono não estarem presentes, tudo o que você precisa fazer é [atualizar sua instalação do Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

Mono 2.4.2.3_6 corrige alguns problemas importantes que impediram o Visual Studio para Mac em execução com confiança, travado, às vezes, o Visual Studio para Mac na inicialização ou impediram que o banco de dados de conclusão de código que está sendo gerado.

Depois de instalar o Mono novo, o Visual Studio para Mac será iniciado conforme o esperado.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Asserção no... /.. /.. /.. /Mono/Metadata/Generic-Sharing.c:704, a condição não atendida ' oti'

Se você estiver recebendo o rastreamento de pilha a seguir:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Isso significa que você está vinculando a uma biblioteca estática compilada com código thumb em seu projeto. A partir da versão SDK do iPhone 3.1 (ou superior no momento da redação deste artigo) Apple introduziu um bug em seu vinculador ao vincular o código de não-Thumb (xamarin. IOS) com o código Thumb (biblioteca estática). Você precisará vincular com uma versão não elevador da sua biblioteca estática para atenuar esse problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException: A tentativa de () de Foo[]:System.Collections.Generic.ICollection'1.get_Count de método (wrapper gerenciado para gerenciado) de compilação JIT

O sufixo de [] indica que você ou a biblioteca de classes está chamando um método em uma matriz por meio de uma coleção genérica, como IEnumerable <>, ICollection <> ou <> IList. Como alternativa, você pode forçar explicitamente o compilador AOT para incluir um método desse tipo chamando o método por conta própria e, certificando-se de que esse código é executado antes da chamada que disparou a exceção. Nesse caso, você poderia escrever:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Que forçará o compilador AOT para incluir o método get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>O Visual Studio para o editor de código-fonte do Mac é extremamente lento

Às vezes, o editor Visual Studio para Mac origem torna-se extremamente lento, que parece parar de responder por alguns segundos entre digitar caracteres.

Esse problema é muito raro e extremamente difíceis de reproduzir - ele geralmente não pode ser reproduzido na mesma máquina após reiniciar o Visual Studio para Mac. Por esse motivo Nós ficaríamos satisfeitos se você pode executar várias etapas de depuração antes de reiniciar o Visual Studio para Mac e nos enviar os resultados.

1.  Tente fechar a guia do editor e abri-lo novamente. Demora um pouco de edição ou mover o cursor até que a lentidão ocorre novamente?
1.  Desabilitar a "sincronização de feixe usando a ferramenta de desenvolvedor"Habilitado para Quartz Debug"(que pode ser encontrado usando o destaque) e verifique se o desempenho do editor de origem será restaurado ao normal.
1.  Tente repetir a etapa (1) com a sincronização de feixe ainda está desabilitado.
1.  Se o editor para de responder por mais de alguns segundos, tente executar "killall-sair [Visual Studio para Mac]" em um terminal enquanto ele está suspenso. Pode ser difícil para o comando kill acontecer enquanto o editor está suspenso, mas é essencial fazer isso, porque o comando força o Mono para gravar rastreamentos de pilha de todos os threads no log MD, que podemos usar para descobrir qual estado em que os threads estão em enquanto os XS está suspenso de tempo.



Anexe os logs de XS **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componentes-{TIMESTAMP}. log**(nas versões mais antigas do MonoDevelop/XS, basta enviar **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **OBSERVAÇÃO: Esse problema foi corrigido no Final do XS 2.2**

## <a name="compiled-application-is-very-large"></a>Aplicativo compilado é muito grande

Para dar suporte à depuração, compilações de depuração contêm um código adicional. Projetos criados no modo de versão são uma fração do tamanho.

A partir do xamarin. IOS 1.3 o debug compilações incluído suporte para todos os componentes do Mono (cada método em cada classe das estruturas) único de depuração.  

Com o xamarin. IOS 1.4 apresentaremos um método mais preciso para depuração, o padrão será fornecer apenas a instrumentação de depuração para seu código e suas bibliotecas e não fazer isso para todos os [assemblies Mono](~/cross-platform/internals/available-assemblies.md) (isso ainda será é possível, mas você terá que consentem em depuração desses assemblies).

## <a name="installation-hangs"></a>Paradas de instalação

Instaladores de Mono e xamarin. IOS de suspensão se você tiver o simulador do iPhone em execução. Esse problema não está limitado a Mono ou xamarin. IOS, esse é um problema consistente entre qualquer software que tenta instalar o software em MacOS neve Leopard se iPhone simulador estiver em execução no momento da instalação.

Verifique se você fechar o simulador do iPhone e repetir a instalação.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Ficou sem trampolines do tipo 0

Se você receber essa mensagem durante a execução de dispositivo, você pode criar o tipo mais trampolines 0 (tipo específico), modificando sua seção de "iPhone compilação" opções de projeto.  Você deseja adicionar destinos de compilação de argumentos extras para o dispositivo:

 `-aot "ntrampolines=2048"`

O número padrão de trampolines é 1024.  Tente aumentar esse número até que você tenha espaço suficiente para seu aplicativo.

## <a name="ran-out-of-trampolines-of-type-1"></a>Ficou sem trampolines do tipo 1

Se você fizer uso intenso de genéricos recursiva, você pode receber essa mensagem no dispositivo.  Você pode criar o tipo mais trampolines 1 (tipo RGCTX) modificando sua seção de "iPhone compilação" opções de projeto.  Você deseja adicionar destinos de compilação de argumentos extras para o dispositivo:

 `-aot "nrgctx-trampolines=2048"`

O número padrão de trampolines é 1024.  Tente aumentar esse número até que você tenha espaço suficiente para o uso de genéricos.

## <a name="ran-out-of-trampolines-of-type-2"></a>Ficou sem trampolines do tipo 2

Se você fizer uso pesado de interfaces, você pode receber essa mensagem no dispositivo.
Você pode criar o tipo mais 2 trampolines (IMT conversões de tipo), modificando sua seção de "iPhone compilação" opções de projeto.  Você deseja adicionar destinos de compilação de argumentos extras para o dispositivo:

 `-aot "nimt-trampolines=512"`

O número padrão de trampolines IMT Thunk é 128.  Tente aumentar esse número até que você tenha espaço suficiente para o uso de interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Depurador não conseguir se conectar com o dispositivo

Quando você inicia a depuração de uma configuração de dispositivo, você verá o depurador mostrar uma caixa de diálogo indicando que ele está tentando se conectar ao aplicativo. Há vários motivos, que o depurador não conseguir se conectar ao aplicativo, dependendo do modo que você está usando para se conectar (USB ou WiFi).

 **Se o dispositivo e o host do depurador estão em redes diferentes**, um firewall ou a rede privada pode estar impedindo que o aplicativo se conecte ao host do depurador no modo de Wi-Fi.

 **O Visual Studio para Mac pode não ser capaz de consultar o IP correto do host**. No Wi-Fi modo Visual Studio para Mac oferece ao aplicativo todos os IPs que ele possa encontrar do host e o aplicativo tenta todos eles para ver se ele pode usar qualquer um deles para se conectar ao Visual Studio para Mac.

 **Outro dispositivo está conectado a uma porta USB no host.** Em alguns casos, outros dispositivos conectados à porta USB portas no host são conhecidas por alguma forma interferir com a depuração no modo USB.

Se o modo de Wi-Fi ou USB não funcionar, você pode tentar o outro com facilidade: no Visual Studio para Mac, abra as preferências, vá para a página de depurador/preferências/iPhone depurador e alterne a caixa de seleção "Debug dispositivos iOS via WiFi, em vez de por USB".   Se não funcionar, você pode ver mais informações sobre a falha no console do dispositivo no modo detalhado (que é habilitado adicionando "-v - v - v" aos argumentos adicionais do mtouch nas opções do projeto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Erro 134: mtouch falha com a seguinte mensagem:

Esse erro pode ser gerado se você estiver tentando compilar com - nolink no estilo de versões 1.4 do xamarin. IOS. Você pode contornar esse erro especificando argumentos adicionais na sua configuração de projeto do monodevelop.

Adicione o argumento

 `-nosymbolstrip`

e o problema deve ser resolvido.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identidade de distribuição não é mostrada no Visual Studio para o projeto do Mac opções de assinatura

O Visual Studio para Mac 2.2 tem um bug que faz com que ele não detectar os certificados de distribuição que contêm uma vírgula. Atualize para o Visual Studio para Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Erro "AFCFileRefWrite retornado: 1" durante o carregamento

Durante o carregamento de um aplicativo em seu dispositivo, você poderá receber um erro "AFCFileRefWrite retornado: 1". Isso pode acontecer se você tiver um arquivo com comprimento zero.

## <a name="error-mtouch-failed-with-no-output"></a>Erro "mtouch falhou sem saída"

A versão atual do xamarin. IOS e o Visual Studio para Mac falham quando o nome do projeto ou o diretório em que a solução ou projeto são armazenados conter espaços.
Para corrigir isso:


-  Certifique-se de que nem o seu projeto ou o diretório onde ele é armazenado contiver um espaço.
-  Nas configurações do projeto"Main", certifique-se de que o nome do projeto não contém espaços.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Erro "o binário que você carregou era inválido. Uma versão de pré-lançamento beta do SDK foi usada para compilar o aplicativo"

Esse erro é geralmente causado com um projeto que foi iniciado no desenvolvimento de iPad antes do lançamento do xamarin. IOS 2.0.0, você provavelmente terá algumas chaves em seu Info. plist, como:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Esse par de chaves deve ser removido como o Visual Studio para Mac lida com isso para você automaticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Erro "uma versão de pré-lançamento beta do SDK foi usada para compilar o aplicativo"

(Contribuído por Ed Anuff)

Siga estas etapas:

-  Alterar a versão do SDK no iPhone Build 3.2 ou o iTunes connect irá rejeitá-la durante o upload porque está vendo um aplicativo para iPad compatível criado usando uma versão do SDK inferiores a 3.2
-  Criar um Info. plist personalizado para o projeto e defina explicitamente MinimumOSVersion 3.0 nele.   Isso substituirá o valor de MinimumOSVersion 3.2 definido pelo xamarin. IOS.   Se você não fizer isso, o aplicativo não poderá executar em um iPhone.
-  Recompilação, zip e upload para o iTunes conectam.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Exceção sem tratamento: System.Exception: Falha ao localizar o seletor someSelector: em {type}

Essa exceção é causada por uma das três coisas:


1.  Você forneceu um seletor para o tempo de execução do Objective-C sem aplicar o atributo [exportação] correspondente a um método
1.  Você habilita a vinculação completa e não aplicado o atributo [Preserve] para o método ed [exportação].
1.  Você aplicou o atributo [Export] para um método particular em um tipo herdado.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>Arquivo MainWindow.xib.designer.cs não é atualizado.

Havia um bug no Xamarin Studio 2.4 que fez com que ele não o arquivo MainWindow.xib com o arquivo MainWindow.xib.designer em novos projetos de grupo. Isso significava que não atualizará o código do designer para esse arquivo específico.

Esse problema foi corrigido na versão do Visual Studio para Mac está disponível no seu atualizador interno, portanto, verifique se você usar a versão mais recente.

Você pode corrigir os projetos existentes, removendo (não excluir) o xib e seu arquivo de designer, em seguida, adicioná-lo novamente. Isso deve reagrupar os arquivos corretamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView ou UIActionSheet desaparecer após a criação

Se você tiver um código como este:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

o objeto "actionSheet" existe como uma variável temporária na função e, assim que a função for encerrado, o objeto está qualificado para coleta de lixo, para que ele acaba sendo coletado como lixo.

Para corrigir esse problema, você precisa manter uma referência ao "actionSheet" fora de seu método, em algum lugar que permanecerá ativo além do seu método.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Projeto sempre é executado no simulador do iPad

O instalador do iPhone SDK 4.0 instala 2 SDKs - o SDK 3.2, para a criação de aplicativos de iPad e o SDK 4.0, usado para criar aplicativos universais e iPhone. Ele também instala um simulador 3.2, que simula a apenas um iPad, e um simulador 4.0 que simula o iPhone ou iPhone 4. Todos os SDKs e simuladores mais antigos são removidos.

O Visual Studio para opções de build do Mac iPhone projeto incluem uma configuração para a versão do SDK que será usada na criação de seu aplicativo. Essa configuração pode ser encontrada em **opções de projeto -> Build -> iPhone Build**.

Novos projetos no Visual Studio para Mac usam o SDK instalado mais antigo como sua configuração padrão do SDK, e se o SDK especificado não existir, o Visual Studio para Mac usa o mais próximo que ele pode encontrar a compilar seu aplicativo. Isso foi feito para que os projetos não iria sempre demandar o SDK mais recente. No entanto, isso é atualmente resulta no 3,2 SDK que está sendo usado - o que resulta no simulador iPad que está sendo usado.

Para corrigir isso, usando o SDK 4.0, vá para **opções de projeto -> Build -> iPhone Build**> e altere o valor do SDK para "4.0" usando a caixa de lista suspensa. Você deve fazer isso para cada combinação de plataforma, acessadas usando os menus suspensos na parte superior do painel e a configuração.

A versão do SDK não deve ser confundida com a configuração de "Versão do sistema operacional mínimo".
Esse valor não precisa corresponder ao valor de versão do SDK – ele afeta a versão mínima do sistema operacional de seu aplicativo será instalado, que pode ser mais antigo que o SDK, desde que você use apenas as APIs que existem no sistema operacional mais antigo ou se proteger o uso dos recursos mais novos usando Veri de versão do sistema operacional de tempo de execução ks. Você deve defini-lo para a versão mais antiga do sistema operacional em que você teste seu aplicativo.

Observe também que o **projeto de destino do simulador do iPhone ->** > menu pode ser usado para escolher o simulador que é usado por padrão durante a execução/depuração de um projeto. Além disso, o **Executar -> executar com**> menu pode ser usado para escolher um simulador específico com o qual deve ser executado

## <a name="ibtool-returns-error-133"></a>ibtool retorna erro 133

Isso significa que você tenha o XCode 4 está instalado.   No XCode 4, a ferramenta ibtool foi removido, ele não é mais possível editar os arquivos XIB com uma ferramenta autônoma.

Se você quiser usar o Interface Builder, instale [XCode série 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponível no site da Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"Não é possível criar associação de exibição para o tipo de mime: application/vnd.apple -<wbr/>interface builder"

Esse erro ocorre se você tentar criar um iPhone da interface do usuário de um projeto não iPhone. Certifique-se de que você iniciar com uma solução do iPhone/iPad, não é possível adicionar apenas os elementos de interface do usuário do iPhone para um projeto não iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Falha de inicialização durante a execução dentro do simulador de iOS

Se você receber uma falha de tempo de execução (SIGSEGV) dentro do simulador, juntamente com um rastreamento de pilha que tem esta aparência:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
... então você provavelmente terá uma (ou mais) assembly obsoleto no diretório do aplicativo simulador. Esses assemblies podem existir como Apple iOS simulator adiciona e atualiza os arquivos, mas nunca exclui-los. Se isso acontecer, em seguida, a solução mais fácil é selecionar "Redefinir e conteúdo e configurações..." no menu de simulador.   

> [!WARNING]
> Isso removerá todos os arquivos, aplicativos e dados do simulador.   Próxima vez que executar seu aplicativo, o Visual Studio para Mac implantá-lo no simulador e não haverá nenhum assembly antigo e obsoleto para causar a falha.

## <a name="simulator-hangs-during-application-installation"></a>Simulador congela durante a instalação do aplicativo

Isso pode acontecer quando os nomes de aplicativo incluem um '.' (ponto) em seu nome.
Isso é proibido como o nome do executável no CFBundleExecutable - mesmo que ele pode funciona em muitos outros casos (como dispositivos).

 \* "O valor não deve incluir qualquer extensão no nome." - [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Erro: "Não há suporte para o tipo de atributo personalizado 0x43" quando arquivos. XIB de clicar duas vezes

Isso é causado pela tentativa de abrir arquivos. XIB quando as variáveis de ambiente estão configuradas incorretamente. Isso não deve ocorrer com o uso normal do Visual Studio para Mac/Xamarin.iOS e abrir novamente o Visual Studio para Mac na/aplicativos deve corrigir o problema.

Ao tentar atualizar o software e essa mensagem de erro for exibida, por favor, email *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Aplicativo é executado no simulador, mas falhar no dispositivo

Esse problema pode se manifestar de várias formas e sempre não produz um erro consistente. Se o aplicativo contém um. XIB, verifique se o **ação de compilação** na. XIB é definido como **InterfaceDefinition**. Isso é a ação de compilação padrão para .xibs.

Para verificar a ação de compilação, clique com o botão direito no arquivo. XIB e escolha **ação de compilação**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: Não há dados disponíveis para a codificação 437

Ao incluir 3ª bibliotecas de terceiros em seu aplicativo xamarin. IOS, você poderá receber um erro no formato "System. NotSupportedException: Não há dados disponíveis para codificação 437" ao tentar compilar e executar o aplicativo. Por exemplo, bibliotecas, como `Ionic.Zip.ZipFile`, pode gerar essa exceção durante a operação.

Isso pode ser resolvido, abrindo as opções para o projeto xamarin. IOS, vai **Build do iOS** > **internacionalização** e verificando o **Oeste** Internacionalização.
