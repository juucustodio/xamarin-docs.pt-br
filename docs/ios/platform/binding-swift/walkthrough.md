---
title: 'Walkthrough: associar uma biblioteca Swift do iOS'
description: Este artigo fornece uma explicação prática de como criar uma associação do Xamarin. iOS para uma estrutura Swift existente, Gigya. Ele aborda tópicos como compilar uma estrutura Swift, associá-lo e usar a associação em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: B563ADE9-D0E3-4BC3-A288-66D2296BE706
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 3bc6f0a95bdd01991ccea69d7917f5fabcb9f51b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430968"
---
# <a name="walkthrough-bind-an-ios-swift-library"></a>Walkthrough: associar uma biblioteca Swift do iOS

> [!IMPORTANT]
> No momento, estamos investigando o uso de associação personalizada na plataforma Xamarin. Faça [**esta pesquisa**](https://www.surveymonkey.com/r/KKBHNLT) para informar os futuros esforços de desenvolvimento.

O Xamarin permite que os desenvolvedores móveis criem experiências móveis nativas entre plataformas usando o Visual Studio e o C#. Você pode usar os componentes do SDK da plataforma iOS prontos para uso. Mas, em muitos casos, você também deseja usar SDKs de terceiros desenvolvidos para essa plataforma, que o Xamarin permite fazer por meio de associações. Para incorporar uma estrutura de Objective-C de terceiros em seu aplicativo Xamarin. iOS, você precisa criar uma associação Xamarin. iOS para poder usá-la em seus aplicativos.

A plataforma iOS, juntamente com suas ferramentas e linguagens nativas, estão constantemente evoluindo e o Swift é uma das áreas mais dinâmicas do mundo de desenvolvimento do iOS agora. Há vários SDKs de terceiros, que já foram migrados do Objective-C para o Swift e nos apresenta novos desafios. Embora o processo de ligação Swift seja semelhante ao Objective-C, ele requer etapas adicionais e definições de configuração para compilar e executar com êxito um aplicativo Xamarin. iOS que é aceitável para o AppStore.

O objetivo deste documento é descrever uma abordagem de alto nível para lidar com esse cenário e fornecer um guia passo a passo detalhado com um exemplo simples.

## <a name="background"></a>Segundo plano

A Swift foi introduzida inicialmente pela Apple em 2014 e agora está na versão 5,1, com a adoção de estruturas de terceiros crescendo rapidamente. Você tem algumas opções para associar uma estrutura Swift e este documento descreve a abordagem usando o cabeçalho de interface gerado pelo Objective-C. O cabeçalho é criado automaticamente pelas ferramentas do Xcode quando uma estrutura é criada e é usado como uma maneira de se comunicar do mundo gerenciado com o mundo Swift.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este passo a passo, você precisa de:

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](../../../cross-platform/macios/binding/objective-sharpie/get-started.md#installing-objective-sharpie)
- [CLI do AppCenter](/appcenter/test-cloud/) (opcional)

## <a name="build-a-native-library"></a>Criar uma biblioteca nativa

A primeira etapa é criar uma estrutura Swift nativa com o cabeçalho Objective-C habilitado. A estrutura geralmente é fornecida por um desenvolvedor de terceiros e tem o cabeçalho inserido no pacote no seguinte diretório: ** \<FrameworkName> . Framework/Headers/ \<FrameworkName> -Swift. h**.

Esse cabeçalho expõe as interfaces públicas, que serão usadas para criar metadados de associação do Xamarin. iOS e gerar classes C# expondo os membros do Swift Framework. Se o cabeçalho não existir ou tiver uma interface pública incompleta (por exemplo, você não vê classes/Membros), você tem duas opções:

- Atualize o código-fonte Swift para gerar o cabeçalho e marcar os membros necessários com o `@objc` atributo
- Criar uma estrutura de proxy onde você controla a interface pública e o proxy de todas as chamadas para a estrutura subjacente

Neste tutorial, a segunda abordagem é descrita, pois ela tem menos dependências em código-fonte de terceiros, que nem sempre está disponível. Outro motivo para evitar a primeira abordagem é o esforço adicional necessário para dar suporte a futuras alterações de estrutura. Depois de começar a adicionar alterações ao código-fonte de terceiros, você será responsável por dar suporte a essas alterações e, potencialmente, mesclá-las com cada atualização futura.

Por exemplo, neste tutorial, uma associação para o [SDK do Gigya Swift](https://developers.gigya.com/display/GD/Swift+SDK) é criada:

1. Abra o Xcode e crie uma nova estrutura Swift, que será um proxy entre o código Xamarin. iOS e a estrutura Swift de terceiros. Clique em **arquivo > novo projeto de >** e siga as etapas do assistente:

    ![Criar projeto de estrutura do Xcode](walkthrough-images/xcode-create-framework-project.png)

    ![projeto do Framework de nome do Xcode](walkthrough-images/xcode-name-framework-project.png)

1. Baixe a [estrutura do Gigya](https://developers.gigya.com/display/GD/Swift+SDK) do site do desenvolvedor e Desembale-a. No momento da gravação, a versão mais recente é [Gigya Swift SDK 1.0.9](https://downloads.gigya.com/predownload?fileName=Swift-Core-framework-1.0.9.zip)

1. Selecione o **SwiftFrameworkProxy** no explorador de arquivos de projeto e, em seguida, selecione a guia geral

1. Arraste e solte o pacote **Gigya. Framework** na lista estruturas e bibliotecas do Xcode na guia geral, marque a opção **copiar itens se necessário** ao adicionar a estrutura:

    ![estrutura de cópia do Xcode](walkthrough-images/xcode-copy-framework.png)

    Verifique se a estrutura Swift foi adicionada ao projeto, caso contrário as opções a seguir não estarão disponíveis.

1. Verifique se a opção **não inserir** está selecionada, o que será controlado mais tarde manualmente:

    [![opção donotembed do Xcode](walkthrough-images/xcode-donotembed-option.png)](walkthrough-images/xcode-donotembed-option.png#lightbox)

1. Verifique se a opção de configurações de compilação **sempre inserir bibliotecas Standard Swift**, que inclui bibliotecas Swift com a estrutura está definida como não. Ele será posteriormente controlado manualmente, que é o dylibs Swift incluído no pacote final:

    [![opção Xcode sempre inserir falso](walkthrough-images/xcode-alwaysembedfalse-option.png)](walkthrough-images/xcode-alwaysembedfalse-option.png#lightbox)

1. Verifique se a opção **habilitar BitCode** está definida como **não**. A partir do momento, o Xamarin. iOS não inclui BitCode enquanto a Apple exige que todas as bibliotecas ofereçam suporte às mesmas arquiteturas:

    [![opção do Xcode habilitar BitCode false](walkthrough-images/xcode-enablebitcodefalse-option.png)](walkthrough-images/xcode-enablebitcodefalse-option.png#lightbox)

    Você pode verificar se a estrutura resultante tem a opção BitCode desabilitada executando o seguinte comando de terminal em relação à estrutura:

    ```bash
    otool -l SwiftFrameworkProxy.framework/SwiftFrameworkProxy | grep __LLVM
    ```

    O resultado deve estar vazio, caso contrário, você deseja examinar as configurações do projeto para sua configuração específica.

1. Verifique se a opção **nome do cabeçalho da interface gerada pelo Objective-C** está habilitada e especifica um nome de cabeçalho. O nome padrão é ** \<FrameworkName> -Swift. h**:

    [![opção de cabeçalho do Xcode objectice-c habilitado](walkthrough-images/xcode-objcheaderenabled-option.png)](walkthrough-images/xcode-objcheaderenabled-option.png#lightbox)

1. Exponha os métodos desejados e marque-os com `@objc` atributo e aplique regras adicionais definidas abaixo. Se você criar a estrutura sem essa etapa, o cabeçalho Objective-C gerado estará vazio e o Xamarin. iOS não poderá acessar os membros do Swift Framework. Expor a lógica de inicialização para o SDK do Gigya Swift subjacente criando um novo arquivo Swift **SwiftFrameworkProxy. Swift** e definindo o código a seguir:

    ```swift
    import Foundation
    import UIKit
    import Gigya

    @objc(SwiftFrameworkProxy)
    public class SwiftFrameworkProxy : NSObject {

        @objc
        public func initFor(apiKey: String) -> String {
            Gigya.sharedInstance().initFor(apiKey: apiKey)
            let gigyaDomain = Gigya.sharedInstance().config.apiDomain
            let result = "Gigya initialized with domain: \(gigyaDomain)"
            return result
        }
    }
    ```

    Algumas observações importantes sobre o código acima:

    - Importe o módulo Gigya aqui do SDK de Gigya de terceiros original e agora pode acessar qualquer membro da estrutura.
    - Marque a classe SwiftFrameworkProxy com o `@objc` atributo especificando um nome; caso contrário, será gerado um nome exclusivo ilegível, como `_TtC19SwiftFrameworkProxy19SwiftFrameworkProxy` . O nome do tipo deve ser claramente definido, pois ele será usado posteriormente por seu nome.
    - Herda a classe de proxy de `NSObject` , caso contrário, ela não será gerada no arquivo de cabeçalho Objective-C.
    - Marque todos os membros a serem expostos como `public` .

1. Altere a configuração de compilação do esquema de **depurar** para **versão**. Para fazer isso, abra a caixa de diálogo **Xcode > destino > editar esquema** e defina a opção de **configuração de compilação** como **liberar**:

    ![esquema de edição do Xcode](walkthrough-images/xcode-edit-scheme.png)

    ![versão do esquema do Xcode Edit](walkthrough-images/xcode-edit-scheme-release.png)

1. Neste ponto, a estrutura está pronta para ser criada. Crie a estrutura para as arquiteturas de simulador e dispositivo e, em seguida, combine as saídas como um único pacote de estrutura. Identifique as versões do SDK instaladas para criar o código-fonte usando a ferramenta **xcodebuild** :

    ```bash
    xcodebuild -showsdks
    ```

    A saída será semelhante ao seguinte:

    ```bash
    iOS SDKs:
            iOS 13.2                        -sdk iphoneos13.2
    iOS Simulator SDKs:
            Simulator - iOS 13.2            -sdk iphonesimulator13.2
    macOS SDKs:
            DriverKit 19.0                  -sdk driverkit.macosx19.0
            macOS 10.15                     -sdk macosx10.15
    tvOS SDKs:
            tvOS 13.2                       -sdk appletvos13.2
    tvOS Simulator SDKs:
            Simulator - tvOS 13.2           -sdk appletvsimulator13.2
    watchOS SDKs:
            watchOS 6.1                     -sdk watchos6.1
    watchOS Simulator SDKs:
            Simulator - watchOS 6.1         -sdk watchsimulator6.1
    ```

    Escolha uma versão desejada do SDK do iOS e do SDK do iOS Simulator, neste caso, a versão 13,2 e execute a compilação com o comando a seguir

    ```bash
    xcodebuild -sdk iphonesimulator13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    xcodebuild -sdk iphoneos13.2 -project "Swift/SwiftFrameworkProxy/SwiftFrameworkProxy.xcodeproj" -configuration Release
    ```

    > [!TIP]
    > Se você tiver um espaço de trabalho em vez de projeto, crie o espaço de trabalho e especifique o destino como um parâmetro necessário. Você também deseja especificar um diretório de saída porque para os espaços de trabalho esse diretório será diferente do para compilações do projeto.

    > [!TIP]
    > Você também pode usar [o script auxiliar](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L3-L14) para criar a estrutura para todas as arquiteturas aplicáveis ou apenas compilá-la do simulador de comutação do Xcode e do dispositivo no seletor de destino.

1. Há duas estruturas Swift, uma para cada plataforma, combinando-as como um único pacote a ser inserido em um projeto de associação Xamarin. iOS mais tarde. Para criar uma estrutura de FAT, que combina as duas arquiteturas, você precisa executar as etapas a seguir. O pacote de estrutura é apenas uma pasta para que você possa fazer todos os tipos de operações, como adicionar, remover e substituir arquivos:

    - Navegue até a pasta de saída da compilação com as subpastas **Release-iPhoneOS** e **Release-iphonesimulator** e copie uma das estruturas como uma versão inicial da saída final (estrutura FAT).

        ```bash
        cp -R "Release-iphoneos" "Release-fat"
        ```

    - Combinar módulos de outra compilação com os módulos da estrutura Fat

        ```bash
        cp -R "Release-iphonesimulator/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/" "Release-fat/SwiftFrameworkProxy.framework/Modules/SwiftFrameworkProxy.swiftmodule/"
        ```

    - Combinar a configuração iPhoneOS + iphonesimulator como uma estrutura de Fat

        ```bash
        lipo -create -output "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphoneos/SwiftFrameworkProxy.framework/SwiftFrameworkProxy" "Release-iphonesimulator/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

    - Verificar os resultados

        ```bash
        lipo -info "Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy"
        ```

        A saída deve renderizar o seguinte, refletindo o nome da estrutura e as arquiteturas incluídas:

        ```bash
        Architectures in the fat file: Release-fat/SwiftFrameworkProxy.framework/SwiftFrameworkProxy are: x86_64 arm64
        ```

    > [!TIP]
    > Se você quiser dar suporte a apenas uma única plataforma (por exemplo, você está criando um aplicativo, que pode ser executado somente em um dispositivo), você pode ignorar a etapa para criar a biblioteca de FAT e usar a estrutura de saída da compilação do dispositivo anteriormente.

    > [!TIP]
    > Você também pode usar [o script auxiliar](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L16-L24) para criar a estrutura de FAT, que automatiza todas as etapas acima.

## <a name="prepare-metadata"></a>Preparar metadados

Neste momento, você deve ter a estrutura com o cabeçalho de interface gerado por Objective-C pronto para ser consumido por uma associação do Xamarin. iOS.  A próxima etapa é preparar as interfaces de definição de API, que são usadas por um projeto de associação para gerar classes C#. Essas definições podem ser criadas manualmente ou automaticamente pela ferramenta de [nitidez objetiva](../../../cross-platform/macios/binding/objective-sharpie/index.md) e pelo arquivo de cabeçalho gerado. Use a nitidez para gerar os metadados:

1. Baixe a última ferramenta de [nitidez do objetivo](../../../cross-platform/macios/binding/objective-sharpie/index.md) do site de downloads oficiais e instale-a seguindo o assistente. Quando a instalação for concluída, você poderá verificá-la executando o comando de nitidez:

    ```bash
    sharpie -v
    ```

1. Gerar metadados usando nitidez e o arquivo de cabeçalho Objective-C gerado automaticamente:

    ```bash
    sharpie bind --sdk=iphoneos13.2 --output="XamarinApiDef" --namespace="Binding" --scope="Release-fat/SwiftFrameworkProxy.framework/Headers/" "Release-fat/SwiftFrameworkProxy.framework/Headers/SwiftFrameworkProxy-Swift.h"
    ```

    A saída reflete os arquivos de metadados que estão sendo gerados: **ApiDefinitions.cs** e **StructsAndEnums.cs**. Salve esses arquivos para a próxima etapa para incluí-los em um projeto de associação do Xamarin. iOS juntamente com as referências nativas:

    ```bash
    Parsing 1 header files...
    Binding...
        [write] ApiDefinitions.cs
        [write] StructsAndEnums.cs
    ```

    A ferramenta irá gerar metadados C# para cada membro Objective-C exposto, que será semelhante ao código a seguir. Como você pode ver, ele pode ser definido manualmente porque tem um formato legível por humanos e mapeamento direto de membros:

    ```csharp
    [Export ("initForApiKey:")]
    string InitForApiKey (string apiKey);
    ```

    > [!TIP]
    > O nome do arquivo de cabeçalho pode ser diferente se você alterou as configurações padrão do Xcode para o nome do cabeçalho. Por padrão, ele tem o nome de um projeto com o sufixo **-Swift** . Você sempre pode verificar o arquivo e seu nome navegando até a pasta cabeçalhos do pacote do Framework.

    > [!TIP]
    > Como parte do processo de automação, você pode usar [o script auxiliar](https://github.com/alexeystrakh/xamarin-binding-swift-framework/blob/master/Swift/Scripts/build.fat.sh#L35) para gerar metadados automaticamente após a criação da estrutura de Fat.

## <a name="build-a-binding-library"></a>Criar uma biblioteca de associação

A próxima etapa é criar um projeto de associação do Xamarin. iOS usando o modelo de associação do Visual Studio, adicionar metadados necessários, referências nativas e, em seguida, compilar o projeto para produzir uma biblioteca de consumo:

1. Abra Visual Studio para Mac e crie um novo projeto de biblioteca de associação do Xamarin. iOS, neste caso, SwiftFrameworkProxy. Binding e conclua o assistente. O modelo de associação do Xamarin. iOS está localizado pelo seguinte caminho: **biblioteca de > do iOS > biblioteca de associação**:

    ![criar biblioteca de associação do Visual Studio](walkthrough-images/visualstudio-create-binding-library.png)

1. Exclua os arquivos de metadados existentes **ApiDefinition.cs** e **structs.cs** , pois eles serão substituídos completamente pelos metadados gerados pela ferramenta de nitidez objetiva.
1. Copie os metadados gerados pela nitidez em uma das etapas anteriores, selecione a seguinte ação de compilação na janela Propriedades: **ObjBindingApiDefinition** para o arquivo **ApiDefinitions.cs** e **ObjBindingCoreSource** para o arquivo **StructsAndEnums.cs** :

    ![metadados de estrutura do projeto do Visual Studio](walkthrough-images/visualstudio-project-structure-metadata.png)

    Os metadados em si descrevem cada classe objetiva-C exposta e membro usando a linguagem C#. Você pode ver a definição do cabeçalho Objective-C original juntamente com a declaração do C#:

    ```csharp
    // @interface SwiftFrameworkProxy : NSObject
    [BaseType (typeof(NSObject))]
    interface SwiftFrameworkProxy
    {
        // -(NSString * _Nonnull)initForApiKey:(NSString * _Nonnull)apiKey __attribute__((objc_method_family("none"))) __attribute__((warn_unused_result));
        [Export ("initForApiKey:")]
        string InitForApiKey (string apiKey);
    }
    ```

    Embora seja um código C# válido, ele não é usado como está, mas sim é usado pelas ferramentas do Xamarin. iOS para gerar classes C# com base nessa definição de metadados. Como resultado, em vez da interface SwiftFrameworkProxy, você obtém uma classe C# com o mesmo nome, que pode ser instanciado pelo código do Xamarin. iOS. Essa classe obtém métodos, propriedades e outros membros definidos por seus metadados, que você chamará de uma maneira C#.

1. Adicione referência nativa à estrutura Fat anterior gerada, bem como a cada dependência dessa estrutura. Nesse caso, adicione as referências nativas do Framework SwiftFrameworkProxy e Gigya ao projeto de associação:

    - Para adicionar referências de estrutura nativa, abra o localizador e navegue até a pasta com as estruturas. Arraste e solte as estruturas sob o local referências nativas no Gerenciador de Soluções. Como alternativa, você pode usar a opção de menu de contexto na pasta referências nativas e clicar em **Adicionar referência nativa** para pesquisar as estruturas e adicioná-las:

    ![referências nativas da estrutura do projeto do Visual Studio](walkthrough-images/visualstudio-project-structure-nativerefs.png)

    - Atualize as propriedades de cada referência nativa e marque três opções importantes:

        - Definir Smart link = true
        - Definir a carga de força = falso
        - Defina a lista de estruturas usadas para criar as estruturas originais. Nesse caso, cada estrutura tem apenas duas dependências: Foundation e UIKit. Defina-o para o campo estruturas:

        ![opções de proxy nativeref do Visual Studio](walkthrough-images/visualstudio-nativeref-proxy-options.png)

        Se você tiver quaisquer sinalizadores de vinculador adicionais para especificar, defina-os no campo sinalizadores do vinculador. Nesse caso, mantenha-o vazio.

    - Especifique sinalizadores de vinculador adicionais quando necessário. Se a biblioteca que você está associando expõe apenas APIs Objective-C, mas internamente está usando o Swift, você pode estar vendo problemas como:

        ```Console
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftQuartzCore
        error MT5209 : Native linking error : warning: Auto-Linking library not found for -lswiftCoreImage
        ```

        Nas propriedades do projeto de associação para a biblioteca nativa, os seguintes valores devem ser adicionados aos sinalizadores do vinculador:

        ```Linker
        L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphonesimulator/ -L/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/iphoneos -Wl,-rpath -Wl,@executable_path/Frameworks
        ```

        As duas primeiras opções (  `-L ...`   aquelas) dizem ao compilador nativo onde encontrar as bibliotecas Swift. O compilador nativo ignorará bibliotecas que não têm a arquitetura correta, o que significa que é possível passar o local para bibliotecas de simuladores e bibliotecas de dispositivos ao mesmo tempo, para que ele funcione para compilações de simuladores e dispositivos (esses caminhos são corretos apenas para iOS; para tvOS e watchOS precisam ser atualizados). Uma desvantagem é que essa abordagem requer que o Xcode correto esteja em/Application/Xcode.app, se o consumidor da biblioteca de associação tiver o Xcode em um local diferente, ele não funcionará. A solução alternativa é adicionar essas opções nos argumentos adicionais de mTouch nas opções de Build do iOS do projeto executável ( `--gcc_flags -L... -L...` ). A terceira opção faz com que o vinculador nativo armazene o local das bibliotecas Swift no executável, para que o sistema operacional possa encontrá-las.

1. A ação final é criar a biblioteca e verificar se você não tem nenhum erro de compilação. Muitas vezes, você descobrirá que os metadados de associações produzidos por nitidez objetiva serão anotados com o  `[Verify]`   atributo. Esses atributos indicam que você deve verificar se a nitidez do objetivo fez a coisa correta comparando a associação com a declaração objetiva-C original (que será fornecida em um comentário acima da declaração associada). Você pode saber mais sobre membros marcados com o atributo pelo [link a seguir](../../../cross-platform/macios/binding/objective-sharpie/platform/verify.md). Depois que o projeto é compilado, ele pode ser consumido por um aplicativo Xamarin. iOS.

## <a name="consume-the-binding-library"></a>Consumir a biblioteca de associação

A etapa final é consumir a biblioteca de associação do Xamarin. iOS em um aplicativo Xamarin. iOS. Crie um novo projeto Xamarin. iOS, adicione referência à biblioteca de associação e ative o SDK do Gigya Swift:

1. Criar projeto Xamarin. iOS. Você pode usar o **aplicativo > do iOS > aplicativo de exibição única** como um ponto de partida:

    ![novo aplicativo do Visual Studio](walkthrough-images/visualstudio-app-new.png)

1. Adicione uma referência de projeto de associação ao projeto de destino ou. dll criado anteriormente. Trate a biblioteca de associação como uma biblioteca do Xamarin. iOS regular:

    ![refs do aplicativo do Visual Studio](walkthrough-images/visualstudio-app-refs.png)

1. Atualizar o código-fonte do aplicativo e adicionar a lógica de inicialização ao ViewController primário, que ativa o SDK do Gigya

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
        var proxy = new SwiftFrameworkProxy();
        var result = proxy.InitForApiKey("APIKey");
        System.Diagnostics.Debug.WriteLine(result);
    }
    ```

1. Crie um botão com um nome **btnLogin** e adicione o seguinte manipulador de clique de botão para ativar um fluxo de autenticação:

    ```csharp
    private void btnLogin_Tap(object sender, EventArgs e)
    {
        _proxy.LoginWithProvider(GigyaSocialProvidersProxy.Instagram, this, (result, error) =>
        {
            // process your login result here
        });
    }
    ```

1. Execute o aplicativo, na saída de depuração, você deve ver a seguinte linha: `Gigya initialized with domain: us1.gigya.com` . Clique no botão para ativar o fluxo de autenticação:

    [![resultado do proxy Swift](walkthrough-images/swiftproxy-result.png)](walkthrough-images/swiftproxy-result.png#lightbox)

Parabéns! Você criou com êxito um aplicativo Xamarin. iOS e uma biblioteca de associação, que consome uma estrutura Swift. O aplicativo acima será executado com êxito no iOS 12.2 + porque a partir desta versão do iOS, a [Apple introduziu a estabilidade da Abi](https://swift.org/blog/swift-5-1-released/) e cada Ios que inicia o 12.2 + inclui bibliotecas de tempo de execução Swift, que poderiam ser usadas para executar seu aplicativo compilado com o Swift 5.1 +. Se você precisar adicionar suporte para versões anteriores do iOS, há mais algumas etapas a serem realizadas:

1. Para adicionar suporte para iOS 12,1 e anteriores, você deseja fornecer dylibs Swift específicas usado para compilar sua estrutura. Use o pacote NuGet [Xamarin. Ios. SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) para processar e copiar o bibliotecas necessário com seu IPA. Adicione a referência do NuGet ao seu projeto de destino e recompile o aplicativo. Nenhuma etapa adicional será necessária, o pacote NuGet instalará tarefas específicas, que serão executadas com o processo de compilação, identificará o dylibs Swift necessário e o empacotaremos com o IPA final.

1. Para enviar o aplicativo para a loja de aplicativos, você deseja usar o Xcode e distribuir a opção, que atualizará o arquivo IPA e a pasta **SwiftSupport** dylibs, de modo que ele será aceito pelo AppStore:

    ○ Arquivar o aplicativo. No menu Visual Studio para Mac, selecione **criar arquivo de > para publicação**:

    ![arquivo morto do Visual Studio para publicação](walkthrough-images/visualstudio-archiveforpublishing.png)

    Essa ação cria o projeto e o obtém para o organizador, que pode ser acessado pelo Xcode para distribuição.

    ○ Distribuir por meio do Xcode. Abra o Xcode e navegue até a janela > a opção de menu do **organizador** :

    ![arquivos mortos do Visual Studio](walkthrough-images/visualstudio-archives.png)

    Selecione o arquivo criado na etapa anterior e clique no botão distribuir aplicativo. Siga o assistente para carregar o aplicativo para o AppStore.

1. Essa etapa é opcional, mas é importante verificar se seu aplicativo pode ser executado no iOS 12,1 e versões anteriores, bem como 12,2. Você pode fazer isso com a ajuda do Test Cloud e da estrutura UITest. Crie um projeto UITest e um teste de interface do usuário básico, que executa o aplicativo:

    - Crie um projeto UITest e configure-o para seu aplicativo Xamarin. iOS:

        ![Visual Studio UITest novo](walkthrough-images/visualstudio-uitest-new.png)

        > [!TIP]
        > Você pode encontrar mais informações sobre como criar um projeto UITest e configurá-lo para seu aplicativo pelo [link a seguir](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

    - Crie um teste básico para executar o aplicativo e usar alguns dos recursos do SDK Swift. Esse teste ativa o aplicativo, tenta fazer logon e, em seguida, pressiona o botão Cancelar:

        ```csharp
        [Test]
        public void HappyPath()
        {
            app.WaitForElement(StatusLabel);
            app.WaitForElement(LoginButton);
            app.Screenshot("App loaded.");
            Assert.AreEqual(app.Query(StatusLabel).FirstOrDefault().Text, "Gigya initialized with domain: us1.gigya.com");

            app.Tap(LoginButton);
            app.WaitForElement(GigyaWebView);
            app.Screenshot("Login activated.");

            app.Tap(CancelButton);
            app.WaitForElement(LoginButton);
            app.Screenshot("Login cancelled.");
        }
        ```

        > [!TIP]
        > Leia mais sobre o UITests Framework e a automação da interface do usuário pelo [link a seguir](/appcenter/test-cloud/uitest/).

    - Crie um aplicativo iOS no app Center, crie uma nova execução de teste com um novo conjunto de dispositivos para executar o teste:

        ![novo Visual Studio app Center](walkthrough-images/visualstudio-appcenter-new.png)

        > [!TIP]
        > Saiba mais sobre o AppCenter Test Cloud pelo [link a seguir](/appcenter/test-cloud/).

    - Instalar a CLI do AppCenter

        ```bash
        npm install -g appcenter-cli
        ```

        > [!IMPORTANT]
        > Verifique se você tem o node v 6.3 ou posterior instalado

    - Execute o teste usando o comando a seguir. Verifique também se a linha de comando do AppCenter está conectada no momento.

        ```bash
        appcenter test run uitest --app "Mobile-Customer-Advisory-Team/SwiftBinding.iOS" --devices a7e7cb50 --app-path "Xamarin.SingleView.ipa" --test-series "master" --locale "en_US" --build-dir "Xamarin/Xamarin.SingleView.UITests/bin/Debug/"
        ```

    - Verifique o resultado. No portal do AppCenter, navegue até o **aplicativo > testar > execuções de teste**:

        ![resultado do UITest do Visual Studio AppCenter](walkthrough-images/visualstudio-appcenter-uitest-result.png)

        E selecione a execução de teste desejada e verifique o resultado:

        ![execuções do Visual Studio AppCenter UITest](walkthrough-images/visualstudio-appcenter-uitest-runs.png)

Você desenvolveu um aplicativo Xamarin. iOS básico que usa uma estrutura Swift nativa por meio de uma biblioteca de associação do Xamarin. iOS. O exemplo fornece uma maneira simplista de usar a estrutura selecionada e, no aplicativo real, será necessário expor mais APIs e preparar metadados para essas APIs. O script para gerar metadados simplificará as alterações futuras nas APIs de estrutura.

## <a name="related-links"></a>Links relacionados

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio para Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](../../../cross-platform/macios/binding/objective-sharpie/index.md)
- [Verificação de metadados de nitidez](../../../cross-platform/macios/binding/objective-sharpie/platform/verify.md)
- [Estrutura de objetivo de associação-C](../binding-objective-c/walkthrough.md)
- [SDK do iOS Gigya (estrutura Swift)](https://developers.gigya.com/display/GD/Swift+SDK)
- [Swift 5,1 de estabilidade da ABI](https://swift.org/blog/swift-5-1-released/)
- [NuGet SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automação do Xamarin UITest](/appcenter/test-cloud/uitest/)
- [Configuração do Xamarin. iOS UITest](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Test Cloud AppCenter](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Repositório de projetos de exemplo](https://github.com/alexeystrakh/xamarin-binding-swift-framework)