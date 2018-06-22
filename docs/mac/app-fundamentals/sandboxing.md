---
title: Um aplicativo Xamarin.Mac de modo seguro
description: Este artigo aborda um aplicativo Xamarin.Mac versão na loja de aplicativos de modo seguro. Ele abrange todos os elementos que entram no modo seguro, como diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a02d7639975de092b05f31bacedd6bde4c9392f9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787903"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Um aplicativo Xamarin.Mac de modo seguro

_Este artigo aborda um aplicativo Xamarin.Mac versão na loja de aplicativos de modo seguro. Ele abrange todos os elementos que entram no modo seguro, como diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel._

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem a mesma capacidade de um aplicativo de área restrita como faria ao trabalhar com Objective-C ou Swift.

[![Um exemplo de aplicativo em execução](sandboxing-images/intro01.png "um exemplo de aplicativo em execução")](sandboxing-images/intro01-large.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com modo seguro em um aplicativo Xamarin.Mac e todos os elementos que entram no modo seguro: diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` atributos usado para conectar as classes c# objetos Objective-C e a interface do usuário elementos.

## <a name="about-the-app-sandbox"></a>Sobre o aplicativo de proteção

A área de segurança de aplicativo fornece forte defesa contra danos que podem ser causado por um aplicativo mal-intencionado que está sendo executado em um Mac, limitando o acesso de um aplicativo tem aos recursos do sistema.

Um aplicativo não em modo seguro tem todos os direitos do usuário que está executando o aplicativo e pode acessar ou fazer qualquer coisa que o usuário pode. Se o aplicativo contém falhas de segurança (ou qualquer estrutura que está usando), um hacker pode potencialmente explorar essas falhas e usar o aplicativo para controlar o Mac que ele está em execução no.

Limitando o acesso aos recursos em uma base por aplicativo, um aplicativo em modo seguro fornece uma linha de defesa contra roubo, danos ou mal-intencionados por parte de um aplicativo em execução no computador do usuário.

A área de segurança do aplicativo é uma tecnologia de controle de acesso incorporada macOS (imposta no nível do kernel) que fornece uma estratégia dupla:

1. A área de segurança do aplicativo permite que o desenvolvedor descrevem _como_ um aplicativo irá interagir com o sistema operacional e, dessa forma, somente os direitos de acesso que são necessários para realizar o trabalho e não mais é concedido.
2. A área de segurança do aplicativo permite que o usuário conceder acesso ao sistema por meio de abrir diretamente e salvar caixas de diálogo, arraste e solte a operações e interações de outro usuário comum.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparando para implementar o aplicativo de proteção

Os elementos do aplicativo de proteção que serão discutidos em detalhes no artigo são da seguinte maneira:

- Diretórios de contêiner
- Direitos
- Permissões de determinado usuário
- Separação de privilégio
- Imposição de kernel

Depois de entender esses detalhes, você poderá criar um plano para a área restrita de aplicativo em seu aplicativo Xamarin.Mac a adoção.

Primeiro, você precisará determinar se o aplicativo é um bom candidato para o modo seguro (a maioria dos aplicativos são). Em seguida, você precisará resolver quaisquer incompatibilidades de API e determinar quais elementos da área de segurança do aplicativo que será necessária. Por fim, considere usar separação de privilégio para maximizar o nível de proteção do aplicativo.

Ao adotar a área de segurança do aplicativo, alguns locais de sistema de arquivos que o aplicativo usa serão diferentes. Particularmente, seu aplicativo terá um diretório de contêiner que será usado para arquivos de suporte de aplicativos, bancos de dados, caches e outros arquivos que não são documentos do usuário. MacOS e Xcode dão suporte para migrar esses tipos de arquivos de seus locais herdados para o contêiner.

## <a name="sandboxing-quick-start"></a>Início rápido do modo seguro

Nesta seção, vamos criar um aplicativo Xamarin.Mac simples que usa um modo de exibição da Web (que exige uma conexão de rede é restrito em modo seguro, a menos que solicitado especificamente) como um exemplo de Introdução com o aplicativo de proteção.

Verificaremos que o aplicativo está realmente em modo seguro e saber como solucionar problemas e resolver erros de aplicativo de proteção comuns.

### <a name="creating-the-xamarinmac-project"></a>Criando o projeto Xamarin.Mac

Vamos fazer o seguinte para criar nosso projeto de exemplo:

1. Inicie o Visual Studio para Mac e clique no **nova solução.** .
2. Do **novo projeto** caixa de diálogo, selecione **Mac** > **aplicativo** > **Cocoa aplicativo**: 

    [![Criando um novo aplicativo de Cocoa](sandboxing-images/sample01.png "criar um novo aplicativo Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Clique o **próximo** botão, digite `MacSandbox` para o nome do projeto e clique o **criar** botão: 

    [![Inserir o nome do aplicativo](sandboxing-images/sample02.png "digitando o nome do aplicativo")](sandboxing-images/sample02-large.png#lightbox)
4. No **solução preenchimento**, clique duas vezes o **Main.storyboard** arquivo para abri-lo para edição no Xcode: 

    [![Editando o storyboard principal](sandboxing-images/sample03.png "editando o storyboard principal")](sandboxing-images/sample03-large.png#lightbox)
5. Arraste um **exibição Web** para a janela de tamanho para preencher a área de conteúdo e defini-la para aumentar ou reduzir a janela: 

    [![Adicionando uma exibição da web](sandboxing-images/sample04.png "adicionando uma exibição da web")](sandboxing-images/sample04-large.png#lightbox)
6. Criar uma tomada para o modo de exibição da web chamado `webView`: 

    [![Criar uma nova loja](sandboxing-images/sample05.png "criar uma nova loja")](sandboxing-images/sample05-large.png#lightbox)
7. Retornar ao Visual Studio para Mac e clique duas vezes o **ViewController.cs** do arquivo no **solução preenchimento** para abri-lo para edição.
8. Adicione a seguinte instrução using: `using WebKit;`
9. Verifique o `ViewDidLoad` método aparência semelhante ao seguinte: 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. Salve as alterações.

Execute a você o aplicativo e certifique-se de que o site da Apple é exibido na janela da seguinte maneira:

[![Mostra um exemplo de aplicativo em execução](sandboxing-images/sample06.png "mostrando um exemplo de aplicativo em execução")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Autenticação e o aplicativo de provisionamento

Antes do aplicativo de proteção pode ser habilitado, é preciso primeiro provisionar e assine nosso aplicativo Xamarin.Mac.

Permitir que faça o seguinte:

1. Faça logon no Portal do desenvolvedor Apple: 

    [![Fazer logon no Portal do desenvolvedor Apple](sandboxing-images/sign01.png "fazer logon no Portal do desenvolvedor Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Selecione **certificados, identificadores e perfis**: 

    [![Selecionar certificados, identificadores e perfis](sandboxing-images/sign02.png "Selecionar certificados, identificadores e perfis")](sandboxing-images/sign02-large.png#lightbox)
3. Em **aplicativos Mac**, selecione **identificadores**: 

    [![Selecionando identificadores](sandboxing-images/sign03.png "selecionando identificadores")](sandboxing-images/sign03-large.png#lightbox)
4. Crie uma nova ID para o aplicativo: 

    [![Criar uma nova ID de aplicativo](sandboxing-images/sign04.png "criando uma nova ID de aplicativo")](sandboxing-images/sign04-large.png#lightbox)
5. Em **perfis de provisionamento**, selecione **desenvolvimento**: 

    [![Seleção de desenvolvimento](sandboxing-images/sign05.png "selecionando o desenvolvimento")](sandboxing-images/sign05-large.png#lightbox)
6. Criar um novo perfil e selecione **o desenvolvimento de aplicativos do Mac**: 

    [![Criando um novo perfil](sandboxing-images/sign06.png "criando um novo perfil")](sandboxing-images/sign06-large.png#lightbox)
7. Selecione a ID de aplicativo criado anteriormente: 

    [![Selecionar a ID do aplicativo](sandboxing-images/sign07.png "selecionando o ID do aplicativo")](sandboxing-images/sign07-large.png#lightbox)
8. Selecione os desenvolvedores para este perfil: 

    [![Adicionando desenvolvedores](sandboxing-images/sign08.png "adicionando desenvolvedores")](sandboxing-images/sign08-large.png#lightbox)
9. Selecione os computadores para este perfil: 

    [![Selecionar os computadores permitidos](sandboxing-images/sign09.png "selecionar os computadores permitidos")](sandboxing-images/sign09-large.png#lightbox)
10. Dê um nome de perfil: 

    [![Nomeando o perfil](sandboxing-images/sign10.png "dar um nome de perfil")](sandboxing-images/sign10-large.png#lightbox)
11. Clique o **feito** botão.

> [!IMPORTANT]
> Em alguns casos você precisará baixar o novo perfil de provisionamento do Portal do desenvolvedor da Apple diretamente e clique duas vezes nele para instalar. Talvez você precise interromper e reiniciar o Visual Studio para Mac antes de ser capaz de acessar o novo perfil.

Em seguida, precisamos carregar a nova ID do aplicativo e o perfil no computador de desenvolvimento. Vamos fazer o seguinte:

1. Inicie o Xcode e selecione **preferências** do **Xcode** menu: 

    ![Editando contas no Xcode](sandboxing-images/sign11.png "editando contas no Xcode")
2. Clique no **exibir detalhes...**  botão: 

    ![Clicar no botão Exibir detalhes](sandboxing-images/sign12.png "clicando no botão Exibir detalhes")
3. Clique no **atualização** botão (no canto inferior esquerdo).
4. Clique no **feito** botão.

Em seguida, precisamos selecionar a nova ID do aplicativo e perfil de provisionamento em nosso projeto Xamarin.Mac. Vamos fazer o seguinte:

1. No **solução preenchimento**, clique duas vezes o **Info. plist** arquivo para abri-lo para edição.
2. Certifique-se de que o **identificador de pacote** corresponde nosso ID de aplicativo criado anteriormente (exemplo: `com.appracatappra.MacSandbox`): 

    [![Editando o identificador de pacote](sandboxing-images/sign13.png "editando o identificador de pacote")](sandboxing-images/sign13-large.png#lightbox)
3. Em seguida, clique duas vezes o **Entitlements.plist** e verifique se o nosso **iCloud repositório de chave-valor** e **iCloud contêineres** todos correspondem nosso ID do aplicativo criado anteriormente (exemplo: `com.appracatappra.MacSandbox`): 

    [![Editar o arquivo Entitlements.plist](sandboxing-images/sign17.png "editando o arquivo Entitlements.plist")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações.
4. No **teclado de solução**, clique duas vezes no arquivo de projeto para abrir suas opções para editar:  

    ![Editign opções da solução](sandboxing-images/sign14.png "Editign opções da solução")
5. Selecione **assinatura Mac**, em seguida, verifique **assinar o pacote de aplicativo** e **assinar o pacote do instalador**. Em **perfil de provisionamento**, selecione aquele que criamos acima: 

    ![Definindo o perfil de provisionamento](sandboxing-images/sign15.png "definindo o perfil de provisionamento")
6. Clique o **feito** botão.

> [!IMPORTANT]
> Você terá que fechar e reiniciar o Visual Studio para Mac para obtê-lo para reconhecer a nova ID do aplicativo e perfil de provisionamento que foi instalado com o Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Solucionando problemas de provisionamento

Agora você deve tentar executar o aplicativo e certifique-se de que tudo está assinado e configurado corretamente. Se o aplicativo ainda é executado como antes, tudo o que é bom. Em caso de falha, você poderá receber uma caixa de diálogo como esta:

[![Um exemplo de provisionamento de caixa de diálogo de problema](sandboxing-images/sign16.png "um exemplo de provisionamento de caixa de diálogo do problema")](sandboxing-images/sign16-large.png#lightbox)

Aqui estão as causas mais comuns de provisionamento e problemas de assinatura:

- A ID do pacote de aplicativo não corresponde à ID de aplicativo do perfil selecionado.
- A ID do desenvolvedor não corresponde à ID de desenvolvedor do perfil selecionado.
- O UUID do Mac que está sendo testado não está registrado como parte do perfil selecionado.

No caso de um problema, corrija o problema no Portal do desenvolvedor Apple, atualize os perfis no Xcode e fazer uma compilação limpa no Visual Studio para Mac.

### <a name="enable-the-app-sandbox"></a>Habilitar a área de segurança do aplicativo

Você pode habilitar o aplicativo de proteção marcando a caixa de seleção em suas opções de projetos. Faça o seguinte:

1. No **solução preenchimento**, clique duas vezes o **Entitlements.plist** arquivo para abri-lo para edição.
2. Marque ambos **habilitar direitos** e **habilitar a área restrita do aplicativo**: 

    [![Direitos de edição e habilitar a área restrita](sandboxing-images/sign17.png "direitos de edição e habilitar a área restrita")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações.

Neste ponto, você habilitou o aplicativo de proteção, mas não forneceu o acesso de rede necessários para a exibição da Web. Se você executar o aplicativo agora, você deve obter uma janela em branco:

[![Mostrando o que está sendo bloqueado o acesso via web](sandboxing-images/sample08.png "mostrando o que está sendo bloqueado o acesso via web")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Verificando se o aplicativo está em modo seguro

Além do comportamento de bloqueio de recurso, há três maneiras principais de informar se um aplicativo Xamarin.Mac foi em modo seguro com êxito:

1. No localizador, verifique o conteúdo do `~/Library/Containers/` pasta - se o aplicativo está em modo seguro, haverá uma pasta chamada como identificador de pacote do aplicativo (exemplo: `com.appracatappra.MacSandbox`): 

    [![Abrir o pacote do aplicativo](sandboxing-images/sample09.png "abrindo o pacote do aplicativo")](sandboxing-images/sample09-large.png#lightbox)
2. O sistema vê o aplicativo como em modo seguro no Monitor de atividade:
    - Iniciar o Monitor de atividade (em `/Applications/Utilities`). 
    - Escolha **exibição** > **colunas** e certifique-se de que o **Sandbox** item de menu é marcado.
    - Certifique-se de que a coluna de área restrita lê `Yes` para seu aplicativo: 

    [![Verificando o aplicativo no Monitor de atividade](sandboxing-images/sample10.png "verificando o aplicativo no Monitor de atividade")](sandboxing-images/sample10-large.png#lightbox)
3. Verifique se os binários do aplicativo está em modo seguro:
    - Inicie o aplicativo de Terminal.
    - Navegue até os aplicativos `bin` directory.
    - Execute este comando: `codesign -dvvv --entitlements :- executable_path` (onde `executable_path` é o caminho para seu aplicativo): 

    [![Verificando o aplicativo na linha de comando](sandboxing-images/sample11.png "verificando o aplicativo na linha de comando")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Depurar um aplicativo de área restrita

O depurador se conecta ao Xamarin.Mac aplicativos por meio de TCP, o que significa que por padrão quando você habilita a área restrita, não é possível se conectar ao aplicativo, portanto, se você tentar executar o aplicativo sem as permissões adequadas habilitadas, você obterá um erro *"não é possível se conectar ao o depurador"*. 

[![Definindo as opções necessárias](sandboxing-images/debug01.png "definindo as opções necessárias")](sandboxing-images/debug01-large.png#lightbox)

O **permitir saída conexões de rede (cliente)** a permissão é necessária para o depurador, habilitar esta permite que a depuração normalmente. Desde que você não pode depurar sem ele, atualizamos o `CompileEntitlements` de destino para `msbuild` para adicionar automaticamente essa permissão para os direitos para qualquer aplicativo que está na área restrita para depuração somente compilações. Compilações de versão devem usar as autorizações especificadas no arquivo de direitos, sem modificações.

### <a name="resolving-an-app-sandbox-violation"></a>Resolver uma violação de área restrita do aplicativo

Uma violação de área restrita do aplicativo ocorre se um aplicativo tentar acessar um recurso que tem de Xamarin.Mac em modo seguro não serem explicitamente permitidos. Por exemplo, nossa exibição Web não conseguir exibir o site da Apple.

A origem mais comum de violações de área restrita do aplicativo ocorre quando as configurações de autorização especificadas no Visual Studio para Mac não correspondem aos requisitos do aplicativo. Faça novamente, em nosso exemplo, a Conexão de rede ausente direitos que mantêm o modo de exibição da Web do trabalho.

#### <a name="discovering-app-sandbox-violations"></a>Detectar violações de área restrita do aplicativo

Se você suspeitar de uma violação de área restrita do aplicativo está ocorrendo em seu aplicativo Xamarin.Mac, a maneira mais rápida para descobrir o problema é usando o **Console** aplicativo.

Faça o seguinte:

1. Compilar o aplicativo em questão e executá-lo do Visual Studio para Mac.
2. Abra o **Console** aplicativo (da `/Applications/Utilties/`).
3. Selecione **todas as mensagens** na barra lateral e digite `sandbox` na pesquisa: 

    [![Um exemplo de um problema de modo seguro no console do](sandboxing-images/resolve01.png "um exemplo de um problema de modo seguro no console do")](sandboxing-images/resolve01-large.png#lightbox)

Para nosso aplicativo de exemplo acima, você pode ver que o kernel está bloqueando o `network-outbound` tráfego devido o aplicativo de proteção, pois estamos não solicitou esse direito.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Corrigir violações com direitos de área restrita do aplicativo

Agora que já vimos como encontrar violações de modo seguro de aplicativo, vamos ver como eles podem ser resolvidos, ajustando os direitos do nosso aplicativo.

Faça o seguinte:

1. No **solução preenchimento**, clique duas vezes o **Entitlements.plist** arquivo para abri-lo para edição.
2. Sob o **direitos** seção, verifique o **permitir saída conexões de rede (cliente)** caixa de seleção: 

    [![Os direitos de edição](sandboxing-images/sign17.png "os direitos de edição")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações para o aplicativo.

Se, em seguida, podemos fazer acima para nosso aplicativo de exemplo compilar e executá-lo, o conteúdo da web agora será exibido como esperado.

## <a name="the-app-sandbox-in-depth"></a>A área de segurança de aplicativo detalhada

Os mecanismos de controle de acesso fornecidos pela área restrita de aplicativo são alguns e fáceis de entender. No entanto, a maneira que a área de segurança do aplicativo será adotada por cada aplicativo é exclusivo e com os requisitos do aplicativo.

Considerando sua melhor esforço para proteger seu aplicativo Xamarin.Mac exploração por códigos mal-intencionados, precisa haver apenas uma única vulnerabilidade em qualquer aplicativo (ou uma das estruturas ou bibliotecas consome) para assumir o controle de interações do aplicativo com o sistema.

A área de segurança do aplicativo foi projetada para evitar que o controle (ou limitar o dano pode causar), permitindo que você especifique interações de pretendido do aplicativo com o sistema. O sistema só concede acesso ao recurso que seu aplicativo precisa para realizar seu trabalho e nada mais.

Ao projetar o aplicativo de proteção, você está criando um cenário pior. Se o aplicativo comprometido por código mal-intencionado, ele é limitado a acessar apenas os arquivos e recursos na proteção do aplicativo.

### <a name="entitlements-and-system-resource-access"></a>Direitos de acesso ao sistema e recursos

Como vimos acima, um aplicativo de Xamarin.Mac que não tenha sido área restrita é concedido todos os direitos de acesso do usuário que está executando o aplicativo. Se comprometido por códigos mal-intencionados, um aplicativo protegido não pode agir como um agente para o comportamento hostil, com uma ampla variando potencial para causar prejuízo.

Habilitando o aplicativo de proteção, você remova todos deixando apenas um conjunto mínimo de privilégios, o que você e habilitar novamente em uma base somente necessidade usando os direitos do aplicativo Xamarin.Mac. 

Modificar recursos de área restrita do aplicativo do aplicativo editando seu **Entitlements.plist** arquivo e verificando ou selecionando os direitos necessários nas caixas de lista suspensa de editores:

[![Os direitos de edição](sandboxing-images/sign17.png "os direitos de edição")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Acesso de sistema de arquivos e diretórios de contêiner

Quando seu aplicativo Xamarin.Mac adota o aplicativo de proteção, ele tem acesso para os seguintes locais:

- **O diretório de contêiner do aplicativo** -na primeira execução, o sistema operacional cria um especial _diretório de contêiner_ onde ir todos os seus recursos, que somente ele possa acessar. O aplicativo terá acesso de leitura/gravação completa para este diretório.
- **Diretórios de contêiner de grupo do aplicativo** -seu aplicativo pode ter acesso a um ou mais _contêineres de grupo_ que são compartilhados entre aplicativos no mesmo grupo.
- **Arquivos especificados pelo usuário** -automaticamente o seu aplicativo obtém acesso a arquivos que são explicitamente aberto ou arrastado e solto sobre o aplicativo pelo usuário.
- **Itens relacionados ao** -com os direitos apropriados, seu aplicativo pode ter acesso a um arquivo com o mesmo nome mas com uma extensão diferente. Por exemplo, um documento que foi salvo como um `.txt` arquivo e um `.pdf`.
- **Diretórios temporários, ferramenta de linha de comando diretórios e locais específicos de mundo legível** -seu aplicativo tem vários níveis de acesso aos arquivos em outros locais bem definidos conforme especificado pelo sistema.

#### <a name="the-app-container-directory"></a>O diretório de aplicativo de contêiner

O diretório de contêiner de aplicativo do aplicativo um Xamarin.Mac tem as seguintes características:

- Ele está em um local ocultado no diretório da página inicial do usuário (geralmente `~Library/Containers`) e podem ser acessados com o `NSHomeDirectory` função (veja abaixo) dentro de seu aplicativo. Porque ele está no diretório inicial, cada usuário terá seu próprio contêiner para o aplicativo.
- O aplicativo terá acesso de leitura/gravação irrestrito para o diretório de contêiner e todas as suas subpastas e arquivos dentro dele.
- A maioria das APIs de localização do caminho do macOS é relativo ao contêiner do aplicativo. Por exemplo, o contêiner terá seu próprio **biblioteca** (acessados por meio de `NSLibraryDirectory`), **suporte a aplicativos** e **preferências** subdiretórios.
- macOS estabelece e impõe a conexão entre o e o aplicativo e o seu contêiner por meio de assinatura de código. Ainda é outro aplicativo tenta falsificar o aplicativo usando sua **identificador de pacote**, ele não poderá acessar o contêiner devido a assinatura de código.
- O contêiner não é para arquivos gerado pelo usuário. Em vez disso, é para arquivos que o aplicativo usa, como bancos de dados, caches ou outros tipos específicos de dados.
- Para _caixa de sapatos_ tipos de aplicativos (como o aplicativo de fotos da Apple), o conteúdo do usuário passará para o contêiner.

> [!IMPORTANT]
> Infelizmente, Xamarin.Mac não tem 100% de cobertura de API ainda (ao contrário de xamarin), como resultado o `NSHomeDirectory` API não foi mapeada na versão atual do Xamarin.Mac.

Como solução temporária, você pode usar o código a seguir:

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>O diretório de contêiner de grupo do aplicativo

Começando com Mac macOS 10.7.5 (e posterior), um aplicativo pode usar o `com.apple.security.application-groups` direito para acessar um contêiner compartilhado que é comuns a todos os aplicativos no grupo. Você pode usar esse contêiner compartilhada para conteúdo de usuário não oposto como banco de dados ou outros tipos de arquivos de suporte (como caches).

Os contêineres de grupo são adicionados automaticamente para área restrita do contêiner da cada aplicativo (se eles fazem parte de um grupo) e são armazenados no `~/Library/Group Containers/<application-group-id>`. A ID do grupo _deve_ começam com sua ID da equipe de desenvolvimento e um período, por exemplo:

```plist
<team-id>.com.company.<group-name>
```

Para obter mais informações, consulte da Apple [adicionar um aplicativo para um grupo de aplicativos](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) na [referência de chave de autorização](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Acesso a sistema Powerbox e arquivos fora do contêiner do aplicativo

Um aplicativo na área restrita Xamarin.Mac pode acessar locais de sistema de arquivos fora de seu contêiner das seguintes maneiras:

- Sob a orientação específica do usuário (por meio de abrir e salvar caixas de diálogo ou outros métodos, como arrastar e soltar).
- Usando os direitos para locais de sistema de arquivo específico (como `/bin` ou `/usr/lib`).
* Quando o local do sistema de arquivos está em determinadas pastas que são world legível (como compartilhamento).

_Powerbox_ é o macOS tecnologia de segurança que interage com o usuário para expandir os direitos de acesso de arquivo do aplicativo Xamarin.Mac em modo seguro. Powerbox não tem nenhuma API, mas é ativado de modo transparente quando o aplicativo chama uma `NSOpenPanel` ou `NSSavePanel`. Acesso de Powerbox está habilitado por meio de direitos que você definiu para o seu aplicativo Xamarin.Mac.

Quando um aplicativo de área restrita exibe um aberto ou caixa de diálogo Salvar, a janela é apresentada pelo Powerbox (e não AppKit) e, portanto, tem acesso a qualquer arquivo ou pasta que o usuário tem acesso.

Quando um usuário seleciona um arquivo ou diretório de abrir ou caixa de diálogo Salvar (ou arrasta-o para o ícone do aplicativo), Powerbox adiciona o caminho associado à proteção do aplicativo.

Além disso, o sistema automaticamente permite que o seguinte para um aplicativo em modo seguro:

- Conexão a um método de entrada do sistema.
- Chamar serviços selecionados pelo usuário da **serviços** menu (somente para serviços marcados como _seguro para aplicativos de área restrita_ pelo provedor de serviço).
- Escolha arquivos abertos pelo usuário a partir de **abrir recente** menu.
- Use copiar e colar entre outros aplicativos.
- Ler arquivos do mundo legível locais a seguir:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Ler e gravar arquivos nos diretórios criados pelo `NSTemporaryDirectory`.

Seja o padrão, arquivos aberto ou salvo por um aplicativo em modo seguro de Xamarin.Mac permanecem acessíveis até que o aplicativo seja encerrado (a menos que o arquivo estava aberto quando o aplicativo é fechado). Arquivos abertos serão automaticamente restaurados de proteção do aplicativo através do recurso de retomar macOS na próxima vez que o aplicativo é iniciado.

Para fornecer persistência a arquivos localizados fora do contêiner de um App Xamarin.Mac, use indicadores com escopo de segurança (veja abaixo).

#### <a name="related-items"></a>Itens relacionados

A área de segurança do aplicativo permite que um aplicativo acessar itens relacionados que têm o mesmo nome, mas extensões diferentes. O recurso tem duas partes: a) uma lista de extensões relacionadas no aplicativo do `Info.plst` arquivo b) o código para informar a área restrita que o aplicativo fará com que esses arquivos.

Há dois cenários em que isso faz sentido:

1. O aplicativo precisa ser capaz de salvar uma versão diferente do arquivo (com uma nova extensão). Por exemplo, exportar um `.txt` o arquivo para um `.pdf` arquivo. Para lidar com essa situação, você deve usar um `NSFileCoordinator` para acessar o arquivo. Você chamará o `WillMove(fromURL, toURL)` método primeiro, mova o arquivo para a nova extensão e, em seguida, chame `ItemMoved(fromURL, toURL)`.
2. O aplicativo precisa para abrir um arquivo principal com uma extensão e vários arquivos de suporte com extensões diferentes. Por exemplo, um filme e um arquivo de subtítulo. Use um `NSFilePresenter` para acessar o arquivo secundário. Forneça o arquivo principal para o `PrimaryPresentedItemURL` propriedade e o arquivo secundário para o `PresentedItemURL` propriedade. Quando o arquivo principal for aberto, chame o `AddFilePresenter` método o `NSFileCoordinator` classe para registrar o arquivo secundário.

Em ambos os cenários, o aplicativo **Info. plist** arquivo deve declarar os tipos de documento que pode abrir o aplicativo. Para qualquer tipo de arquivo, adicione o `NSIsRelatedItemType` (com um valor de `YES`) para sua entrada na `CFBundleDocumentTypes` matriz.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Abrir e salvar o comportamento da caixa de diálogo com aplicativos de área restrita

Os seguintes limites são colocados no `NSOpenPanel` e `NSSavePanel` ao chamá-los de um aplicativo em modo seguro de Xamarin.Mac:

- Você não pode chamar programaticamente o **Okey** botão.
- Programaticamente, você não pode alterar a seleção do usuário em um `NSOpenSavePanelDelegate`.

Além disso, as seguintes modificações de herança estão em vigor:

- **Aplicativo não em modo seguro** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Indicadores com escopo de segurança e acesso ao recurso persistente

Como mencionado acima, um aplicativo de área restrita Xamarin.Mac pode acessar um arquivo ou recurso fora do seu contêiner por meio da interação direta do usuário (como fornecido pela PowerBox). No entanto, acesso a esses recursos não automaticamente persistem inicia de aplicativo ou sistema é reiniciado.

Usando _Security-Scoped indicadores_, um aplicativo de área restrita Xamarin.Mac pode preservar a intenção do usuário e manter o acesso fornecido recursos depois que um aplicativo reiniciar.

#### <a name="security-scoped-bookmark-types"></a>Tipos de indicadores com escopo de segurança

Ao trabalhar com indicadores de Security-Scoped e persistente de acesso aos recursos, há dois casos de uso sistine:

- **Um indicador de App-Scoped fornece acesso persistente para um arquivo especificado pelo usuário ou uma pasta.** 

    Por exemplo, se o aplicativo na área restrita Xamarin.Mac permite usar para abrir um documento externo para edição (usando um `NSOpenPanel`), o aplicativo pode criar um indicador de App-Scoped para que ele possa acessar o mesmo arquivo novamente no futuro.
- **Um indicador de Document-Scoped fornece um acesso persistente de documento específico para um arquivo sub.** 

Por exemplo, um aplicativo de edição de vídeo que cria um arquivo de projeto que tem acesso a imagens individuais, videoclipes e arquivos de som que posteriormente serão combinados em um único filme.

Quando o usuário importa um arquivo de recurso para o projeto (por meio de um `NSOpenPanel`), o aplicativo cria um indicador de Document-Scoped para o item que está armazenado no projeto, para que o arquivo esteja sempre acessível para o aplicativo.

Um indicador de Document-Scoped podem ser resolvido por qualquer aplicativo que pode abrir os dados de indicador e o próprio documento. Isso dá suporte a portabilidade, permitindo que o usuário enviar os arquivos de projeto para outro usuário e ter todos os indicadores funcionam bem para eles.

> [!IMPORTANT]
> Um indicador de Document-Scoped pode _somente_ apontar para um único arquivo e não uma pasta e arquivo não pode estar em um local usado pelo sistema (como `/private` ou `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Usando indicadores com escopo de segurança

Usando o tipo de indicador Security-Scoped, requer que você execute as seguintes etapas:

1. **Definir os direitos apropriados no aplicativo do Xamarin.Mac que precisa usar indicadores Security-Scoped** -For App-Scoped indicadores, defina o `com.apple.security.files.bookmarks.app-scope` chave de autorização para `true`. Para marcadores Document-Scoped, defina o `com.apple.security.files.bookmarks.document-scope` chave de autorização para `true`.
2. **Criar um indicador de Security-Scoped** -isso será feito para um arquivo ou pasta que o usuário forneceu acesso ao (via `NSOpenPanel` por exemplo), que o aplicativo precisará acessar persistente. Use o `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` método o `NSUrl` classe para criar o indicador.
3. **Resolver o indicador Security-Scoped** - quando o aplicativo precisa acessar o recurso novamente (por exemplo, após a reinicialização) será necessário resolver o indicador para uma URL com escopo de segurança. Use o `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` método o `NSUrl` classe para resolver o indicador.
4. **Notificar explicitamente o sistema que você deseja acessar o arquivo da URL Security-Scoped** - esta etapa precisa ser feita imediatamente depois de obter a URL de Security-Scoped acima ou, quando você desejar recuperar o acesso ao recurso depois de ter liberada à medida seu acesso a ele. Chamar o `StartAccessingSecurityScopedResource ()` método o `NSUrl` classe para começar a acessar uma URL de Security-Scoped.
5. **Notificar explicitamente o sistema que são feitas ao acessar o arquivo da URL Security-Scoped** -assim que possível, você deve informar o sistema quando o aplicativo não precisa acessar o arquivo (por exemplo, se o usuário o feche). Chamar o `StopAccessingSecurityScopedResource ()` método o `NSUrl` classe para interromper o acesso a uma URL de Security-Scoped.

Depois de liberar o acesso a um recurso, você precisará retornar para a etapa 4 novamente para restabelecer o acesso. Se o aplicativo Xamarin.Mac for reiniciado, você deve retornar à etapa 3 e resolver novamente o indicador.

> [!IMPORTANT]
> Falha ao liberar o acesso aos recursos da URL Security-Scoped fará com que um aplicativo Xamarin.Mac apresentam vazamento de recursos do Kernel. Como resultado, o aplicativo não poderá adicionar locais de sistema de arquivos a seu contêiner até que ele seja reiniciado.

### <a name="the-app-sandbox-and-code-signing"></a>O aplicativo de proteção e a assinatura de código

Depois de habilitar a área de segurança do aplicativo e habilitar os requisitos específicos para seu aplicativo Xamarin.Mac (por meio de direitos), você deve assinar código do projeto para a área restrita entrem em vigor. Você deve executar a assinatura porque os direitos necessários para a área de segurança do aplicativo são vinculados a assinatura do aplicativo de código. 

macOS impõe um vínculo entre o contêiner do aplicativo e sua assinatura de código, dessa forma, que nenhum outro aplicativo pode acessar o contêiner, mesmo se ele está falsificando os ID do pacote de aplicativos Esse mecanismo funciona da seguinte maneira:

1. Quando o sistema cria o contêiner do aplicativo, ele define um _lista de controle de acesso_ (ACL) no contêiner. A entrada de controle de acesso inicial na lista contém o aplicativo _designado requisito_ (DR), que descreve como futuras versões do aplicativo pode ser reconhecida (quando ele foi atualizado).
2. Cada vez que um aplicativo com a mesma ID de pacote é iniciado, o sistema verifica se a assinatura de código do aplicativo corresponde designado requisitos especificados em uma das entradas de ACL do contêiner. Se o sistema não encontrar uma correspondência, ela impedirá que o aplicativo Iniciar.

Code Signing funciona das seguintes maneiras:

1. Antes de criar o projeto Xamarin.Mac, obtenha um certificado de desenvolvimento, um certificado de distribuição e um certificado de identificação do desenvolvedor do Portal do desenvolvedor da Apple.
2. Quando o aplicativo Xamarin.Mac distribui o Mac App Store, ele está assinado com uma assinatura de código da Apple.

Quando o teste e depuração, você usará uma versão do aplicativo Xamarin.Mac assinado (que será usado para criar o contêiner de aplicativo). Posteriormente, se você deseja testar ou instale a versão da Apple App Store, será assinado com a assinatura da Apple e falhará ao iniciar (desde que ele não tem a mesma assinatura de código como o contêiner do aplicativo original). Nessa situação, você obterá um relatório de erro semelhante à seguinte:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Para corrigir isso, você precisará ajustar a entrada ACL para apontar para a versão do aplicativo assinado do Apple.

Para obter mais informações sobre como criar e baixar os perfis de provisionamento necessárias de modo seguro, consulte o [autenticação e o aplicativo de provisionamento](#Signing_and_Provisioning_the_App) seção acima.

#### <a name="adjusting-the-acl-entry"></a>Ajustando a entrada ACL

Para permitir que a versão assinada do Apple do aplicativo para executar Xamarin.Mac, faça o seguinte:

1. Abra o aplicativo de Terminal (em `/Applications/Utilities`).
2. Abra uma janela de localizador para a versão assinada do Apple do aplicativo Xamarin.Mac.
3. Tipo `asctl container acl add -file ` na janela do Terminal.
4. Arraste o ícone do aplicativo Xamarin.Mac da janela do localizador e solte-o na janela de Terminal.
5. O caminho completo para o arquivo será adicionado ao comando no Terminal.
6. Pressione **Enter** para executar o comando.

A ACL do contêiner contém agora os requisitos de códigos designado para ambas as versões do aplicativo Xamarin.Mac e macOS agora permitirá que a versão ser executado.

#### <a name="display-a-list-of-acl-code-requirements"></a>Exibir uma lista dos requisitos de código ACL

Você pode exibir uma lista dos requisitos de código na ACL do contêiner, fazendo o seguinte:

1. Abra o aplicativo de Terminal (em `/Applications/Utilities`).
2. Digite `asctl container acl list -bundle <container-name>` no terminal integrado.
3. Pressione **Enter** para executar o comando.

O `<container-name>` geralmente é o identificador de pacote para o aplicativo Xamarin.Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Criando um aplicativo de Xamarin.Mac para o aplicativo de proteção

Há um fluxo de trabalho comuns que deve ser seguido durante a criação de um aplicativo de Xamarin.Mac para o aplicativo de proteção. Dito isso, as especificações da implementação de modo seguro em um aplicativo serão exclusivos para a funcionalidade do aplicativo especificado.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Seis etapas para adotar o aplicativo de proteção

Criando um aplicativo de Xamarin.Mac para a área restrita de aplicativo normalmente consiste das seguintes etapas:

1. Determine se o aplicativo é adequado para a área restrita.
2. Crie uma estratégia de desenvolvimento e distribuição.
3. Resolva quaisquer incompatibilidades de API.
4. Aplica os direitos de área restrita de aplicativo necessária para o projeto Xamarin.Mac.
5. Adicione a separação de privilégio usando XPC.
6. Implemente uma estratégia de migração.

> [!IMPORTANT]
> Você deve colocar somente não executável principal no pacote de aplicativos, mas também todos os auxiliares incluídos ferramenta no pacote ou aplicativo. Isso é necessário para qualquer aplicativo distribuído de Mac App Store e, se possível, deve ser feito para qualquer outra forma de distribuição de aplicativos.

Para obter uma lista de todos os binários do executável no pacote do aplicativo um Xamarin.Mac, digite o seguinte comando no Terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Onde `[Your-App-Bundle]` é o nome e o caminho para o pacote do aplicativo.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinar se um aplicativo Xamarin.Mac é adequado para a área restrita

A maioria dos aplicativos de Xamarin.Mac são totalmente compatíveis com o aplicativo de proteção e, portanto, adequado para a área restrita. Se o aplicativo exigir um comportamento que não permite a proteção do aplicativo, você deve considerar uma abordagem alternativa.

Se seu aplicativo requer um dos seguintes comportamentos, ele é incompatível com o aplicativo de proteção:

- **Serviços de autorização** -com a área de segurança de aplicativo, você não pode trabalhar com as funções descritas [autorização serviços C referência](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **APIs de acessibilidade** -não é possível aplicativos de área restrita auxiliares, como leitores de tela ou aplicativos que controlam a outros aplicativos.
- **Enviar eventos Apple para aplicativos arbitrários** -se o aplicativo requer o envio de eventos da Apple para um aplicativo desconhecido, arbitrário, ele não pode ser em modo seguro. Para obter uma lista conhecida de aplicativos de chamada, o aplicativo ainda pode ser em modo seguro e os direitos serão necessário incluir a lista de aplicativos de chamada.
- **Enviar dicionários de informações do usuário em notificações distribuídas para outras tarefas** -com a área de segurança de aplicativo, você não pode incluir um `userInfo` dicionário ao lançar uma `NSDistributedNotificationCenter` objeto para outras tarefas de mensagens.
- **Carregar extensões de Kernel** -o carregamento de extensões de Kernel é proibido pela área de segurança do aplicativo.
- **Simulando a entrada de usuário em aberto e caixas de diálogo Salvar** - programaticamente manipulando abrir ou salvar caixas de diálogo para simular ou altere a entrada do usuário é proibido pela área de segurança do aplicativo.
- **Acessando ou preferências de configuração em outros aplicativos** -manipular as configurações de outros aplicativos é proibido pela área de segurança do aplicativo.
- **Configurar definições de rede** -manipular as configurações de rede é proibida pela área de segurança do aplicativo.
- **Finalizando a outros aplicativos** -a área restrita de aplicativo proíbe usando `NSRunningApplication` para encerrar a outros aplicativos.

### <a name="resolving-api-incompatibilities"></a>Resolver as incompatibilidades de API

Ao criar um aplicativo de Xamarin.Mac para o aplicativo de proteção, você pode encontrar incompatibilidades com o uso de algumas APIs macOS.

Aqui estão alguns problemas comuns e que você pode fazer para resolvê-los:

- **Abrir, salvar e controle de documentos** - se você estiver gerenciando documentos usando qualquer tecnologia que `NSDocument`, você deverá alternar para ele devido o suporte interno para o aplicativo de proteção. `NSDocument` funciona com PowerBox e oferece suporte para manter os documentos dentro de sua área restrita se o usuário move-los no localizador automaticamente.
- **Manter o acesso aos recursos do sistema de arquivo** - se a Xamarin.Mac aplicativo depende persistente acesso aos recursos fora do respectivo contêiner, use indicadores com escopo de segurança para manter o acesso.
- **Criar um Item de logon para um aplicativo** -com a área de segurança de aplicativo, você não pode criar um logon usando o item `LSSharedFileList` nem que você pode manipular o estado dos serviços de inicialização usando `LSRegisterURL`. Use o `SMLoginItemSetEnabled` funcionar conforme descrito em maçãs [adicionando itens de logon usando a estrutura de gerenciamento de serviço](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentação.
- **Acessando dados do usuário** - se você estiver usando funções POSIX como `getpwuid` para obter o diretório base do usuário dos serviços de diretório, considere o uso de símbolos Cocoa ou essencial como `NSHomeDirectory`.
- **Acessando as preferências de outros aplicativos** – porque a área de segurança do aplicativo direciona APIs de localização de caminho para o contêiner do aplicativo, modificando preferências ocorrer dentro do contêiner e acessar outro preferências de aplicativos no não permitido. 
- **Usando o vídeo incorporado do HTML5 em modos de exibição Web** -se o aplicativo Xamarin.Mac usa WebKit para reproduzir vídeos inseridos do HTML5, você também deve vincular o aplicativo de acordo com a estrutura de AV Foundation. A área de segurança do aplicativo impedirá CoreMedia play esses vídeos caso contrário.

### <a name="applying-required-app-sandbox-entitlements"></a>Aplicação de direitos do aplicativo de proteção necessários

Você precisa editar os direitos para qualquer aplicativo Xamarin.Mac que você deseja executar na caixa de proteção do aplicativo e verificar o **habilitar modo seguro de aplicativo** caixa de seleção.

Com base na funcionalidade do aplicativo, você talvez precise habilitar outros direitos acessar recursos do sistema operacional ou recursos. Portanto, funciona de modo seguro de aplicativo melhor quando você minimizar os direitos que você solicitar o mínimo necessário para executar seu aplicativo apenas aleatoriamente habilitar direitos.

Para determinar quais direitos requer um aplicativo de Xamarin.Mac, faça o seguinte:

1. Habilitar a área de segurança do aplicativo e executar o aplicativo Xamarin.Mac.
2. Execute os recursos do aplicativo.
3. Abra o aplicativo de Console (disponível no `/Applications/Utilities`) e procure `sandboxd` violações a **todas as mensagens de** log.
4. Para cada `sandboxd` violação, resolva o problema usando o contêiner do aplicativo em vez de outros locais de sistema de arquivos ou aplicar direitos de área restrita do aplicativo para habilitar o acesso a recursos restritos do sistema operacional.
5. Execute novamente e testar todos os recursos de aplicativo Xamarin.Mac novamente.
6. Repita até que todos os `sandboxd` violações foram resolvidas.

### <a name="add-privilege-separation-using-xpc"></a>Adicionar a separação de privilégio usando XPC

Ao desenvolver um aplicativo Xamarin.Mac para o aplicativo de proteção, examinar os comportamentos do aplicativo em termos de acesso e os privilégios e considere a possibilidade de separar operações de alto risco em seus próprios serviços XPC.

Para obter mais informações, consulte da Apple [criando serviços de XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) e [guia de programação de serviços e Daemons](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implementar uma estratégia de migração

Se você estiver liberando uma nova versão de um aplicativo de Xamarin.Mac não era anteriormente em modo seguro em modo seguro, você precisará garantir que os usuários atuais tenham um caminho suave de atualização. 

 Para obter detalhes sobre como implementar um manifesto de migração do contêiner, leia da Apple [migrando um aplicativo para uma área restrita](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentação.

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de modo seguro um aplicativo Xamarin.Mac. Primeiro, criamos um aplicativo de Xamarin.Mac simplesmente para mostrar os fundamentos da área de segurança do aplicativo. Em seguida, mostramos como resolver violações de área restrita. Em seguida, usamos um profundo observar o aplicativo de proteção e por fim, analisamos criando um aplicativo de Xamarin.Mac para o aplicativo de proteção.



## <a name="related-links"></a>Links relacionados

- [Publicando na App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Sobre o aplicativo de proteção](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemas comuns de modo seguro de aplicativo](https://developer.apple.com/library/content/qa/qa1773/_index.html)
