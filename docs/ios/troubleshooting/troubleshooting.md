---
title: Solução de problemas
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/0201
ms.openlocfilehash: 6a179c1d63e9b5a7b8a42705d5c112a7b71a4906
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>Solução de problemas

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin não é possível resolver System.ValueTuple

Esse erro ocorre devido a uma incompatibilidade com o Visual Studio.

- **Atualização 1 do Visual Studio 2017** (versão mais antiga ou 15.1) é compatível apenas com **System.ValueTuple NuGet 4.3.0** (ou anterior).

- **Atualização 2 do Visual Studio 2017** (versão 15.2 ou mais recente) só é compatível com o **System.ValueTuple NuGet 4.3.1** ou mais recente.

Escolha o System.ValueTuple NuGet correta que corresponde à sua instalação do Visual Studio de 2017.


## <a name="receiving-error-retrieving-update-information-error-message"></a>Recebendo a mensagem de erro 'Erro ao recuperar informações de atualização'

Quando a tentativa de atualizar o software e essa mensagem de erro aparece, tente reiniciar seu IDE e logoff e depois em como sua conta no IDE.

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>Como criar saídas ou ações com o construtor de Interface?

Com a introdução do Xamarin Designer para iOS no Visual Studio para Mac e o Visual studio, os desenvolvedores do xamarin agora podem tirar vantagem de criar uma interface do usuário por meio de Storyboards e .xibs. Consulte o [Hello, iOS](~/ios/get-started/hello-ios/index.md) guias para obter mais informações sobre como usar o designer.

Você também pode consultar da Apple [tomada](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) e [ações](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) guias para obter mais informações sobre como usar IB tomadas e ações.

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding lança NotSupportedException

Talvez você esteja usando uma codificação que não é adicionada por padrão. Verifique o [internacionalização](~/ios/app-fundamentals/localization/index.md) para aprender a adicionar suporte para codificação mais.

## <a name="systemmissingmethodexception-anything-else"></a>System. MissingMethodException (tudo)

O membro provavelmente foi removido pelo vinculador e, portanto, não existe no assembly em tempo de execução.  Há várias soluções para isso:

-  Adicionar o [[preservar]](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) para o membro de atributo.  Isso impedirá que o vinculador removê-lo.
-  Ao invocar [mtouch](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29) , use o **- nolink** ou **- linksdkonly** opções. -    **-nolink** desabilita vinculação todos.
-    **-linksdkonly** só vincular assemblies fornecido xamarin, como *monotouch.dll* ou xamarin.ios.dll.

Observe que os assemblies são vinculados para que o executável resultante será menor; Assim, desabilitando o vínculo pode resultar em um executável maior do que é desejável.

## <a name="you-are-getting-a-modelnotimplementedexception"></a>Você está obtendo um ModelNotImplementedException

Se você estiver recebendo essa exceção, isso significa que você está chamando base. Método () em uma classe que substitui um modelo. Você não precisa chamar o método base em uma classe para modelos (são as classes que são marcados com o atributo [modelo]).

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>Esta classe não é um valor de chave de codificação compatíveis com a chave XXXX

Se você receber esse erro ao carregar um arquivo NIB significa que o valor que xxxx não foi encontrado em sua classe gerenciada. Isso significa que você não tem uma declaração assim:

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

A definição acima é gerada automaticamente pelo Visual Studio para Mac para todos os arquivos XIB que você adicionar ao Visual Studio para Mac no `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` arquivo.

Além disso, os tipos que contém o código acima devem ser uma subclasse de [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).  Se for do tipo recipiente dentro de um namespace, ele também deve ter uma [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo que fornece um nome de tipo sem um namespace (como o construtor de Interface não oferece suporte a namespaces em tipos):

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Classe desconhecida XXXX no arquivo de construtor da Interface

Esse erro é gerado se você definir uma classe em seus arquivos do construtor de interface, mas você não fornecer a implementação real para ele em seu código c#.

Você precisa adicionar um código como este:

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System. MissingMethodException: Nenhum construtor encontrado para Foo.Bar::ctor(System.IntPtr)

Esse erro é gerado em tempo de execução quando o código tenta criar uma instância das classes que é referenciado em seu arquivo de construtor da Interface. Isso significa que você esqueceu de adicionar um construtor que aceita um único IntPtr como parâmetro.

O construtor com um identificador de IntPtr é usado para associar objetos gerenciados com suas representações de não gerenciados.

Para corrigir isso, adicione a seguinte linha de código à classe Foo.Bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Tipo {Foo} não contém uma definição para `GetNativeField' and no extension method `GetNativeField' do tipo {Foo} foi encontrado

Se você receber esse erro nos arquivos gerados designers (*. xib.designer.cs), isso significa que uma das duas coisas:

 **1) não classe parcial ou classe base**

Classes parciais gerado pelo designer devem ter correspondentes classes parciais no código do usuário herdam a alguma subclasse de `NSObject`, muitas vezes `UIViewController`. Certifique-se de que você tenha tal classe para o tipo que está fornecendo o erro.

 **2) namespaces de padrão alterado**

Os arquivos de designer são gerados usando configurações de namespace padrão do seu projeto. Se você tiver alterado essas configurações, ou renomear o projeto, as classes parciais geradas podem não estar mais no mesmo namespace que as contrapartes do código do usuário.

Configurações de Namespace podem ser encontradas na caixa de diálogo Opções de projeto. O namespace padrão é encontrado na **geral -> configurações do principal** seção. Se ele estiver em branco, o nome do projeto é usado como padrão. Configurações de namespace mais avançadas podem ser encontradas no **código-fonte -> políticas de nomeação de .NET** seção.

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>Para ações de aviso: O método particular 'Foo' nunca é usado. (CS0169)

Ações para arquivos de construtor da interface estão conectadas aos widgets por reflexão em tempo de execução, esse aviso é esperado.

Você pode usar "aviso #pragma desabilitar 0169" "aviso #pragma habilitar 0169" em torno de suas ações para suprimir este aviso apenas para esses métodos, ou adicionar 0169 para o campo "Ignorar avisos" nas opções do compilador se quiser desabilitá-lo para o projeto inteiro (não recomendado).

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch falhou com a seguinte mensagem: não é possível abrir o assembly ' / path/to/yourproject.exe'

Se você vir essa mensagem de erro, geralmente o problema é que o caminho absoluto para o seu projeto contém um espaço. Isso será corrigido em uma futura versão do xamarin, mas você pode contornar o problema, movendo o projeto para uma pasta sem espaços.

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>A versão de sqlite3 é antiga -, atualize para pelo menos v3.5.0!

Isso acontece quando você faz o seguinte:

1.  Use Mono.Data.Sqlite
1.  Usar o Mac OS X Leopard (10.5)
1.  Execute seu aplicativo no simulador.


O problema é que Mono é pegar o OS X `libsqlite3.dylib`, do não o iPhoneSimulator `libsqlite3.dylib` arquivo. Seu aplicativo *será* funcionam no dispositivo, mas não o simulador.

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>Implantar a falha do dispositivo com System. Exception: AMDeviceInstallApplication retornou 3892346901

Esse erro significa que a configuração de assinatura de código para a id do certificado/pacote não coincide com o perfil de provisionamento instalado no dispositivo.  Confirmar tiver o certificado apropriado selecionado nas opções de projeto -> iPhone assinatura de pacote e a id do pacote correto especificada nas opções de projeto de aplicativo do iPhone ->

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>Conclusão de código não está funcionando no Visual Studio para Mac

Certifique-se de que você estiver usando a versão mais recente do Visual Studio para Mac e xamarin

Se o problema ainda estiver presente, [registrar um bug](http://monodevelop.com/Developers#Reporting_Bugs), anexando o **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componentes-{TIMESTAMP}. log** arquivos de log.

Se tudo o mais falhar, você pode tentar remover o cache de conclusão de código para que ele seja regenerado:

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

Tenha cuidado para que você digitar esse comando corretamente ou acidentalmente, você pode remover arquivos importantes.

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>O Visual Studio para Mac Falha ao copiar texto

Os utilitários de Mac populares QuickSilver, barra de ferramentas do Google e barra inicial têm recursos de área de transferência que corrompem Visual Studio para a memória do Mac. Em suas opções, você pode listar o Visual Studio para Mac como um processo que não devem interferir com.

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>O Visual Studio para Mac reclama sobre 2.4 Mono necessária

Se você atualizou o Visual Studio para Mac devido a uma atualização recente e, quando você tentar iniciá-lo novamente ele reclama sobre 2.4 Mono não estarem presentes, tudo o que você precisa fazer é [atualizar sua instalação Mono 2.4](http://www.go-mono.com/mono-downloads/download.html).  

2.4.2.3_6 mono corrige alguns problemas importantes que impediram que o Visual Studio para Mac executado de forma confiável, suspenso, às vezes, o Visual Studio para Mac na inicialização ou impediram que o banco de dados de conclusão de código que está sendo gerado.

Depois de instalar o novo Mono, Visual Studio para Mac será iniciado como esperado.

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>Asserção em... /.. /.. /.. /Mono/Metadata/Generic-Sharing.c:704, condição não atendida ' oti'

Se você estiver recebendo o rastreamento de pilha a seguir:

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

Isso significa que você está vinculando uma biblioteca estática compilada com o código de thumb em seu projeto. A partir do iPhone SDK versão 3.1 (ou superior no momento da redação deste artigo) Apple introduziu um bug no seu vinculador ao vincular o código de não Thumb (xamarin) com o código de Thumb (sua biblioteca estática). Você precisará vincular com uma versão não Thumb da sua biblioteca estática para atenuar esse problema.

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System. ExecutionEngineException: Tentativa () de Foo[]:System.Collections.Generic.ICollection'1.get_Count de método (wrapper gerenciado para gerenciado) de compilação JIT

O sufixo de [] indica que você ou a biblioteca de classe está chamando um método em uma matriz por meio de uma coleção genérica, como <> IEnumerable, ICollection <> ou <> IList. Como alternativa, você pode forçar explicitamente o compilador AOT para incluir esse método chamando o método por conta própria e, certificando-se de que esse código é executado antes da chamada que disparou a exceção. Nesse caso, você poderia escrever:

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

Que forçará o compilador AOT para incluir o método get_Count.

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>O Visual Studio para o editor de origem de Mac é extremamente lento

Às vezes, o Visual Studio para o editor de origem Mac torna-se extremamente lento, que aparecem desligar por alguns segundos entre digitar caracteres.

Esse problema é muito raro e extremamente difíceis de reproduzir - ele geralmente não pode ser reproduzido na mesma máquina após reiniciar o Visual Studio para Mac. Por esse motivo agradeceríamos-lo se você pode executar várias etapas de depuração antes de reiniciar o Visual Studio para Mac e enviar os resultados para nós.

1.  Tente fechar a guia editor e abri-lo novamente. É necessário um pouco de edição ou mover o cursor ao redor até que a lentidão ocorra novamente?
1.  Desabilitar a "sincronização de transmissão usando a ferramenta de desenvolvedor"Quartz Debug"(que pode ser encontrado usando o destaque) e verifique se o desempenho do editor de origem será restaurado ao normal.
1.  Tente repetir a etapa (1) com a sincronização de transmissão ainda está desabilitado.
1.  Se o editor de trava por mais de alguns segundos, tente executar "killall-sair [Visual Studio para Mac]" em um terminal enquanto ele é suspenso. Pode ser difícil para o comando kill para ocorrer enquanto o editor é suspenso, mas é essencial para fazer isso, porque o comando força Mono para gravar o log MD que podemos usar para descobrir o estado em que os threads estão na enquanto os XS é suspenso rastreamentos de pilha de todos os threads de tempo.



Anexe os logs de XS **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**, **AndroidTools-{TIMESTAMP}. log**, e **componentes-{TIMESTAMP}. log**(nas versões mais antigas do XS/MonoDevelop, basta enviar **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**).

 **Observação: Esse problema foi corrigido no XS 2.2 Final**

## <a name="compiled-application-is-very-large"></a>Aplicativo compilado é muito grande

Para dar suporte à depuração, compilações de depuração contêm um código adicional. Projetos criados no modo de versão são uma fração do tamanho.

A partir do xamarin 1.3 de depuração compilações incluído suporte à depuração para cada componente único de Mono (cada método na classe de todas as estruturas).  

Com xamarin 1.4 apresentaremos um método melhor refinado para depuração, o padrão será apenas fornecer instrumentação de depuração para seu código e suas bibliotecas e não fazer isso para todos os [assemblies Mono](~/cross-platform/internals/available-assemblies.md) (isso ainda é possível, mas você precisará aceitar na depuração esses assemblies).

## <a name="installation-hangs"></a>Paradas de instalação

Se você tiver a execução do simulador de iPhone de suspensão instaladores Mono e xamarin. Esse problema não está limitado a Mono ou xamarin, esse é um problema consistente em qualquer software que tenta instalar software em MacOS neve Leopard se iPhone simulador está em execução no momento da instalação.

Verifique se você fechar o simulador de iPhone e tente novamente a instalação.

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>Ficou sem trampolines do tipo 0

Se você receber essa mensagem durante a execução do dispositivo, você pode criar o tipo mais 0 trampolines (tipo específico), modificando a seção do projeto opções "iPhone compilação".  Você deseja adicionar destinos de compilação de argumentos extra para o dispositivo:

 `-aot "ntrampolines=2048"`

O número padrão de trampolines é 1024.  Tente aumentar esse número até que você tem suficiente para seu aplicativo.

## <a name="ran-out-of-trampolines-of-type-1"></a>Ficou sem trampolines do tipo 1

Se você usar intensamente genéricos recursiva, você pode receber esta mensagem no dispositivo.  Você pode criar o tipo mais trampolines 1 (tipo RGCTX) modificando-se a seção do projeto opções "iPhone compilação".  Você deseja adicionar destinos de compilação de argumentos extra para o dispositivo:

 `-aot "nrgctx-trampolines=2048"`

O número padrão de trampolines é 1024.  Tente aumentar esse número até que você tem suficiente para o uso de genéricos.

## <a name="ran-out-of-trampolines-of-type-2"></a>Ficou sem trampolines do tipo 2

Se você fizer uso intensivo de interfaces, você pode receber esta mensagem no dispositivo.
Você pode criar o tipo mais 2 trampolines (IMT conversões de tipo), modificando a seção do projeto opções "iPhone compilação".  Você deseja adicionar destinos de compilação de argumentos extra para o dispositivo:

 `-aot "nimt-trampolines=512"`

O número padrão de trampolines IMT conversão é 128.  Tente aumentar esse número até que você tem suficiente para o uso de interfaces.

## <a name="debugger-is-unable-to-connect-with-the-device"></a>Depurador é capaz de se conectar com o dispositivo

Quando você inicia a depuração de uma configuração de dispositivo, você verá o depurador mostrar uma caixa de diálogo indicando que ele está tentando se conectar ao aplicativo. Há várias razões, que o depurador pode não ser capaz de se conectar ao aplicativo, dependendo do modo que você está usando para se conectar (Wi-Fi ou USB).

 **Se o dispositivo e o host do depurador estão em redes diferentes**, um firewall ou rede privada pode estar impedindo a conexão com o host do depurador no modo de Wi-Fi do aplicativo.

 **Visual Studio para Mac pode não ser capaz de consultar o IP correto do host**. Em Wi-Fi modo Visual Studio para Mac dá ao aplicativo todos os IPs encontre do host e o aplicativo tenta todos eles para ver se ele pode usar qualquer um para se conectar ao Visual Studio para Mac.

 **Outro dispositivo está conectado a uma porta USB no host.** Em alguns casos, outros dispositivos conectados à porta USB portas no host são conhecidas por alguma forma interferir com a depuração no modo USB.

Se o modo de Wi-Fi ou USB não funcionar, você pode tentar outro facilmente: no Visual Studio para Mac, abra as preferências, vá para a página de preferências/depurador/iPhone depurador e alternar a caixa de seleção "Depurar dispositivos iOS em Wi-Fi em vez de por USB".   Se não funcionar, você pode ver mais informações sobre a falha no console do dispositivo no modo detalhado (que é habilitado adicionando "-v - v - v" para os argumentos adicionais mtouch nas opções do projeto).

## <a name="error-134-mtouch-failed-with-the-following-message"></a>Erro 134: mtouch falha com a seguinte mensagem:

Esse erro poderá ocorrer se você estiver tentando criar com - nolink no estilo xamarin 1.4 das versões. Você pode contornar esse erro especificando argumentos adicionais na sua configuração de projeto monodevelop.

Adicione o argumento

 `-nosymbolstrip`

e o problema deve ser resolvido.

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Identidade de distribuição não é mostrada no Visual Studio para o projeto de Mac opções de assinatura

O Visual Studio para Mac 2.2 tem um bug que faz com que ele não detectar os certificados de distribuição que contêm uma vírgula. Atualize para o Visual Studio para Mac 2.2.1.

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>Erro "AFCFileRefWrite retornado: 1" durante o carregamento

Ao carregar um aplicativo em seu dispositivo, você poderá receber um erro "AFCFileRefWrite retornado: 1". Isso pode acontecer se você tiver um arquivo de comprimento zero.

## <a name="error-mtouch-failed-with-no-output"></a>Erro "mtouch falhou sem saída"

A versão atual do xamarin e o Visual Studio para Mac falhar quando o nome do projeto ou o diretório onde são armazenadas a solução ou projeto conter espaços.
Para corrigir isso:


-  Certifique-se de que nem o projeto ou o diretório onde ele está armazenado contiver um espaço.
-  No seu projeto "principais configurações", certifique-se de que o nome do projeto não contém espaços.

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>Erro "o binário carregado era inválido. Uma versão de pré-lançamento beta do SDK foi usada para criar o aplicativo"

Esse erro é geralmente causado com um projeto que foi iniciado em desenvolvimento iPad antes do lançamento do xamarin 2.0.0, você provavelmente terá algumas chaves no seu Info. plist como:

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

Esse par de chaves deve ser removido como o Visual Studio para Mac trata-lo para você automaticamente.

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>Erro "uma versão de pré-lançamento beta do SDK foi usada para criar o aplicativo"

(Contribuição Ed Anuff)

Siga estas etapas:

-  Alteração de conectar-se a versão do SDK no iPhone versão 3.2 ou iTunes rejeitará no carregamento porque ele está vendo um aplicativo compatível iPad criado usando uma versão do SDK com menos de 3.2
-  Crie um Info. plist personalizado para o projeto e definir explicitamente MinimumOSVersion 3.0 nele.   Isso substituirá o valor de MinimumOSVersion 3.2 definido por xamarin.   Se você não fizer isso, o aplicativo não poderá executar em um iPhone.
-  Reconstrução, zip e o carregamento iTunes se conectar.

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>Exceção sem tratamento: System. Exception: Falha ao localizar someSelector seletor: em {type}

Essa exceção é causada por um dos três coisas:


1.  Você forneceu um seletor para o tempo de execução Objective-C sem aplicar o atributo [exportação] correspondente a um método
1.  Você habilita a vinculação completa e não aplicado o atributo [preservar] para o método de exportação] ed.
1.  Você aplicou o atributo [exportação] para um método privado em um tipo herdado.


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>Arquivo MainWindow.xib.designer.cs não for atualizado

Ocorreu um erro no Xamarin Studio 2.4 que fez com que ele não agrupar o arquivo MainWindow.xib com o arquivo MainWindow.xib.designer em novos projetos. Isso significa que ele não atualizará o código de designer para esse arquivo específico.

Esse problema é corrigido na versão do Visual Studio para Mac está disponível no seu atualizador interna, portanto certifique-se de usar a versão mais recente.

Você pode corrigir os projetos existentes, removendo (não excluir) xib e seu arquivo de designer, em seguida, adicioná-lo novamente. Isso deve reagrupar os arquivos corretamente.

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView ou UIActionSheet desaparecer depois que está sendo criado

Se você tiver um código como este:

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

o objeto "actionSheet" existe como uma variável temporária na função e assim que a função termina, o objeto é qualificado para a coleta de lixo, para que ele acaba sendo coletado como lixo.

Para corrigir esse problema, você precisa manter uma referência a "actionSheet" fora de seu método, em algum lugar que operará além do seu método.

## <a name="project-always-runs-in-the-ipad-simulator"></a>Projeto sempre é executado no iPad simulador

O instalador do iPhone SDK 4.0 instala 2 SDKs - o SDK 3.2, para a criação de aplicativos de iPad e o SDK 4.0, usado para aplicativos Universal e construção iPhone. Ele também instala um simulador 3.2, que simula a apenas um iPad, e o simulador 4.0 que simula o iPhone ou iPhone 4. Todos os SDKs e simuladores mais antigos são removidos.

O Visual Studio para opções de compilação de projeto do Mac iPhone incluem uma configuração para a versão do SDK que será usada na criação de seu aplicativo. Essa configuração pode ser encontrada em **opções de projeto -> construção -> iPhone compilação**.

Novos projetos no Visual Studio para Mac usam o SDK instalado mais antigo como sua configuração de SDK padrão e se o SDK especificado não existir, o Visual Studio para Mac usará o mais próximo que pode encontrar ao criar seu aplicativo. Isso foi feito para que os projetos não sempre seriam requre o SDK mais recente. No entanto, isso é atualmente resulta no 3.2 SDK que está sendo usado - que resulta no simulador iPad que está sendo usado.

Para corrigir esse problema usando o SDK 4.0, vá para **opções de projeto -> construção -> iPhone compilação**> e altere o valor do SDK para "4.0" usando a caixa suspensa. Você deve fazer isso para cada combinação de plataforma, acessada por meio de listas suspensas na parte superior do painel e a configuração.

A versão do SDK não deve ser confundida com a configuração de "Versão do sistema operacional mínimo".
Esse valor não precisa corresponder ao valor de versão do SDK - ele afeta a versão mínima do sistema operacional do seu aplicativo será instalado, que pode ser mais antigo que o SDK, desde que você usar apenas as APIs que existe no sistema operacional mais antigo, ou impedir o uso de recursos mais recentes usando Veri de versão do sistema operacional em tempo de execução ks. Você deve configurá-lo para a versão mais antiga do sistema operacional em que você teste seu aplicativo.

Observe também que o **projeto -> iPhone simulador destino**> menu pode ser usado para separar o simulador é usado por padrão durante a execução/depuração de um projeto. Além disso, o **execução -> executar com**> menu pode ser usado para escolher um simulador específico com o qual deseja executar

## <a name="ibtool-returns-error-133"></a>ibtool retorna erro 133

Isso significa que você tenha XCode 4 está instalado.   No XCode 4, a ferramenta ibtool foi removido, não é possível editar os arquivos XIB com uma ferramenta autônoma.

Se você quiser usar o construtor de Interface, instalar [XCode série 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792), disponível no site da Apple.

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"Não é possível criar associação de exibição para o tipo de mime: application/vnd.apple -<wbr/>construtor da interface"

Esse erro ocorre se você tentar criar um iPhone da interface do usuário de um projeto não iPhone. Certifique-se de que você iniciar com uma solução iPhone/iPad, não é possível adicionar apenas os elementos de interface do usuário do iPhone para um projeto não iPhone/iPad.

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>Falha de inicialização ao executar dentro do simulador de iOS

Se você receber uma falha de tempo de execução (SIGSEGV) dentro do simulador junto com um rastreamento de pilha que tem esta aparência:

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
... então você provavelmente terá uma (ou mais) assembly obsoleto no seu diretório de aplicativo do simulador. Esses assemblies podem existir como Apple iOS simulador adiciona e atualiza arquivos, mas nunca exclui-los. Se isso acontecer, em seguida, a solução mais fácil é selecionar "Redefinir e conteúdo e configurações..." no menu do simulador.   

**Aviso:** isso removerá todos os arquivos, aplicativos e dados do simulador.   Próxima vez que executar seu aplicativo, o Visual Studio para Mac implantá-lo no simulador e não haverá nenhum assembly antigo, obsoleto que causam a falha.

## <a name="simulator-hangs-during-application-installation"></a>Simulador trava durante a instalação do aplicativo

Isso pode acontecer quando os nomes de aplicativo incluir um '.' (ponto) em seu nome.
Isso é proibido como o nome do executável no CFBundleExecutable - mesmo se ele puder funciona em muitos outros casos (como dispositivos).

 * "O valor não deve incluir qualquer extensão de nome." - [http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>Erro: "tipo de atributo personalizado 0x43 não é suportado" quando duplo clicando em arquivos de .xib

Isso é causado pela tentativa de abrir arquivos .xib quando as variáveis de ambiente são definidas incorretamente. Isso não deve ocorrer com o uso normal do Visual Studio para Mac/Xamarin.iOS e abrir novamente o Visual Studio para Mac de/aplicativos deve corrigir o problema.

Ao tentar atualizar o software e essa mensagem de erro for exibida, por favor, email *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>Aplicativo é executado no simulador, mas falhar no dispositivo

Esse problema pode se manifestar de várias formas e sempre não produz um erro consistente. Se o aplicativo contém um .xib, verifique se o **ação de compilação** no .xib é definido como **InterfaceDefinition**. Essa é a ação de compilação padrão para .xibs.

Para verificar a ação de compilação, clique com o botão direito no arquivo .xib e escolha **ação de compilação**.


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: Não há dados disponíveis para codificação 437

Ao incluir 3º bibliotecas de terceiros em seu aplicativo xamarin, você poderá receber um erro no formato "NotSupportedException: não há dados disponíveis para codificação 437" ao tentar compilar e executar o aplicativo. Por exemplo, bibliotecas, como `Ionic.Zip.ZipFile`, pode gerar essa exceção durante a operação.

Isso pode ser resolvido, abra as opções para o projeto xamarin, vai **iOS compilação** > **internacionalização** e verificando o **Oeste** Internacionalização.



<a name="Can't_upgrade_to_Indie/Business_from_Trial_Account" />


## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>Não é possível atualizar o Indie de negócios de conta de avaliação

Se você adquiriu recentemente xamarin e iniciou anteriormente uma versão de avaliação do xamarin, você precisará concluir as etapas a seguir para obter essa alteração de licença selecionada pelo Visual Studio para Mac ou o Visual Studio.

-  Feche o Visual Studio para Mac/Visual Studio
-  Remover todos os arquivos de ~/Library/MonoTouch no Mac ou %PROGRAMDATA%\MonoTouch\License\ para Windows
-  Abra novamente o Visual Studio para Mac/Visual Studio e compilar um projeto do xamarin


## <a name="receiving-activation-incomplete-error-message"></a>Recebendo a mensagem de erro 'Ativação incompletas'

Esse problema pode ocorrer ao usar o xamarin para Visual Studio. Para resolver esse problema, envie os logs do seguinte local para [ contact@xamarin.com ](mailto:contact@xamarin.com).

-  Local do log: %LocalAppData%/Xamarin/Logs
