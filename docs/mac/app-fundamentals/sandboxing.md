---
title: Área restrita um aplicativo xamarin. Mac
description: Este artigo aborda o modo seguro de um aplicativo xamarin. Mac para lançar na App Store. Ele abrange todos os elementos que entram no modo seguro, como diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e38ca07aeef1cbd8e121421ebcbad2207a1bb823
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865980"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Área restrita um aplicativo xamarin. Mac

_Este artigo aborda o modo seguro de um aplicativo xamarin. Mac para lançar na App Store. Ele abrange todos os elementos que entram no modo seguro, como diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel._

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem a mesma capacidade de um aplicativo de área restrita, como faria ao trabalhar com Objective-C ou Swift.

[![Um exemplo de aplicativo em execução](sandboxing-images/intro01.png "um exemplo de aplicativo em execução")](sandboxing-images/intro01-large.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com áreas de segurança em um aplicativo xamarin. Mac e todos os elementos que entram no modo seguro: os diretórios de contêiner, direitos, permissões de usuário determinado, separação de privilégios e imposição de kernel. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` atributos usado para conectar suas classes de c# a objetos de Objective-C e da interface do usuário elementos.

## <a name="about-the-app-sandbox"></a>Sobre a área restrita de aplicativo

A área restrita de aplicativo fornece uma forte defesa contra danos que podem ser causado por um aplicativo mal-intencionado que está sendo executado em um Mac, limitando o acesso que um aplicativo tem aos recursos do sistema.

Um aplicativo não área restrita e tem todos os direitos do usuário que está executando o aplicativo e pode acessar ou fazer qualquer coisa que o usuário pode. Se o aplicativo contiver brechas de segurança (ou qualquer estrutura que ele está usando), um hacker pode potencialmente explorar esses pontos fracos e usar o aplicativo para assumir o controle de que ele está em execução no Mac.

Limitando o acesso aos recursos em uma base por aplicativo, um aplicativo em área restrita fornece uma linha de defesa contra roubo, corrompidos ou mal-intencionados por parte de um aplicativo em execução no computador do usuário.

A área restrita de aplicativo é uma tecnologia de controle de acesso incorporada ao macOS (impostas no nível do kernel) que oferece uma estratégia de dupla:

1. A área restrita de aplicativo permite que o desenvolvedor descrever _como_ um aplicativo irá interagir com o sistema operacional e, dessa forma, ele recebe apenas os direitos de acesso que são necessários para realizar o trabalho e nada mais.
2. A área restrita de aplicativo permite que o usuário conceder mais acesso ao sistema por meio de abrir o perfeitamente e salvar as caixas de diálogo, arraste e solte as operações e interações do usuário de outros, comuns.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparando para implementar a área restrita de aplicativo

Os elementos da área de segurança aplicativo que serão discutidos em detalhes no artigo são da seguinte maneira:

- Diretórios de contêiner
- Direitos
- Permissões de usuário determinado
- Separação de privilégio
- Imposição de kernel

Depois de entender esses detalhes, você poderá criar um plano para adotar a área restrita de aplicativo em seu aplicativo xamarin. Mac.

Primeiro, você precisará determinar se o aplicativo é um bom candidato para a área restrita (a maioria dos aplicativos são). Em seguida, você precisará resolver quaisquer incompatibilidades de API e determinar quais elementos da área de segurança de aplicativo exigirá. Por fim, examine o uso de separação de privilégio para maximizar o nível de proteção do aplicativo.

Ao adotar a área restrita de aplicativo, alguns locais de sistema de arquivos que seu aplicativo usa serão diferentes. Particularmente, seu aplicativo terá um diretório de contêiner que será usado para arquivos de suporte do aplicativo, os bancos de dados, caches e outros arquivos que não são documentos do usuário. MacOS e o Xcode fornecem suporte para migrar esses tipos de arquivos de seus locais herdados para o contêiner.

## <a name="sandboxing-quick-start"></a>Início rápido da área restrita

Nesta seção, vamos criar um aplicativo xamarin. Mac simples que usa uma exibição da Web (que exige uma conexão de rede que é restrito em modo seguro, a menos que solicitado especificamente) como um exemplo de como começar a área restrita de aplicativo.

Verificaremos se o aplicativo é, na verdade, na área restrita e saiba como solucionar problemas e resolver erros comuns de área restrita de aplicativo.

### <a name="creating-the-xamarinmac-project"></a>Criando o projeto xamarin. Mac

Vamos fazer o seguinte para criar nosso projeto de exemplo:

1. Inicie o Visual Studio para Mac e clique no **nova solução...** .
2. Dos **novo projeto** caixa de diálogo, selecione **Mac** > **aplicativo** > **aplicativo Cocoa**: 

    [![Criando um novo aplicativo Cocoa](sandboxing-images/sample01.png "criando um novo aplicativo Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Clique o **próxima** botão, digite `MacSandbox` para o nome do projeto e clique o **criar** botão: 

    [![Inserir o nome do aplicativo](sandboxing-images/sample02.png "inserindo o nome do aplicativo")](sandboxing-images/sample02-large.png#lightbox)
4. No **painel de soluções**, clique duas vezes o **Main. Storyboard** arquivo para abri-lo para edição no Xcode: 

    [![Editando o storyboard principal](sandboxing-images/sample03.png "editando o storyboard principal")](sandboxing-images/sample03-large.png#lightbox)
5. Arraste uma **exibição da Web** para a janela, dimensione-o para preencher a área de conteúdo e defini-la para aumentar e reduzir com a janela: 

    [![Adicionando uma exibição da web](sandboxing-images/sample04.png "adicionando uma exibição da web")](sandboxing-images/sample04-large.png#lightbox)
6. Criar uma saída para o modo de exibição da web chamado `webView`: 

    [![Criando uma novo tomada](sandboxing-images/sample05.png "criando uma nova saída")](sandboxing-images/sample05-large.png#lightbox)
7. Retorne ao Visual Studio para Mac e clique duas vezes o **ViewController.cs** arquivo na **painel de soluções** para abri-lo para edição.
8. Adicione a seguinte instrução using: `using WebKit;`
9. Verifique o `ViewDidLoad` método são semelhantes ao seguinte: 

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Salve as alterações.

Execute a você o aplicativo e certifique-se de que o site da Apple é exibido na janela da seguinte maneira:

[![Mostrando um exemplo de aplicativo em execução](sandboxing-images/sample06.png "mostrando um exemplo de aplicativo em execução")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Assinatura e o aplicativo de provisionamento

Antes, podemos habilitar a área restrita de aplicativo, primeiro precisamos provisionar e assinar nosso aplicativo xamarin. Mac.

Permitir que faça o seguinte:

1. Faça logon no Portal do desenvolvedor da Apple: 

    [![Logon no Portal do desenvolvedor da Apple](sandboxing-images/sign01.png "fazendo logon no Portal do desenvolvedor da Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Selecione **certificados, identificadores e perfis**: 

    [![Selecionar certificados, identificadores e perfis](sandboxing-images/sign02.png "Selecionar certificados, identificadores e perfis")](sandboxing-images/sign02-large.png#lightbox)
3. Sob **aplicativos Mac**, selecione **identificadores**: 

    [![Selecionando identificadores](sandboxing-images/sign03.png "selecionando identificadores")](sandboxing-images/sign03-large.png#lightbox)
4. Crie uma nova ID para o aplicativo: 

    [![Criando uma nova ID do aplicativo](sandboxing-images/sign04.png "criando uma nova ID do aplicativo")](sandboxing-images/sign04-large.png#lightbox)
5. Sob **perfis de provisionamento**, selecione **desenvolvimento**: 

    [![Seleção de desenvolvimento](sandboxing-images/sign05.png "selecionando desenvolvimento")](sandboxing-images/sign05-large.png#lightbox)
6. Criar um novo perfil e selecione **desenvolvimento de aplicativos do Mac**: 

    [![Criando um novo perfil](sandboxing-images/sign06.png "criando um novo perfil")](sandboxing-images/sign06-large.png#lightbox)
7. Selecione a ID do aplicativo que criamos acima: 

    [![Selecionando a ID do aplicativo](sandboxing-images/sign07.png "selecionando a ID do aplicativo")](sandboxing-images/sign07-large.png#lightbox)
8. Selecione os desenvolvedores para este perfil: 

    [![Os desenvolvedores adicionando](sandboxing-images/sign08.png "adicionando desenvolvedores")](sandboxing-images/sign08-large.png#lightbox)
9. Selecione os computadores para este perfil: 

    [![Selecionando os computadores permitidos](sandboxing-images/sign09.png "selecionando os computadores permitidos")](sandboxing-images/sign09-large.png#lightbox)
10. Dê um nome de perfil: 

    [![Dando um nome de perfil](sandboxing-images/sign10.png "dando um nome de perfil")](sandboxing-images/sign10-large.png#lightbox)
11. Clique o **feito** botão.

> [!IMPORTANT]
> Em alguns casos, você precisará baixar o novo perfil de provisionamento do Portal do desenvolvedor da Apple diretamente e clique duas vezes nele para instalar. Você talvez também precise parar e reiniciar o Visual Studio para Mac antes de ser capaz de acessar o novo perfil.

Em seguida, precisamos carregar a nova ID do aplicativo e o perfil na máquina de desenvolvimento. Vamos fazer o seguinte:

1. Inicie o Xcode e selecione **preferências** da **Xcode** menu: 

    ![Editando contas no Xcode](sandboxing-images/sign11.png "editando contas no Xcode")
2. Clique no **exibir detalhes...**  botão: 

    ![Clicar no botão Exibir detalhes](sandboxing-images/sign12.png "clicando no botão Exibir detalhes")
3. Clique no **Refresh** botão (no canto inferior esquerdo).
4. Clique no **feito** botão.

Em seguida, precisamos selecionar o novo ID do aplicativo e o perfil de provisionamento em nosso projeto xamarin. Mac. Vamos fazer o seguinte:

1. No **painel de soluções**, clique duas vezes o **Info. plist** arquivo para abri-lo para edição.
2. Certifique-se de que o **identificador de pacote** corresponde à nossa ID de aplicativo que criamos acima (exemplo: `com.appracatappra.MacSandbox`): 

    [![O identificador de pacote de edição](sandboxing-images/sign13.png "editando o identificador de pacote")](sandboxing-images/sign13-large.png#lightbox)
3. Em seguida, clique duas vezes o **Entitlements. plist** do arquivo e certifique-se de nosso **iCloud Store de chave-valor** e o **contêineres do iCloud** todos corresponder à nossa ID de aplicativo que criamos acima (exemplo: `com.appracatappra.MacSandbox`): 

    [![Editando o arquivo Entitlements. plist](sandboxing-images/sign17.png "editando o arquivo Entitlements. plist")](sandboxing-images/sign17-large.png#lightbox)
4. Salve as alterações.
5. No **painel de soluções**, clique duas vezes no arquivo de projeto para abrir suas opções para edição:  

    ![Editign opções da solução](sandboxing-images/sign14.png "Editign opções da solução")
6. Selecione **assinatura do Mac**, em seguida, verifique **assinar o pacote de aplicativo** e **assinar o pacote de instalador**. Sob **perfil de provisionamento**, selecione aquele que criamos acima: 

    ![Definindo o perfil de provisionamento](sandboxing-images/sign15.png "definindo o perfil de provisionamento")
7. Clique o **feito** botão.

> [!IMPORTANT]
> Você terá que fechar e reiniciar o Visual Studio para Mac para obtê-lo para reconhecer a nova ID do aplicativo e perfil de provisionamento que foi instalada pelo Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Solução de problemas de provisionamento

Neste momento você deve tentar executar o aplicativo e certifique-se de que tudo o que é assinado e provisionado corretamente. Se o aplicativo ainda é executado como antes, tudo o que é bom. Em caso de falha, você poderá receber uma caixa de diálogo semelhante à seguinte:

[![Um exemplo de provisionamento de caixa de diálogo de problema](sandboxing-images/sign16.png "um exemplo de provisionamento de caixa de diálogo do problema")](sandboxing-images/sign16-large.png#lightbox)

Aqui estão as causas mais comuns de provisionamento e problemas de assinatura:

- A ID do pacote de aplicativo não corresponde à ID do aplicativo do perfil selecionado.
- A ID de desenvolvedor não corresponde à ID de desenvolvedor do perfil selecionado.
- O UUID do que está sendo testado no Mac não está registrado como parte do perfil selecionado.

No caso de um problema, corrija o problema no Portal do desenvolvedor da Apple, atualize os perfis no Xcode e fazer uma compilação limpa no Visual Studio para Mac.

### <a name="enable-the-app-sandbox"></a>Habilitar a área restrita de aplicativo

Você pode habilitar a área restrita de aplicativo, selecionando uma caixa de seleção em suas opções de projetos. Faça o seguinte:

1. No **painel de soluções**, clique duas vezes o **Entitlements. plist** arquivo para abri-lo para edição.
2. Marque ambos **habilitar direitos** e **habilitar a área restrita de aplicativo**: 

    [![Edição de direitos e habilitar a área restrita](sandboxing-images/sign17.png "edição de direitos e habilitar a área restrita")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações.

Neste ponto, você habilitou a área restrita de aplicativo, mas você não forneceu o acesso de rede necessária para a exibição da Web. Se você executar o aplicativo agora, você verá uma janela em branco:

[![Mostrando o acesso via web sendo bloqueado](sandboxing-images/sample08.png "mostrando o acesso via web que está sendo bloqueado")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Verificando se o aplicativo está na área restrita

Além do comportamento de bloqueio de recurso, existem três maneiras principais de dizer se um aplicativo xamarin. MAC foi confirmado com êxito em área restrita:

1. No Finder, verifique o conteúdo do `~/Library/Containers/` pasta - se o aplicativo em área restrita, haverá uma pasta chamada como o identificador de pacote do aplicativo (exemplo: `com.appracatappra.MacSandbox`): 

    [![Abrir o pacote do aplicativo](sandboxing-images/sample09.png "abrindo o pacote do aplicativo")](sandboxing-images/sample09-large.png#lightbox)
2. O sistema considera o aplicativo em área restrita no Monitor de atividade:
    - Iniciar o Monitor de atividade (em `/Applications/Utilities`). 
    - Escolher **modo de exibição** > **colunas** e certifique-se de que o **Sandbox** item de menu é marcado.
    - Certifique-se de que a coluna de área restrita lê `Yes` para seu aplicativo: 

    [![Verificando o aplicativo no Monitor de atividade](sandboxing-images/sample10.png "verificando o aplicativo no Monitor de atividade")](sandboxing-images/sample10-large.png#lightbox)
3. Verifique se o binário do aplicativo é em área restrita:
    - Inicie o aplicativo de Terminal.
    - Navegue até os aplicativos `bin` directory.
    - Execute este comando: `codesign -dvvv --entitlements :- executable_path` (onde `executable_path` é o caminho para seu aplicativo): 

    [![Verificando o aplicativo na linha de comando](sandboxing-images/sample11.png "verificando o aplicativo na linha de comando")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Depurar um aplicativo em área restrita

O depurador se conectar a aplicativos xamarin. MAC por meio de TCP, o que significa que por padrão quando você habilita a área restrita, não é possível se conectar ao aplicativo, portanto, se você tentar executar o aplicativo sem as permissões adequadas habilitadas, você receberá um erro *"não é possível se conectar ao o depurador"* . 

[![Definindo as opções necessárias](sandboxing-images/debug01.png "definindo as opções necessárias")](sandboxing-images/debug01-large.png#lightbox)

O **permitir saída conexões de rede (cliente)** permissão é aquela exigida para que o depurador, habilitar esse permitirá depuração normalmente. Uma vez que você não pode depurar sem ele, atualizamos o `CompileEntitlements` de destino para `msbuild` para adicionar automaticamente essa permissão para os direitos para compilações de qualquer aplicativo que está na área restrita para depuração somente. Builds de versão devem usar os direitos especificados no arquivo de direitos, sem modificações.

### <a name="resolving-an-app-sandbox-violation"></a>Resolvendo uma violação de área restrita de aplicativo

Uma violação de área restrita de aplicativo ocorre se uma área restrita e xamarin. Mac aplicativo tentou acessar um recurso que tem não serem explicitamente permitidos. Por exemplo, nossa exibição da Web não está mais sendo capaz de exibir o site da Apple.

A origem mais comum de violações de área restrita de aplicativo ocorre quando as configurações de autorização especificadas no Visual Studio para Mac não correspondem aos requisitos do aplicativo. Novamente, de volta para nosso exemplo, a Conexão de rede ausentes direitos que mantêm o modo de exibição da Web do trabalho.

#### <a name="discovering-app-sandbox-violations"></a>Descobrir violações de área restrita de aplicativo

Se você suspeitar de uma violação de área restrita do aplicativo está ocorrendo em seu aplicativo xamarin. Mac, a maneira mais rápida para descobrir o problema é usando o **Console** aplicativo.

Faça o seguinte:

1. Compilar o aplicativo em questão e executá-lo do Visual Studio para Mac.
2. Abra o **Console** aplicativo (da `/Applications/Utilties/`).
3. Selecione **todas as mensagens** na barra lateral e insira `sandbox` na pesquisa: 

    [![Um exemplo de um problema de modo seguro no console do](sandboxing-images/resolve01.png "um exemplo de um problema de modo seguro no console do")](sandboxing-images/resolve01-large.png#lightbox)

Para nosso aplicativo de exemplo acima, você pode ver que o kernel está bloqueando o `network-outbound` tráfego por causa da área de restrita de aplicativo, desde que não tenhamos solicitado certo.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Correção de violações com direitos de área restrita de aplicativo

Agora que vimos como encontrar violações de modo seguro do aplicativo, vamos ver como eles podem ser resolvidos, ajustando os direitos do nosso aplicativo.

Faça o seguinte:

1. No **painel de soluções**, clique duas vezes o **Entitlements. plist** arquivo para abri-lo para edição.
2. Sob o **direitos** seção, verifique os **permitir saída conexões de rede (cliente)** caixa de seleção: 

    [![Editando os direitos](sandboxing-images/sign17.png "editando os direitos")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações para o aplicativo.

Se, em seguida, podemos executar as ações acima para nosso exemplo de aplicativo compilar e executá-lo, o conteúdo da web agora será exibido conforme o esperado.

## <a name="the-app-sandbox-in-depth"></a>A área restrita de aplicativo detalhados

Os mecanismos de controle de acesso fornecidos pelo aplicativo de área restrita são poucos e fáceis de entender. No entanto, a maneira que a área restrita de aplicativo será adotada por cada aplicativo é exclusivo e com os requisitos do aplicativo.

Considerando seu melhor esforço para proteger seu aplicativo xamarin. MAC sejam exploradas por códigos mal-intencionados, precisa haver apenas uma única vulnerabilidade no aplicativo (ou uma das bibliotecas ou estruturas consome) para obter o controle de interações do aplicativo com o sistema.

A área restrita de aplicativo foi projetada para evitar a tomada de controle (ou limitar o dano pode causar), permitindo que você especifique a interações pretendido do aplicativo com o sistema. O sistema só concederá acesso ao recurso que seu aplicativo requer para concluir seu trabalho e nada mais.

Ao projetar para a área restrita de aplicativo, você está projetando para um cenário de pior caso. Se o aplicativo comprometido por código mal-intencionado, é limitado a acesso somente a arquivos e recursos na proteção do aplicativo.

### <a name="entitlements-and-system-resource-access"></a>Os recursos e acesso aos recursos de sistema

Como vimos anteriormente, um aplicativo xamarin. MAC que não tenha sido em área restrita é concedido a todos os direitos e o acesso do usuário que está executando o aplicativo. Se comprometido por código mal-intencionado, um aplicativo não protegido pode agir como um agente para o comportamento hostil, com uma ampla variando potencial para provocar danos.

Ao habilitar a área restrita de aplicativo, você pode remove tudo, exceto um conjunto mínimo de privilégios, o que você e habilitar novamente em uma base somente necessidade usando os direitos do seu aplicativo xamarin. Mac. 

Modificar recursos de área restrita do aplicativo do seu aplicativo editando sua **Entitlements. plist** arquivos e de verificação ou selecionando os direitos necessários nas caixas suspensas editores:

[![Editando os direitos](sandboxing-images/sign17.png "editando os direitos")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Acesso de sistema de arquivos e diretórios de contêiner

Quando seu aplicativo xamarin. Mac adota a área restrita de aplicativo, ele tem acesso para os seguintes locais:

- **O diretório de contêiner do aplicativo** -na primeira execução, o sistema operacional cria um especial _diretório de contêiner_ onde ir todos os seus recursos, que somente ele possa acessar. O aplicativo terá acesso completo de leitura/gravação a esse diretório.
- **Diretórios de contêiner do grupo de aplicativo** -seu aplicativo pode ter acesso a um ou mais _contêineres grupo_ que são compartilhados entre aplicativos no mesmo grupo.
- **Arquivos especificados pelo usuário** -automaticamente o seu aplicativo obtém acesso aos arquivos que são explicitamente aberto ou arrastado e solto no aplicativo pelo usuário.
- **Itens relacionados** -com os direitos apropriados, seu aplicativo pode ter acesso a um arquivo com o mesmo nome mas com uma extensão diferente. Por exemplo, um documento que foi salvo como um `.txt` arquivo e um `.pdf`.
- **Diretórios temporários, diretórios de ferramenta de linha de comando e locais específicos de mundo legível** -seu aplicativo tem vários níveis de acesso a arquivos em outros locais bem definidos, conforme especificado pelo sistema.

#### <a name="the-app-container-directory"></a>O diretório de contêiner do aplicativo

Diretório de contêiner do aplicativo de um aplicativo xamarin. MAC tem as seguintes características:

- Ele está em um local oculto no diretório da página inicial do usuário (normalmente `~Library/Containers`) e pode ser acessado com o `NSHomeDirectory` função (veja abaixo) dentro de seu aplicativo. Porque ele está no diretório inicial, cada usuário receberá seu próprio contêiner para o aplicativo.
- O aplicativo tem acesso de leitura/gravação irrestrito para o diretório de contêiner e todos os seus subdiretórios e arquivos dentro dele.
- A maioria das APIs de localização de caminho do macOS é relativo ao contêiner do aplicativo. Por exemplo, o contêiner terá seu próprio **biblioteca** (acessado por meio de `NSLibraryDirectory`), **suporte a aplicativos** e **preferências** subdiretórios.
- macOS estabelece e impõe a conexão entre o aplicativo e seu contêiner por meio de assinatura de código. É até mesmo outro aplicativo tenta falsificar o aplicativo usando sua **identificador de pacote**, ele não poderá acessar o contêiner devido a assinatura de código.
- O contêiner não é para arquivos gerados pelo usuário. Em vez disso, ele é para arquivos que o aplicativo usa, como bancos de dados, caches ou outros tipos específicos de dados.
- Para _shoebox_ tipos de aplicativos (como o aplicativo de fotos da Apple), o conteúdo do usuário entrarão no contêiner.

> [!IMPORTANT]
> Infelizmente, xamarin. Mac não tem 100% de cobertura de API ainda (ao contrário do xamarin. IOS), assim o `NSHomeDirectory` API não foi mapeada na versão atual do xamarin. Mac.

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

#### <a name="the-app-group-container-directory"></a>O diretório de contêiner do grupo de aplicativo

Começando com o Mac macOS 10.7.5 (e posterior), um aplicativo pode usar o `com.apple.security.application-groups` direito para acessar um contêiner compartilhado que é comum a todos os aplicativos no grupo. Você pode usar esse contêiner compartilhado para conteúdo de não-usuário voltado para o banco de dados ou outros tipos de arquivos de suporte (como caches).

Os contêineres de grupo são adicionados automaticamente a área restrita do contêiner cada aplicativo (se eles são parte de um grupo) e são armazenados em `~/Library/Group Containers/<application-group-id>`. A ID do grupo _deve_ começam com sua ID de equipe de desenvolvimento e um período, por exemplo:

```plist
<team-id>.com.company.<group-name>
```

Para obter mais informações, consulte da Apple [adicionando um aplicativo para um grupo de aplicativos](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) na [referência de chave de direito](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Acesso ao sistema Powerbox e o arquivo fora do contêiner de aplicativo

Um aplicativo em área restrita do xamarin. Mac pode acessar o sistema de arquivos locais fora de seu contêiner das seguintes maneiras:

- Sob a orientação específica do usuário (por meio de abrir e salvar caixas de diálogo ou outros métodos, como arrastar e soltar).
- Usando os direitos para locais de sistema de arquivo específico (como `/bin` ou `/usr/lib`).
* Quando o local do sistema de arquivos está em determinados diretórios que são mundo legível (como compartilhamento).

_Powerbox_ é a tecnologia de segurança que interage com o usuário para expandir os direitos de acesso de arquivo na área restrita do aplicativo xamarin. Mac o macOS. Powerbox não tem nenhuma API, mas é ativado de modo transparente quando o aplicativo chama um `NSOpenPanel` ou `NSSavePanel`. Acesso Powerbox é habilitado por meio de direitos que você definir para seu aplicativo xamarin. Mac.

Quando um aplicativo de área restrita e exibe um aberto ou caixa de diálogo Salvar, a janela é apresentada pelo Powerbox (e não AppKit) e, portanto, tem acesso a qualquer arquivo ou diretório que o usuário tem acesso.

Quando um usuário seleciona um arquivo ou diretório de abrir ou caixa de diálogo Salvar (ou arrasta-o até o ícone do aplicativo), Powerbox adiciona o caminho associado à proteção do aplicativo.

Além disso, o sistema automaticamente permite o seguinte a um aplicativo em área restrita:

- Conexão a um método de entrada do sistema.
- Chamar serviços selecionados pelo usuário da **Services** menu (apenas nos serviços marcado como _seguro para aplicativos de área restrita_ pelo provedor de serviços).
- Escolha de arquivos abertos pelo usuário a partir de **abrir recente** menu.
- Use copiar e colar entre os outros aplicativos.
- Ler arquivos de mundo legível locais a seguir:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- Ler e gravar arquivos nos diretórios criados pelo `NSTemporaryDirectory`.

Seja o padrão, arquivos aberto ou salvo por um aplicativo em área restrita do xamarin. MAC permanecem acessíveis até que o aplicativo ser encerrado (a menos que o arquivo estava aberto quando o aplicativo é fechado). Arquivos abertos serão restaurados automaticamente à proteção do aplicativo por meio do recurso de retomada do macOS na próxima vez que o aplicativo é iniciado.

Para fornecer a persistência para arquivos localizados fora do contêiner de um App xamarin. Mac, use indicadores com escopo de segurança (veja abaixo).

#### <a name="related-items"></a>Itens relacionados

A área restrita de aplicativo permite que um aplicativo acessar itens relacionados que têm o mesmo nome de arquivo, mas extensões diferentes. O recurso tem duas partes: a) uma lista de extensões relacionadas no aplicativo do `Info.plst` arquivo, b) o código para informar a área restrita que o aplicativo será fazer com esses arquivos.

Há dois cenários em que isso faz sentido:

1. O aplicativo precisa ser capaz de salvar uma versão diferente do arquivo (com uma nova extensão). Por exemplo, exportando uma `.txt` o arquivo para um `.pdf` arquivo. Para lidar com essa situação, você deve usar um `NSFileCoordinator` para acessar o arquivo. Você chamará o `WillMove(fromURL, toURL)` método primeiro, mova o arquivo para a nova extensão e, em seguida, chamar `ItemMoved(fromURL, toURL)`.
2. O aplicativo precisa abrir um arquivo principal com uma extensão e vários arquivos de suporte com extensões diferentes. Por exemplo, um filme e um arquivo de subtítulo. Use um `NSFilePresenter` para acessar o arquivo secundário. Forneça o arquivo principal para o `PrimaryPresentedItemURL` propriedade e o arquivo secundário para o `PresentedItemURL` propriedade. Quando o arquivo principal for aberto, chame o `AddFilePresenter` método da `NSFileCoordinator` classe para registrar o arquivo secundário.

Em ambos os cenários, o aplicativo **Info. plist** arquivo deve declarar os tipos de documento que o aplicativo pode ser aberto. Para qualquer tipo de arquivo, adicione a `NSIsRelatedItemType` (com um valor de `YES`) para sua entrada no `CFBundleDocumentTypes` matriz.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Abrir e salvar o comportamento da caixa de diálogo com os aplicativos de área restrita

Os seguintes limites são colocados na `NSOpenPanel` e `NSSavePanel` ao chamá-los a partir de um aplicativo em área restrita do xamarin. Mac:

- Você não pode invocar programaticamente a **Okey** botão.
- Por meio de programação, você não pode alterar a seleção do usuário em um `NSOpenSavePanelDelegate`.

Além disso, as modificações de herança a seguir estão em vigor:

- **Aplicativo não em área restrita** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Indicadores no escopo de segurança e acesso a recursos persistente

Como mencionado acima, um aplicativo de área restrita e xamarin. Mac pode acessar um arquivo ou recurso fora de seu contêiner por meio de interação direta do usuário (como fornecido pela PowerBox). No entanto, acesso a esses recursos não automaticamente persiste entre o aplicativo é iniciado ou o sistema é reiniciado.

Usando _Security-Scoped indicadores_, um aplicativo de área restrita e xamarin. Mac pode preservar a intenção do usuário e manter o acesso a determinado recursos depois que um aplicativo reiniciar.

#### <a name="security-scoped-bookmark-types"></a>Tipos de indicador com escopo de segurança

Ao trabalhar com indicadores Security-Scoped e acesso a recursos persistente, há dois casos de uso sistine:

- **Um indicador de App-Scoped fornece acesso persistente para um arquivo especificado pelo usuário ou uma pasta.** 

    Por exemplo, se o aplicativo em área restrita do xamarin. MAC permite usar para abrir um documento externo para edição (usando um `NSOpenPanel`), o aplicativo pode criar um indicador de App-Scoped para que ele possa acessar o mesmo arquivo novamente no futuro.
- **Um indicador de Document-Scoped fornece um acesso persistente de documento específico em um arquivo de subpropriedades.** 

Por exemplo, um aplicativo de edição de vídeo que cria um arquivo de projeto que tem acesso às imagens individuais, clipes de vídeo e arquivos de som que posteriormente serão combinados em um único filme.

Quando o usuário importa um arquivo de recurso para o projeto (por meio de um `NSOpenPanel`), o aplicativo cria um indicador de Document-Scoped para o item que é armazenado no projeto, para que o arquivo esteja sempre acessível para o aplicativo.

Um indicador de Document-Scoped podem ser resolvido por qualquer aplicativo que pode abrir os dados de indicador e o próprio documento. Isso dá suporte à portabilidade, permitindo que o usuário enviar os arquivos de projeto para outro usuário e ter todos os indicadores funcionam bem para eles.

> [!IMPORTANT]
> Um indicador de Document-Scoped pode _só_ apontar para um único arquivo e não uma pasta e esse arquivo não pode estar em um local usado pelo sistema (como `/private` ou `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Usando indicadores no escopo de segurança

Usando qualquer tipo de indicador Security-Scoped, exige que você execute as seguintes etapas:

1. **Definir os direitos apropriados no aplicativo do xamarin. MAC que precisa usar indicadores Security-Scoped** -For App-Scoped indicadores, defina a `com.apple.security.files.bookmarks.app-scope` chave de autorização para `true`. Para Document-Scoped indicadores, defina as `com.apple.security.files.bookmarks.document-scope` chave de autorização para `true`.
2. **Criar um indicador de Security-Scoped** -você fará isso para qualquer arquivo ou pasta que o usuário forneceu acesso a (por meio de `NSOpenPanel` por exemplo), que o aplicativo precisará acessar persistente. Use o `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` método da `NSUrl` classe para criar o indicador.
3. **Resolver o indicador Security-Scoped** - quando o aplicativo precisa para acessar o recurso novamente (por exemplo, após a reinicialização) será necessário resolver o indicador para uma URL com escopo de segurança. Use o `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` método da `NSUrl` classe resolver o indicador.
4. **Notificar explicitamente o sistema que você deseja acessar o arquivo da URL Security-Scoped** – esta etapa precisa ser feita imediatamente depois de obter a URL de Security-Scoped acima ou, quando você desejar recuperar o acesso ao recurso depois de ter liberada à medida seu acesso a ele. Chame o `StartAccessingSecurityScopedResource ()` método da `NSUrl` classe para começar a acessar uma URL Security-Scoped.
5. **Notificar explicitamente o sistema que você terminar de acessar o arquivo da URL Security-Scoped** -assim que possível, você deve informar o sistema quando o aplicativo não precisa mais acesso ao arquivo (por exemplo, se o usuário a feche). Chame o `StopAccessingSecurityScopedResource ()` método da `NSUrl` classe pare de acessar uma URL Security-Scoped.

Depois que você cede o acesso a um recurso, você precisará retornar para a etapa 4 novamente para restabelecer o acesso. Se o aplicativo xamarin. MAC for reiniciado, você deve retornar à etapa 3 e resolver novamente o indicador.

> [!IMPORTANT]
> Falha ao liberar o acesso aos recursos de URL Security-Scoped fará com que um aplicativo xamarin. Mac causar perda de recursos do Kernel. Como resultado, o aplicativo não poderá adicionar locais de sistema de arquivos para seu contêiner até que ele seja reiniciado.

### <a name="the-app-sandbox-and-code-signing"></a>A área restrita de aplicativo e a assinatura de código

Depois de habilitar a área restrita de aplicativo e habilitar os requisitos específicos para seu aplicativo xamarin. MAC (por meio de direitos), você deve assinar código do projeto para a área restrita entrem em vigor. Você deve executar como os direitos necessários para a área restrita de aplicativo estão vinculados a assinatura do aplicativo de assinatura de código. 

macOS impõe um vínculo entre de contêiner um aplicativo e sua assinatura de código, dessa forma, que nenhum outro aplicativo pode acessar esse contêiner, mesmo que a falsificação de aplicativos do ID de pacote. Esse mecanismo funciona da seguinte maneira:

1. Quando o sistema cria o contêiner do aplicativo, ele define uma _lista de controle de acesso_ (ACL) no contêiner. A entrada de controle de acesso iniciais na lista contém o aplicativo _requisito designado_ (DR), que descreve como futuras versões do aplicativo pode ser reconhecido (quando ele tiver sido atualizado).
2. Cada vez que um aplicativo com a mesma ID de pacote é iniciado, o sistema verifica se a assinatura de código do aplicativo corresponde ao designado requisitos especificados em uma das entradas na ACL do contêiner. Se o sistema não encontrar uma correspondência, ele impede que o aplicativo Iniciar.

Code Signing funciona das seguintes maneiras:

1. Antes de criar o projeto xamarin. Mac, obtenha um certificado de desenvolvimento, um certificado de distribuição e um certificado de ID de desenvolvedor do Portal do desenvolvedor da Apple.
2. Quando a Mac App Store distribui o aplicativo xamarin. Mac, ele é assinado com uma assinatura de código da Apple.

Ao testar e depurar, você estará usando uma versão do aplicativo xamarin. MAC que você conectado (que será usado para criar o contêiner de aplicativo). Posteriormente, se você deseja testar ou instale a versão da Apple App Store, será assinado com a assinatura da Apple e falhará ao iniciar (já que ele não tem a mesma assinatura de código como o contêiner original do aplicativo). Nessa situação, você receberá um relatório de falhas semelhante ao seguinte:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Para corrigir isso, você precisará ajustar a entrada ACL para apontar para a versão assinada do Apple do aplicativo.

Para obter mais informações sobre como criar e baixar os perfis de provisionamento necessário para a área restrita, consulte o [de assinatura e provisionamento de aplicativo](#Signing_and_Provisioning_the_App) seção acima.

#### <a name="adjusting-the-acl-entry"></a>Ajustando a entrada ACL

Para permitir que a versão assinada do Apple do aplicativo xamarin. Mac para ser executado, faça o seguinte:

1. Abra o aplicativo Terminal (em `/Applications/Utilities`).
2. Abra uma janela do localizador para a versão assinada do Apple do aplicativo xamarin. Mac.
3. Tipo `asctl container acl add -file` na janela do Terminal.
4. Arraste o ícone do aplicativo xamarin. MAC da janela do localizador e solte-a na janela do Terminal.
5. O caminho completo para o arquivo será adicionado ao comando no Terminal.
6. Pressione **Enter** para executar o comando.

Agora, a ACL do contêiner contém os requisitos de códigos designado para ambas as versões do aplicativo xamarin. Mac e macOS agora permitirá que seja a versão ser executado.

#### <a name="display-a-list-of-acl-code-requirements"></a>Exibir uma lista de requisitos de ACL de código

Você pode exibir uma lista dos requisitos de código na ACL do contêiner fazendo o seguinte:

1. Abra o aplicativo Terminal (em `/Applications/Utilities`).
2. Digite `asctl container acl list -bundle <container-name>` no terminal integrado.
3. Pressione **Enter** para executar o comando.

O `<container-name>` geralmente é o identificador de pacote para o aplicativo xamarin. Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Criando um aplicativo xamarin. Mac para a área restrita de aplicativo

Há um fluxo de trabalho comuns que deve ser seguido durante a criação de um aplicativo xamarin. Mac para a área restrita de aplicativo. Dito isso, as especificações da implementação de área restrita em um aplicativo vai ser exclusivo para a determinada funcionalidade do aplicativo.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Seis etapas para adotar a área restrita de aplicativo

Criando um aplicativo xamarin. Mac para a área restrita de aplicativo normalmente consiste as seguintes etapas:

1. Determina se o aplicativo é adequado para a área restrita.
2. Crie uma estratégia de desenvolvimento e distribuição.
3. Resolva quaisquer incompatibilidades de API.
4. Aplica os direitos de área restrita de aplicativo necessários ao projeto xamarin. Mac.
5. Adicione a separação de privilégio usando XPC.
6. Implemente uma estratégia de migração.

> [!IMPORTANT]
> Você deve colocar o executável principal no pacote de aplicativos, mas também todos os auxiliares incluídos não apenas a ferramenta no pacote ou aplicativo. Isso é necessário para qualquer aplicativo distribuído pela Mac App Store e, se possível, deve ser feito para qualquer outra forma de distribuição de aplicativos.

Para obter uma lista de todos os binários executáveis no pacote do aplicativo um xamarin. Mac, digite o seguinte comando no Terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Onde `[Your-App-Bundle]` é o nome e o caminho para o pacote do aplicativo.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinar se um aplicativo xamarin. MAC é adequado para a área restrita

A maioria dos aplicativos xamarin. Mac são totalmente compatíveis com a área restrita de aplicativo e, portanto, adequado para a área restrita. Se o aplicativo exigir um comportamento que não permite que a área de restrita de aplicativo, você deve considerar uma abordagem alternativa.

Se seu aplicativo requer um dos seguintes comportamentos, é incompatível com a área restrita de aplicativo:

- **Serviços de autorização** -com a área de restrita de aplicativo, não é possível trabalhar com as funções descritas [referência de C de serviços de autorização](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **APIs de acessibilidade** -não é possível aplicativos de área restrita adaptativas como leitores de tela ou aplicativos que controlam a outros aplicativos.
- **Enviar eventos da Apple para aplicativos arbitrários** -se o aplicativo requer o envio de eventos da Apple para um aplicativo desconhecido, arbitrário, ele não pode ser em área restrita. Para obter uma lista conhecida de aplicativos de chamada, o aplicativo ainda pode estar em modo seguro e os direitos precisará incluir a lista de aplicativos de chamada.
- **Enviar dicionários de informações do usuário nas notificações distribuídos para outras tarefas** -com a área de restrita de aplicativo, você não pode incluir uma `userInfo` dicionário ao lançar um `NSDistributedNotificationCenter` objeto para outras tarefas de sistema de mensagens.
- **Carregar extensões de Kernel** -o carregamento de extensões de Kernel é proibido pela área de segurança do aplicativo.
- **Simulando entrada do usuário em caixas de diálogo Salvar e abrir** - programaticamente manipulando abrir ou salvar as caixas de diálogo para simular ou alterar a entrada do usuário é proibida pela área de segurança do aplicativo.
- **Acessando ou preferências de configuração em outros aplicativos** -manipular as configurações de outros aplicativos é proibida pela área de segurança do aplicativo.
- **Definindo configurações de rede** -manipular as configurações de rede é proibida pela área de segurança do aplicativo.
- **Encerrando a outros aplicativos** -a área restrita de aplicativo proíbe usando `NSRunningApplication` para encerrar outros aplicativos.

### <a name="resolving-api-incompatibilities"></a>Resolver as incompatibilidades de API

Ao projetar um aplicativo xamarin. Mac para a área restrita de aplicativo, você pode encontrar as incompatibilidades com o uso de algumas APIs do macOS.

Aqui estão alguns problemas comuns e coisas que você pode fazer para resolvê-los:

- **Abrir, salvar e controle de documentos** – se você estiver gerenciando documentos usando qualquer tecnologia diferente `NSDocument`, você deve alternar para ele por causa do suporte interno para a área restrita de aplicativo. `NSDocument` automaticamente funciona com PowerBox e fornece suporte para manter os documentos dentro de sua área restrita se o usuário movê-los no Finder.
- **Manter o acesso aos recursos do sistema de arquivo** - se o aplicativo depende do acesso persistente a recursos fora de seu contêiner, xamarin. Mac usar indicadores com escopo de segurança para manter o acesso.
- **Criar um Item de logon para um aplicativo** -com a área de restrita de aplicativo, você não pode criar um logon usando o item `LSSharedFileList` nem você pode manipular o estado dos serviços de inicialização usando `LSRegisterURL`. Use o `SMLoginItemSetEnabled` funcionar conforme descrito no maçãs [adicionando itens de logon usando a estrutura de gerenciamento de serviço](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) documentação.
- **Acessando dados do usuário** – se você estiver usando, como funções POSIX `getpwuid` para obter o diretório base do usuário de serviços de diretório, considere o uso de símbolos Cocoa ou principal base como `NSHomeDirectory`.
- **Acessando as preferências de outros aplicativos** – porque a área restrita de aplicativo direciona as APIs de localização de caminho para o contêiner do aplicativo, modificando as preferências ocorre dentro do contêiner e acessando outro preferências de aplicativos em não permitido. 
- **Usando o vídeo incorporado do HTML5 em modos de exibição Web** -se o aplicativo xamarin. Mac usa WebKit para reproduzir vídeos incorporados do HTML5, você também deve vincular o aplicativo de acordo com a estrutura de AV Foundation. A área restrita de aplicativo impedirá CoreMedia play esses vídeos caso contrário.

### <a name="applying-required-app-sandbox-entitlements"></a>Aplicação de direitos de área restrita de aplicativo necessários

Você precisará editar os direitos para qualquer aplicativo do xamarin. MAC que você deseja executar na área restrita do aplicativo e verificar a **habilitar o modo de aplicativo seguro** caixa de seleção.

Com base na funcionalidade do aplicativo, você talvez precise habilitar outros direitos acessar os recursos ou recursos do sistema operacional. Portanto, áreas de segurança de aplicativo funciona melhor quando você minimizar os direitos que você solicitar o mínimo necessário para executar seu aplicativo apenas aleatoriamente habilitar direitos.

Para determinar quais direitos requer um aplicativo xamarin. Mac, faça o seguinte:

1. Habilitar a área restrita de aplicativo e executar o aplicativo xamarin. Mac.
2. Execute por meio dos recursos do aplicativo.
3. Abra o aplicativo de Console (disponível no `/Applications/Utilities`) e procure `sandboxd` violações na **todas as mensagens** log.
4. Para cada `sandboxd` violação, resolva o problema usando o contêiner de aplicativo em vez de outros locais de sistema de arquivos ou aplicar direitos de área restrita de aplicativo para habilitar o acesso a recursos restritos do sistema operacional.
5. Execute novamente e teste de todos os recursos de aplicativo do xamarin. Mac novamente.
6. Repita até que todos os `sandboxd` violações que foram resolvidas.

### <a name="add-privilege-separation-using-xpc"></a>Adicionar a separação de privilégio usando XPC

Ao desenvolver um aplicativo xamarin. Mac para a área restrita de aplicativo, examine os comportamentos do aplicativo em termos de privilégios e acesso, em seguida, considere separar operações de alto risco em seus próprios serviços XPC.

Para obter mais informações, consulte da Apple [criando serviços de XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) e [o guia de programação de serviços e Daemons](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i).

### <a name="implement-a-migration-strategy"></a>Implementar uma estratégia de migração

Se você estiver liberando uma versão nova de um aplicativo xamarin. MAC que não estava anteriormente em área restrita em área restrita, você precisará garantir que os usuários atuais têm um caminho suave de atualização. 

 Para obter detalhes sobre como implementar um manifesto de migração do contêiner, leia da Apple [migrando um aplicativo para uma área restrita](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) documentação.

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de área restrita um aplicativo xamarin. Mac. Primeiro, criamos um aplicativo de xamarin. Mac simplesmente para mostrar os conceitos básicos da área de segurança do aplicativo. Em seguida, mostramos como resolver as violações de área restrita. Em seguida, usamos um profundo examinar a área restrita de aplicativo e por fim, analisamos criando um aplicativo xamarin. Mac para a área restrita de aplicativo.



## <a name="related-links"></a>Links relacionados

- [Publicando na App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Sobre a área restrita de aplicativo](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemas comuns de área restrita do aplicativo](https://developer.apple.com/library/content/qa/qa1773/_index.html)
