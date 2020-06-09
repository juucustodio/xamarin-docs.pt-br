---
title: Dicas de solução de problemas para o Xamarin. iOS
description: Este documento fornece várias dicas úteis para solução de problemas durante o desenvolvimento de aplicativos Xamarin. iOS. Ele descreve mensagens de erro específicas, bem como outros problemas potenciais.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 6368c3a4b128c06687b23b965b308ad6a788188b
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574481"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Dicas de solução de problemas para o Xamarin. iOS

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>O Xamarin. iOS não pode resolver System. ValueTuple

Esse erro ocorre devido a uma incompatibilidade com o Visual Studio.

- O **Visual Studio 2017 atualização 1** (versão 15,1 ou mais antiga) só é compatível com **System. ValueTuple NuGet 4.3.0** (ou mais antigo).

- O **Visual Studio 2017 atualização 2** (versão 15,2 ou mais recente) só é compatível com o **System. ValueTuple NuGet 4.3.1** ou mais recente.

Escolha o NuGet System. ValueTuple correto que corresponde à sua instalação do Visual Studio 2017.

## <a name="receiving-error-retrieving-update-information-error-message"></a>Recebendo a mensagem de erro ' erro ao recuperar informações de atualização '

Ao tentar atualizar o software e essa mensagem de erro aparecer, tente reiniciar o IDE e fazer logoff e, em seguida, voltar para sua conta no IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Como fazer criar imtomadas ou ações com Interface Builder?

Com a introdução do Xamarin Designer para iOS no Visual Studio para Mac e no Visual Studio, os desenvolvedores do Xamarin. iOS agora podem aproveitar a criação de uma interface do usuário por meio de storyboards e. xibs. Consulte os guias [Olá, Ios](~/ios/get-started/hello-ios/index.md) para obter mais informações sobre como usar o designer.

Você também pode consultar os guias de [tomada](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) e [ações](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) da Apple para obter mais informações sobre como usar saídas e ações no IB.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System. Text. Encoding. GetEncoding gera NotSupportedException

Você pode estar usando uma codificação que não é adicionada por padrão. Verifique a página de [internacionalização](~/ios/app-fundamentals/localization/index.md) para saber como adicionar suporte para mais codificação.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (qualquer outra coisa)

O membro provavelmente foi removido pelo vinculador e, portanto, não existe no assembly em tempo de execução.  Há várias soluções para isso:

- Adicione o [`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) atributo ao membro.  Isso impedirá que o vinculador o remova.
- Ao invocar [**mTouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29), use as opções **-nolink** ou **-linksdkonly** :
  - **-nolink** desabilita toda a vinculação.
  - **-linksdkonly** vinculará somente os assemblies Xamarin. Ios fornecidos, como **Xamarin. Ios. dll**, enquanto preserva todos os tipos em assemblies criados pelo usuário (ou seja, seus projetos de aplicativo).

Observe que os assemblies são vinculados para que o executável resultante seja menor; Portanto, desabilitar a vinculação pode resultar em um executável maior do que o desejado.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Você está recebendo um ModelNotImplementedException

Se você estiver recebendo essa exceção, isso significa que você está chamando base. Método () em uma classe que substitui um modelo. Você não precisa chamar o método base em uma classe para modelos (essas são classes que são sinalizadas com o atributo [Model]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Essa classe não é compatível com codificação de valor de chave para a chave XXXX

Se você receber esse erro ao carregar um arquivo NIB, isso significa que o valor XXXX não foi encontrado em sua classe gerenciada. Isso significa que você não tem uma declaração como esta:

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

A definição acima é gerada automaticamente pelo Visual Studio para Mac para quaisquer arquivos XIB que você adicionar ao Visual Studio para Mac no `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` arquivo.

Além disso, os tipos que contêm o código acima devem ser uma subclasse de [NSObject](xref:Foundation.NSObject).  Se o tipo recipiente estiver dentro de um namespace, ele também deverá ter um atributo [[Register]](xref:Foundation.RegisterAttribute) que fornece um nome de tipo sem um namespace (já que Interface Builder não dá suporte a namespaces em tipos):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe XXXX desconhecida no arquivo Interface Builder

Esse erro será gerado se você definir uma classe em seus arquivos do construtor de interface, mas não fornecer a implementação real para ele em seu código C#.

Você precisa adicionar um código como este:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System. MissingMethodException: nenhum construtor encontrado para foo. bar:: ctor (System. IntPtr)

Esse erro é produzido em tempo de execução quando o código tenta instanciar uma instância das classes que você referenciou de seu arquivo de Interface Builder. Isso significa que você esqueceu de adicionar um construtor que usa um único IntPtr como parâmetro.

O construtor com um identificador IntPtr é usado para associar objetos gerenciados a suas representações não gerenciadas.

Para corrigir isso, adicione a seguinte linha de código à classe foo. bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>O tipo {foo} não contém uma definição para `GetNativeField` e nenhum método `GetNativeField` de extensão do tipo {foo} foi encontrado

Se você receber esse erro nos arquivos gerados pelo designer (*. xib.designer.cs), isso significa que uma das duas coisas:

 **1) classe parcial ou classe base ausente**

As classes parciais geradas pelo designer devem ter classes parciais correspondentes no código do usuário que herdam de alguma subclasse de `NSObject` , muitas vezes `UIViewController` . Verifique se você tem essa classe para o tipo que está dando o erro.

 **2) namespaces padrão alterados**

Os arquivos de designer são gerados usando as configurações de namespace padrão do seu projeto. Se você alterou essas configurações ou renomeou o projeto, as classes parciais geradas podem não estar mais no mesmo namespace que suas contrapartes de código do usuário.

As configurações de namespace podem ser encontradas na caixa de diálogo opções do projeto. O namespace padrão é encontrado na seção **geral->configurações principais** . Se estiver em branco, o nome do seu projeto será usado como o padrão. As configurações de namespace mais avançadas podem ser encontradas na seção **código-fonte-> .net Naming Policies** .

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Aviso para ações: o método particular ' foo ' nunca é usado. (CS0169)

As ações para os arquivos do Interface Builder são conectadas aos widgets por reflexão em tempo de execução, portanto, esse aviso é esperado.

Você pode usar "#pragma Aviso Disable 0169" "#pragma Aviso Enable 0169" em suas ações se desejar suprimir esse aviso apenas para esses métodos, ou adicionar 0169 ao campo "ignorar avisos" nas opções do compilador se desejar desabilitá-lo para todo o projeto (não recomendado).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mTouch falhou com a seguinte mensagem: não é possível abrir o assembly '/path/to/yourproject.exe '

Se você vir essa mensagem de erro, geralmente o problema é que o caminho absoluto para seu projeto contém um espaço. Isso será corrigido em uma versão futura do Xamarin. iOS, mas você poderá contornar o problema movendo o projeto para uma pasta sem espaços.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Sua versão do sqlite3 é antiga. atualize para pelo menos v 3.5.0!

Isso acontece quando você faz todo o seguinte:

1. Usar mono. Data. sqlite
1. Usar Mac OS X leopardo (10,5)
1. Execute seu aplicativo no simulador.

O problema é que o mono está selecionando o OS X `libsqlite3.dylib` , não o arquivo do iPhoneSimulator `libsqlite3.dylib` . Seu aplicativo *will* funcionará no dispositivo, mas não no simulador.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>A implantação no dispositivo falha com System. Exception: AMDeviceInstallApplication retornou 3892346901

Esse erro significa que a configuração de assinatura de código para seu certificado/ID de pacote não corresponde ao perfil de provisionamento instalado em seu dispositivo.  Confirme se você tem o certificado apropriado selecionado em opções do projeto->assinatura de pacote do iPhone e a ID de pacote correta especificada em opções do projeto->aplicativo do iPhone

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>A conclusão do código não está funcionando no Visual Studio para Mac

Verifique se você está usando a versão mais recente do Visual Studio para Mac e Xamarin. iOS

Se o problema ainda estiver presente, registre [um bug](https://monodevelop.com/Developers#Reporting_Bugs), anexando os arquivos de log **~/library/logs/XamarinStudio-{Version}/IDE-{timestamp}.log**, **AndroidTools-{timestamp}. log**e **Components-{timestamp}. log** .

Se todo o resto falhar, você poderá tentar remover o cache de conclusão de código para que ele seja regenerado:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Tenha cuidado ao digitar esse comando corretamente ou remova acidentalmente arquivos importantes.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio para Mac falha quando você copia o texto

Os populares utilitários de Mac QuickSilver, barra de ferramentas do Google e barra inicial têm recursos de área de transferência que corromperam a memória Visual Studio para Mac. Em suas opções, você pode listar Visual Studio para Mac como um processo que eles não devem interferir.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio para Mac reclamações sobre o mono 2,4 necessário

Se você atualizou Visual Studio para Mac devido a uma atualização recente e, ao tentar iniciá-lo novamente, o mono 2,4 não está presente, tudo o que você precisa fazer é [atualizar sua instalação do mono 2,4](http://www.go-mono.com/mono-downloads/download.html).  

O mono 2.4.2.3 _6 corrige alguns problemas importantes que impediam Visual Studio para Mac de serem executados de forma confiável, às vezes suspensos Visual Studio para Mac na inicialização ou impediram a geração do banco de dados de conclusão de código.

Depois de instalar o novo mono, Visual Studio para Mac será iniciado conforme o esperado.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Asserção em.. /.. /.. /.. /mono/Metadata/Generic-Sharing.c: 704, condição ' Oti ' não atendida

Se você estiver recebendo o rastreamento de pilha a seguir:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Isso significa que você está vinculando uma biblioteca estática compilada com código Thumb em seu projeto. A partir da versão 3,1 do SDK do iPhone (ou mais recente no momento da elaboração deste artigo), a Apple introduziu um bug em seu vinculador ao vincular código não-Thumb (Xamarin. iOS) com código de Thumb (sua biblioteca estática). Você precisará vincular com uma versão não-Thumb da sua biblioteca estática para atenuar esse problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>System. ExecutionEngineException: tentando o método de compilação JIT (wrapper gerenciado para gerenciado) foo []: System. Collections. Generic. ICollection ' 1. get_Count ()

O sufixo [] indica que você ou a biblioteca de classes está chamando um método em uma matriz por meio de uma coleção genérica, como IEnumerable<>, ICollection<> ou IList<>. Como alternativa, você pode forçar explicitamente o compilador de AOT a incluir esse método chamando o método por conta própria e certificando-se de que esse código seja executado antes da chamada que disparou a exceção. Nesse caso, você poderia escrever:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Que forçará o compilador AOT a incluir o método get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio para Mac Editor de origem é extremamente lento

Às vezes, o editor de origem Visual Studio para Mac se torna extremamente lento, parecendo parar por vários segundos entre caracteres de digitação.

Esse problema é muito raro e extremamente difícil de reproduzir-ele geralmente não pode ser reproduzido no mesmo computador após a reinicialização do Visual Studio para Mac. Por esse motivo, gostaríamos que você pudesse executar várias etapas de depuração antes de reiniciar Visual Studio para Mac e enviar os resultados para nós.

1. Tente fechar a guia Editor e abri-la novamente. Demora um pouco para editar ou mover o cursor até que a lentidão ocorra novamente?
1. Desabilite "transmitir sincronização" usando a ferramenta de desenvolvedor "depuração do Quartz" (que você pode encontrar usando o Spotlight) e verifique se o desempenho do editor de origem é restaurado para normal.
1. Tente repetir a etapa (1) com a sincronização de transmissão ainda desabilitada.
1. Se o editor parar por mais de alguns segundos, tente executar "killall-QUIT [Visual Studio para Mac]" em um terminal enquanto ele estiver suspenso. Pode ser difícil tempo que o comando Kill acontece enquanto o editor está suspenso, mas é essencial fazê-lo, pois o comando força o mono a gravar rastreamentos de pilha de todos os threads para o log MD, que podemos usar para descobrir em que estado os threads estão enquanto XS está suspenso.

Anexe os logs XS, **~/library/logs/XamarinStudio-{Version}/IDE-{timestamp}.log**, **ANDROIDTOOLS-{timestamp}. log**e **Components-{timestamp}. log** (em versões mais antigas de xs/MonoDevelop, basta enviar **~/library/logs/MonoDevelop-(3.0 | 2.8 | 2.6)/MonoDevelop.log**).

> [!NOTE]
> O problema acima foi corrigido em XS 2,2 final * *

## <a name="compiled-application-is-very-large"></a>O aplicativo compilado é muito grande

Para dar suporte à depuração, as compilações de depuração contêm código adicional. Os projetos criados no modo de versão são uma fração do tamanho.

A partir do Xamarin. iOS 1,3, as compilações de depuração incluíram suporte à depuração para cada componente único do mono (todos os métodos em todas as classes das estruturas).  

Com o Xamarin. iOS 1,4, apresentaremos um método mais refinado para depuração, o padrão será fornecer apenas Instrumentação de depuração para seu código e suas bibliotecas, e não fazer isso para todos os [assemblies do mono](~/cross-platform/internals/available-assemblies.md) (isso ainda será possível, mas você precisará optar pela depuração desses assemblies).

## <a name="installation-hangs"></a>Travamentos de instalação

Os instaladores mono e Xamarin. iOS travarão se você tiver o simulador do iPhone em execução. Esse problema não se limita a mono ou Xamarin. iOS, esse é um problema consistente em qualquer software que tente instalar o software no MacOS neve leopardo se o simulador do iPhone estiver sendo executado no momento da instalação.

Feche o simulador do iPhone e tente a instalação novamente.

<a name="trampolines"></a>

## <a name="ran-out-of-trampolines-of-type-0"></a>Ficou sem trampolines do tipo 0

Se você receber essa mensagem durante a execução do dispositivo, poderá criar mais tipo 0 trampolines (específico do tipo) modificando as opções de projeto seção "Build do iPhone".  Você deseja adicionar argumentos extras para os destinos de compilação do dispositivo:

 `-aot "ntrampolines=2048"`

O número padrão de trampolines é 1024. Tente aumentar esse número até que você tenha o suficiente para o seu aplicativo.

## <a name="ran-out-of-trampolines-of-type-1"></a>Ficou sem trampolines do tipo 1

Se você fizer uso intensivo de genéricos recursivos, poderá receber essa mensagem no dispositivo.  Você pode criar mais tipo 1 trampolines (tipo RGCTX) modificando a seção Opções de seu projeto "Build do iPhone".  Você deseja adicionar argumentos extras para os destinos de compilação do dispositivo:

 `-aot "nrgctx-trampolines=2048"`

O número padrão de trampolines é 1024. Tente aumentar esse número até que você tenha o suficiente para o uso de genéricos.

## <a name="ran-out-of-trampolines-of-type-2"></a>Ficou sem trampolines do tipo 2

Se você fizer interfaces pesadas de uso, poderá receber essa mensagem no dispositivo.
Você pode criar mais tipo 2 trampolines (conversão de IMT) modificando a seção Opções de seu projeto "Build do iPhone".  Você deseja adicionar argumentos extras para os destinos de compilação do dispositivo:

 `-aot "nimt-trampolines=512"`

O número padrão de trampolines de conversão IMT é 128. Tente aumentar esse número até que você tenha o suficiente para o uso de interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>O depurador não pode se conectar ao dispositivo

Ao iniciar a depuração de uma configuração de dispositivo, você verá que o depurador mostra uma caixa de diálogo indicando que está tentando se conectar ao aplicativo. Há vários motivos pelos quais o depurador pode não conseguir se conectar ao aplicativo, dependendo do modo que você está usando para se conectar (USB ou WiFi).

 **Se o dispositivo e o host do depurador estiverem em redes diferentes**, um firewall ou rede privada poderá estar impedindo que o aplicativo se conecte ao host do depurador no modo WiFi.

 **Visual Studio para Mac pode não conseguir consultar o IP correto do host**. No modo WiFi Visual Studio para Mac fornece ao aplicativo todos os IPs que ele pode encontrar do host e o aplicativo tenta ver se ele pode usar qualquer um deles para se conectar ao Visual Studio para Mac.

 **Outro dispositivo está conectado a uma porta USB no host.** Em alguns casos, outros dispositivos conectados às portas USB no host têm sido conhecidos de alguma forma que interferem na depuração no modo USB.

Se o modo WiFi ou USB não funcionar, você poderá experimentar facilmente o outro: em Visual Studio para Mac, abra as preferências, vá para a página de depurador preferências/depurador/iPhone e alterne a caixa de seleção "depurar dispositivos iOS por Wi-Fi em vez de USB".   Se nenhum funcionar, você poderá ver mais informações sobre a falha no console do dispositivo no modo detalhado (que é habilitado adicionando "-v-v-v" aos argumentos mTouch adicionais nas opções do projeto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Erro 134: mTouch falhou com a seguinte mensagem:

Esse erro pode ser gerado se você estiver tentando compilar com-nolink no estilo Xamarin. iOS 1,4 de versões. Você pode contornar esse erro especificando argumentos extras em sua configuração de projeto MonoDevelop.

Adicionar o argumento

 `-nosymbolstrip`

e o problema deve ser resolvido.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>A identidade de distribuição não é mostrada em Visual Studio para Mac opções de assinatura de projeto

Visual Studio para Mac 2,2 tem um bug que faz com que ele não detecte certificados de distribuição que contenham uma vírgula. Atualize para Visual Studio para Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Erro "AFCFileRefWrite retornado: 1" durante o carregamento

Ao carregar um aplicativo em seu dispositivo, você poderá receber um erro "AFCFileRefWrite retornado: 1". Isso pode acontecer se você tiver um arquivo de comprimento zero.

## <a name="error-mtouch-failed-with-no-output"></a>Erro "mTouch falhou sem saída"

A versão atual do Xamarin. iOS e Visual Studio para Mac falha quando o nome do projeto ou o diretório no qual a solução ou o projeto são armazenados contêm espaços.
Para corrigir isso:

- Verifique se o seu projeto ou o diretório onde ele está armazenado contém um espaço.
- No projeto "configurações principais", verifique se o nome do projeto não contém espaços.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Erro "o binário que você carregou era inválido. Uma versão beta de pré-lançamento do SDK foi usada para criar o aplicativo "

Esse erro geralmente é causado com um projeto que foi iniciado no desenvolvimento do iPad antes da liberação do Xamarin. iOS 2.0.0, provavelmente você tem algumas chaves em seu info. plist como:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Esse par de chaves deve ser removido, pois Visual Studio para Mac manipula-o para você automaticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Erro "uma versão beta de pré-lançamento do SDK foi usada para compilar o aplicativo"

(Contribuído pelo Ed Anuff)

Siga estas etapas:

- Altere a versão do SDK do iPhone Build para 3,2 ou iTunes Connect para rejeitá-lo no carregamento, pois ele está vendo um aplicativo compatível com iPad criado usando uma versão do SDK inferior a 3,2
- Crie um info. plist personalizado para o projeto e defina explicitamente MinimumOSVersion como 3,0 nele.   Isso substituirá o valor de MinimumOSVersion 3,2 definido pelo Xamarin. iOS.   Se você não fizer isso, o aplicativo não será capaz de executar em um iPhone.
- Recompile, zip e upload para o iTunes Connect.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Exceção sem tratamento: System. Exception: falha ao localizar o seletor someSelector: em {Type}

Essa exceção é causada por uma das três coisas:

1. Você forneceu um seletor para o tempo de execução Objective-C sem aplicar o atributo [Export] correspondente a um método
1. Você habilitou a vinculação completa e não aplicou o atributo [preserve] ao método [Export] Ed.
1. Você aplicou o atributo [Export] a um método particular em um tipo herdado.

## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>O arquivo MainWindow.xib.designer.cs não está atualizado

Houve um bug no Xamarin Studio 2,4 que fez com que ele não agrupasse o arquivo MainWindow. xib com o arquivo MainWindow. xib. designer em novos projetos. Isso significa que ele não atualizaria o código do designer para esse arquivo específico.

Esse problema é corrigido na versão do Visual Studio para Mac que está disponível em seu atualizador interno, portanto, certifique-se de usar a versão mais recente.

Você pode corrigir os projetos existentes removendo (não excluindo) o XIB e seu arquivo de designer e, em seguida, adicionando-o de volta. Isso deve reagrupar os arquivos corretamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView ou UIActionSheet desaparecer após ser criado

Se você tiver algum código como este:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

o objeto "actionSheet" reside como uma variável temporária na função e assim que a função é encerrada, o objeto é elegível para coleta de lixo e, portanto, acaba sendo coletado pelo lixo.

Para corrigir esse problema, você precisa manter uma referência a "actionSheet" fora do seu método, em algum lugar que ficará além do seu método.

## <a name="project-always-runs-in-the-ipad-simulator"></a>O projeto sempre é executado no simulador de iPad

O instalador do iPhone SDK 4,0 instala 2 SDKs – o SDK do 3,2, para compilar aplicativos somente para iPad e o SDK do 4,0, usado para criar aplicativos iPhone e universal. Ele também instala um simulador de 3,2, que simula apenas um iPad e um simulador de 4,0 que simula iPhone ou iPhone 4. Todos os SDKs e simuladores mais antigos são removidos.

Visual Studio para Mac opções de Build de projeto do iPhone incluem uma configuração para a versão do SDK que será usada na criação do seu aplicativo. Essa configuração pode ser encontrada em **Opções do projeto->Build->o iPhone Build**.

Novos projetos no Visual Studio para Mac usam o SDK instalado mais antigo como sua configuração padrão do SDK e, se o SDK especificado não existir, Visual Studio para Mac usará o mais próximo possível para criar seu aplicativo. Isso foi feito para que os projetos nem sempre exijam o SDK mais recente. No entanto, isso resulta atualmente no SDK 3,2 que está sendo usado-o que resulta no simulador de iPad em uso.

Para corrigir isso usando o SDK 4,0, acesse **Opções do projeto->Build->iPhone build**> e altere o valor do SDK para "4,0" usando a caixa suspensa. Você deve fazer isso para cada combinação de configuração e plataforma, acessada usando os menus suspensos na parte superior do painel.

A versão do SDK não deve ser confundida com a configuração "versão mínima do sistema operacional".
Esse valor não precisa corresponder ao valor da versão do SDK – ele afeta a versão mínima do sistema operacional em que seu aplicativo será instalado, o que pode ser mais antigo que o SDK, desde que você use apenas as APIs existentes no sistema operacional mais antigo ou proteja o uso de recursos mais recentes usando verificações de versão do sistema operacional de tempo de execução. Você deve defini-lo como a versão mais antiga do sistema operacional em que você testa seu aplicativo.

Observe também que o menu> de **destino do >iPhone Simulator** pode ser usado para escolher o simulador usado por padrão durante a execução/depuração de um projeto. Além disso, o menu **executar >executar com**> pode ser usado para escolher um simulador específico com o qual executar

## <a name="ibtool-returns-error-133"></a>ibtool retorna o erro 133

Isso significa que você tem o XCode 4 instalado.   No XCode 4, a ferramenta ibtool foi removida, não é mais possível editar seus arquivos XIB com uma ferramenta autônoma.

Se você quiser usar Interface Builder, instale o XCode Series 3, disponível no site da Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>"Não é possível criar a associação de exibição para o tipo MIME: application/vnd. Apple-Interface-Builder"

Esse erro ocorrerá se você tentar criar uma interface do usuário do iPhone a partir de um projeto que não seja do iPhone. Certifique-se de começar com uma solução iPhone/iPad, não é possível apenas adicionar elementos da interface do usuário do iPhone a um projeto que não seja iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Falha na inicialização ao executar dentro do simulador de iOS

Se você obtiver uma falha de tempo de execução (SIGSEGV) dentro do simulador junto com um rastreamento de pilha semelhante a este:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

... em seguida, você provavelmente tem um (ou mais) assembly obsoleto no diretório de aplicativo do simulador. Esses assemblies podem existir desde que o simulador do iOS da Apple adiciona e atualiza arquivos, mas nunca os exclui. Se isso acontecer, a solução mais fácil é selecionar "redefinir e conteúdo e configurações..." no menu simulador.   

> [!WARNING]
> Isso removerá todos os arquivos, aplicativos e dados do simulador.   Na próxima vez que você executar o aplicativo, Visual Studio para Mac o implantará no simulador e não haverá nenhum assembly antigo e obsoleto para causar a falha.

## <a name="simulator-hangs-during-application-installation"></a>O simulador trava durante a instalação do aplicativo

Isso pode acontecer quando os nomes de aplicativo incluem um '. ' (ponto) em seu nome.
Isso é proibido como o nome do executável em CFBundleExecutable, mesmo que ele possa funcionar em muitos outros casos (como dispositivos).

"O valor não deve incluir nenhuma extensão no nome."

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Erro: "não há suporte para o tipo de atributo personalizado 0x43" ao clicar duas vezes em arquivos. xib

Isso é causado pela tentativa de abrir arquivos. xib quando as variáveis de ambiente são definidas incorretamente. Isso não deve acontecer com o uso normal de Visual Studio para Mac/Xamarin. iOS e a reabertura de Visual Studio para Mac do/Applications deve corrigir o problema.

Ao tentar atualizar o software e essa mensagem de erro aparecer, envie um email para*support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>O aplicativo é executado no simulador, mas falha no dispositivo

Esse problema pode ser manifestado em várias formas e nem sempre produz um erro consistente. Se o aplicativo contiver um. xib, verifique se a **ação de compilação** em. xib está definida como **InterfaceDefinition**. Essa é a ação de compilação padrão para. xibs.

Para verificar a ação de Build, clique com o botão direito do mouse no arquivo. xib e escolha **criar ação**.

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: não há dados disponíveis para codificação 437

Ao incluir bibliotecas de terceiros em seu aplicativo Xamarin. iOS, você pode receber um erro no formato "System. NotSupportedException: nenhum dado está disponível para codificação 437" ao tentar compilar e executar o aplicativo. Por exemplo, bibliotecas, como `Ionic.Zip.ZipFile` , podem gerar essa exceção durante a operação.

Isso pode ser resolvido abrindo as opções para o projeto Xamarin. Ios, indo para a **iOS Build**  >  **internacionalização** de Build do IOS e verificando a internacionalização **oeste** .
