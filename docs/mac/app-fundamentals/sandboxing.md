---
title: Área restrita de um aplicativo Xamarin. Mac
description: Este artigo aborda a área restrita de um aplicativo Xamarin. Mac para lançamento na loja de aplicativos. Ele abrange todos os elementos que entram na área restrita, como diretórios de contêineres, direitos, permissões determinadas pelo usuário, separação de privilégios e imposição de kernel.
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 02059c43d26c2e685abd685231fe5faf3d7a6bfe
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030109"
---
# <a name="sandboxing-a-xamarinmac-app"></a>Área restrita de um aplicativo Xamarin. Mac

_Este artigo aborda a área restrita de um aplicativo Xamarin. Mac para lançamento na loja de aplicativos. Ele abrange todos os elementos que entram na área restrita, como diretórios de contêineres, direitos, permissões determinadas pelo usuário, separação de privilégios e imposição de kernel._

## <a name="overview"></a>Visão Geral

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem a mesma capacidade de colocar um aplicativo em área restrita ao trabalhar com Objective-C ou Swift.

[![Um exemplo do aplicativo em execução](sandboxing-images/intro01.png "Um exemplo do aplicativo em execução")](sandboxing-images/intro01-large.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com a área restrita em um aplicativo Xamarin. Mac e todos os elementos que vão para a área restrita: diretórios de contêiner, direitos, permissões determinadas pelo usuário, separação de privilégios e imposição de kernel. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em Este artigo.

Talvez você queira dar uma olhada na seção [ C# expondo classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica o `Register` e `Export` atributos usados para conectar suas C# classes ao Objective-C objetos e elementos de interface do usuário.

## <a name="about-the-app-sandbox"></a>Sobre a área restrita do aplicativo

A área restrita do aplicativo fornece uma defesa forte contra danos que podem ser causados por um aplicativo mal-intencionado sendo executado em um Mac, limitando o acesso que um aplicativo tem aos recursos do sistema.

Um aplicativo não-sandbox tem os direitos totais do usuário que está executando o aplicativo e pode acessar ou fazer qualquer coisa que o usuário possa. Se o aplicativo contiver brechas de segurança (ou qualquer estrutura que esteja usando), um hacker poderá explorar esses pontos fracos e usar o aplicativo para assumir o controle do Mac em que ele está sendo executado.

Ao limitar o acesso aos recursos por aplicativo, um aplicativo em área restrita fornece uma linha de defesa contra roubo, corrupção ou intenção mal-intencionada na parte de um aplicativo em execução na máquina do usuário.

A área restrita do aplicativo é uma tecnologia de controle de acesso incorporada ao macOS (imposta no nível do kernel) que fornece uma estratégia dupla:

1. A área restrita do aplicativo permite que o desenvolvedor Descreva _como_ um aplicativo irá interagir com o sistema operacional e, dessa forma, recebe apenas os direitos de acesso necessários para realizar o trabalho e não mais.
2. A área restrita do aplicativo permite que o usuário conceda de forma direta acesso adicional ao sistema por meio das caixas de diálogo abrir e salvar, operações de arrastar e soltar e outras interações de usuário comuns.

### <a name="preparing-to-implement-the-app-sandbox"></a>Preparando para implementar a área restrita do aplicativo

Os elementos da área restrita do aplicativo que serão discutidos em detalhes no artigo são os seguintes:

- Diretórios de contêiner
- Direitos
- Permissões determinadas pelo usuário
- Separação de privilégios
- Imposição de kernel

Depois de entender esses detalhes, você poderá criar um plano para adotar a área restrita do aplicativo em seu aplicativo Xamarin. Mac.

Primeiro, você precisará determinar se seu aplicativo é um bom candidato para a área restrita (a maioria dos aplicativos é). Em seguida, você precisará resolver quaisquer incompatibilidades de API e determinar quais elementos da área restrita do aplicativo serão necessários. Por fim, examine o uso da separação de privilégios para maximizar o nível de defesa do aplicativo.

Ao adotar a área restrita do aplicativo, alguns locais do sistema de arquivos que seu aplicativo usa serão diferentes. Particularmente, seu aplicativo terá um diretório de contêiner que será usado para arquivos de suporte de aplicativo, bancos de dados, caches e outros arquivos que não são documentos de usuário. Tanto o macOS quanto o Xcode oferecem suporte para migrar esses tipos de arquivos de seus locais herdados para o contêiner.

## <a name="sandboxing-quick-start"></a>Início rápido da área restrita

Nesta seção, criaremos um aplicativo Xamarin. Mac simples que usa uma exibição da Web (que requer uma conexão de rede restrita na área restrita, a menos que solicitado especificamente) como um exemplo de introdução à área restrita do aplicativo.

Verificaremos se o aplicativo está na verdade em área restrita e saiba como solucionar problemas e resolver erros comuns da área restrita do aplicativo.

### <a name="creating-the-xamarinmac-project"></a>Criando o projeto Xamarin. Mac

Vamos fazer o seguinte para criar nosso projeto de exemplo:

1. Inicie o Visual Studio para Mac e clique na **nova solução..** .
2. Na caixa de diálogo **novo projeto** , selecione **Mac**  > **aplicativo**  > **aplicativo Cocoa**:

    [![Criando um novo aplicativo Cocoa](sandboxing-images/sample01.png "Criando um novo aplicativo Cocoa")](sandboxing-images/sample01-large.png#lightbox)
3. Clique no botão **Avançar** , insira `MacSandbox` para o nome do projeto e clique no botão **criar** :

    [![Inserindo o nome do aplicativo](sandboxing-images/sample02.png "Inserindo o nome do aplicativo")](sandboxing-images/sample02-large.png#lightbox)
4. No **painel de soluções**, clique duas vezes no arquivo **Main. Storyboard** para abri-lo para edição no Xcode:

    [![Editando o storyboard principal](sandboxing-images/sample03.png "Editando o storyboard principal")](sandboxing-images/sample03-large.png#lightbox)
5. Arraste uma **exibição da Web** para a janela, dimensione-a para preencher a área de conteúdo e defina-a para aumentar e reduzir com a janela:

    [![Adicionando uma exibição da Web](sandboxing-images/sample04.png "Adicionando uma exibição da Web")](sandboxing-images/sample04-large.png#lightbox)
6. Crie uma tomada para a exibição da Web chamada `webView`:

    [![Criando uma nova tomada](sandboxing-images/sample05.png "Criando uma nova tomada")](sandboxing-images/sample05-large.png#lightbox)
7. Volte para Visual Studio para Mac e clique duas vezes no arquivo **ViewController.cs** no **painel de soluções** para abri-lo para edição.
8. Adicione a seguinte instrução Using: `using WebKit;`
9. Faça com que o método `ViewDidLoad` se pareça com o seguinte:

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. Salve as alterações.

Execute o aplicativo e verifique se o site da Apple é exibido na janela da seguinte maneira:

[![Mostrando um exemplo de aplicativo de execução](sandboxing-images/sample06.png "Mostrando um exemplo de aplicativo de execução")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>Assinando e Provisionando o aplicativo

Antes que possamos habilitar a área restrita do aplicativo, primeiro precisamos provisionar e assinar nosso aplicativo Xamarin. Mac.

Faça o seguinte:

1. Faça logon no portal do desenvolvedor da Apple:

    [![Fazendo logon no portal do desenvolvedor da Apple](sandboxing-images/sign01.png "Fazendo logon no portal do desenvolvedor da Apple")](sandboxing-images/sign01-large.png#lightbox)
2. Selecione **certificados, identificadores & perfis**:

    [![Selecionando certificados, identificadores & perfis](sandboxing-images/sign02.png "Selecionando certificados, identificadores & perfis")](sandboxing-images/sign02-large.png#lightbox)
3. Em **aplicativos Mac**, selecione **identificadores**:

    [![Selecionando identificadores](sandboxing-images/sign03.png "Selecionando identificadores")](sandboxing-images/sign03-large.png#lightbox)
4. Crie uma nova ID para o aplicativo:

    [![Criando uma nova ID do aplicativo](sandboxing-images/sign04.png "Criando uma nova ID do aplicativo")](sandboxing-images/sign04-large.png#lightbox)
5. Em **perfis de provisionamento**, selecione **desenvolvimento**:

    [![Selecionando desenvolvimento](sandboxing-images/sign05.png "Selecionando desenvolvimento")](sandboxing-images/sign05-large.png#lightbox)
6. Crie um novo perfil e selecione **desenvolvimento de aplicativo Mac**:

    [![Criando um novo perfil](sandboxing-images/sign06.png "Criando um novo perfil")](sandboxing-images/sign06-large.png#lightbox)
7. Selecione a ID do aplicativo que criamos acima:

    [![Selecionando a ID do aplicativo](sandboxing-images/sign07.png "Selecionando a ID do aplicativo")](sandboxing-images/sign07-large.png#lightbox)
8. Selecione os desenvolvedores para este perfil:

    [![Adicionando desenvolvedores](sandboxing-images/sign08.png "Adicionando desenvolvedores")](sandboxing-images/sign08-large.png#lightbox)
9. Selecione os computadores para este perfil:

    [![Selecionando os computadores permitidos](sandboxing-images/sign09.png "Selecionando os computadores permitidos")](sandboxing-images/sign09-large.png#lightbox)
10. Dê um nome ao perfil:

    [![Dando um nome ao perfil](sandboxing-images/sign10.png "Dando um nome ao perfil")](sandboxing-images/sign10-large.png#lightbox)
11. Clique no botão **concluído** .

> [!IMPORTANT]
> Em algumas instâncias, talvez seja necessário baixar o novo perfil de provisionamento diretamente do portal do desenvolvedor da Apple e clicar duas vezes nele para instalar o. Talvez você também precise parar e reiniciar Visual Studio para Mac antes de poder acessar o novo perfil.

Em seguida, precisamos carregar a nova ID e o perfil do aplicativo no computador de desenvolvimento. Vamos fazer o seguinte:

1. Inicie o Xcode e selecione **preferências** no menu do **Xcode** :

    ![Editando contas no Xcode](sandboxing-images/sign11.png "Editando contas no Xcode")
2. Clique no botão **Exibir detalhes...** :

    ![Clicando no botão Exibir detalhes](sandboxing-images/sign12.png "Clicando no botão Exibir detalhes")
3. Clique no botão **Atualizar** (no canto inferior esquerdo).
4. Clique no botão **concluído** .

Em seguida, precisamos selecionar a nova ID do aplicativo e o perfil de provisionamento em nosso projeto Xamarin. Mac. Vamos fazer o seguinte:

1. No **painel de soluções**, clique duas vezes no arquivo **info. plist** para abri-lo para edição.
2. Verifique se o **identificador do pacote** corresponde à nossa ID do aplicativo que criamos acima (exemplo: `com.appracatappra.MacSandbox`):

    [![Editando o identificador de pacote](sandboxing-images/sign13.png "Editando o identificador de pacote")](sandboxing-images/sign13-large.png#lightbox)
3. Em seguida, clique duas vezes no arquivo **. plist de direitos** e verifique se o **repositório de chave-valor do icloud** e os **contêineres do icloud** correspondem à nossa ID do aplicativo criada acima (exemplo: `com.appracatappra.MacSandbox`):

    [![Editando o arquivo. plist de direitos](sandboxing-images/sign17.png "Editando o arquivo. plist de direitos")](sandboxing-images/sign17-large.png#lightbox)
4. Salve as alterações.
5. No **painel de soluções**, clique duas vezes no arquivo de projeto para abrir suas opções de edição:

    ![Editign as opções da solução](sandboxing-images/sign14.png "Editign as opções da solução")
6. Selecione **assinatura do Mac**e, em seguida, marque **o pacote do aplicativo** e **assine-o**. Em **perfil de provisionamento**, selecione aquele que criamos acima:

    ![Configurando o perfil de provisionamento](sandboxing-images/sign15.png "Configurando o perfil de provisionamento")
7. Clique no botão **concluído** .

> [!IMPORTANT]
> Talvez seja necessário encerrar e reiniciar Visual Studio para Mac para que ele reconheça a nova ID do aplicativo e o perfil de provisionamento que foi instalado pelo Xcode.

#### <a name="troubleshooting-provisioning-issues"></a>Solucionando problemas de provisionamento

Neste ponto, você deve tentar executar o aplicativo e verificar se tudo está assinado e provisionado corretamente. Se o aplicativo ainda for executado como antes, tudo será bom. Em caso de falha, você pode obter uma caixa de diálogo semelhante à seguinte:

[![Uma caixa de diálogo de problema de provisionamento de exemplo](sandboxing-images/sign16.png "Uma caixa de diálogo de problema de provisionamento de exemplo")](sandboxing-images/sign16-large.png#lightbox)

Aqui estão as causas mais comuns de problemas de provisionamento e assinatura:

- A ID do pacote de aplicativo não corresponde à ID do aplicativo do perfil selecionado.
- A ID do desenvolvedor não corresponde à ID do desenvolvedor do perfil selecionado.
- O UUID do Mac que está sendo testado não está registrado como parte do perfil selecionado.

No caso de um problema, corrija o problema no portal do desenvolvedor da Apple, atualize os perfis no Xcode e faça uma compilação limpa em Visual Studio para Mac.

### <a name="enable-the-app-sandbox"></a>Habilitar a área restrita do aplicativo

Você habilita a área restrita do aplicativo marcando uma caixa de seleção em suas opções de projetos. Faça o seguinte:

1. No **painel de soluções**, clique duas vezes no arquivo **pretitles. plist** para abri-lo para edição.
2. Marque ambos **habilitar direitos** e habilitar a **área restrita do aplicativo**:

    [![Editando direitos e habilitando a área restrita](sandboxing-images/sign17.png "Editando direitos e habilitando a área restrita")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações.

Neste ponto, você habilitou a área restrita do aplicativo, mas não forneceu o acesso de rede necessário para o modo de exibição da Web. Se você executar o aplicativo agora, deverá obter uma janela em branco:

[![Mostrando o acesso à Web que está sendo bloqueado](sandboxing-images/sample08.png "Mostrando o acesso à Web que está sendo bloqueado")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>Verificando se o aplicativo está em área restrita

Além do comportamento de bloqueio de recursos, há três maneiras principais de saber se um aplicativo Xamarin. Mac foi protegido com êxito:

1. No Finder, verifique o conteúdo da pasta `~/Library/Containers/`-se o aplicativo estiver em área restrita, haverá uma pasta chamada como o identificador do pacote do seu aplicativo (exemplo: `com.appracatappra.MacSandbox`):

    [![Abrindo o pacote do aplicativo](sandboxing-images/sample09.png "Abrindo o pacote do aplicativo")](sandboxing-images/sample09-large.png#lightbox)
2. O sistema vê o aplicativo como área restrita no monitor de atividade:
    - Inicie o monitor de atividade (em `/Applications/Utilities`).
    - Escolha **Exibir** **colunas** de  >  e verifique se o item de menu **área restrita** está marcado.
    - Verifique se a coluna sandbox lê `Yes` para seu aplicativo:

    [![Verificando o aplicativo no monitor de atividade](sandboxing-images/sample10.png "Verificando o aplicativo no monitor de atividade")](sandboxing-images/sample10-large.png#lightbox)
3. Verifique se o binário do aplicativo está em área restrita:
    - Inicie o aplicativo de terminal.
    - Navegue até o diretório de `bin` de aplicativos.
    - Emita este comando: `codesign -dvvv --entitlements :- executable_path` (em que `executable_path` é o caminho para seu aplicativo):

    [![Verificando o aplicativo na linha de comando](sandboxing-images/sample11.png "Verificando o aplicativo na linha de comando")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>Depurando um aplicativo em área restrita

O depurador se conecta a aplicativos Xamarin. Mac por meio de TCP, o que significa que, por padrão, quando você habilita a área restrita, ele não consegue se conectar ao aplicativo, portanto, se você tentar executar o aplicativo sem as permissões apropriadas habilitadas, você receberá um erro *"não é possível se conectar ao depurador"* .

[![Definindo as opções necessárias](sandboxing-images/debug01.png "Definindo as opções necessárias")](sandboxing-images/debug01-large.png#lightbox)

A permissão **permitir conexões de rede de saída (cliente)** é a necessária para o depurador, permitindo que a depuração seja normalmente ativada. Como não é possível depurar sem ele, atualizamos o destino de `CompileEntitlements` para `msbuild` adicionar automaticamente essa permissão aos direitos de qualquer aplicativo que esteja em área restrita somente para compilações de depuração. As compilações de versão devem usar os direitos especificados no arquivo de direitos, sem modificações.

### <a name="resolving-an-app-sandbox-violation"></a>Resolvendo uma violação da área restrita do aplicativo

Uma violação de área restrita do aplicativo ocorrerá se um aplicativo Xamarin. Mac em área restrita tentar acessar um recurso que não tenha sido explicitamente permitido. Por exemplo, nossa exibição da Web não pode mais exibir o site da Apple.

A fonte mais comum de violações de área restrita do aplicativo ocorre quando as configurações de direito especificadas em Visual Studio para Mac não correspondem aos requisitos do aplicativo. Novamente, de volta ao nosso exemplo, os direitos de conexão de rede ausentes que mantêm a exibição da Web funcionando.

#### <a name="discovering-app-sandbox-violations"></a>Descobrindo violações de área restrita do aplicativo

Se você suspeitar que uma violação da área restrita do aplicativo está ocorrendo no aplicativo Xamarin. Mac, a maneira mais rápida de descobrir o problema é usando o aplicativo de **console** .

Faça o seguinte:

1. Compile o aplicativo em questão e execute-o em Visual Studio para Mac.
2. Abra o aplicativo de **console** (de `/Applications/Utilties/`).
3. Selecione **todas as mensagens** na barra lateral e insira `sandbox` na pesquisa:

    [![Um exemplo de um problema de área restrita no console](sandboxing-images/resolve01.png "Um exemplo de um problema de área restrita no console")](sandboxing-images/resolve01-large.png#lightbox)

Para nosso aplicativo de exemplo acima, você pode ver que o kerning está bloqueando o tráfego de `network-outbound` devido à área restrita do aplicativo, já que não solicitamos esse direito.

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>Corrigindo violações de área restrita do aplicativo com direitos

Agora que vimos como encontrar violações de área restrita do aplicativo, vejamos como elas podem ser resolvidas ajustando os direitos do nosso aplicativo.

Faça o seguinte:

1. No **painel de soluções**, clique duas vezes no arquivo **pretitles. plist** para abri-lo para edição.
2. Na seção **direitos** , marque a caixa de seleção **permitir conexões de rede de saída (cliente)** :

    [![Editando os direitos](sandboxing-images/sign17.png "Editando os direitos")](sandboxing-images/sign17-large.png#lightbox)
3. Salve as alterações no aplicativo.

Se fizermos o anterior para nosso aplicativo de exemplo, compile e execute-o, o conteúdo da Web agora será exibido conforme o esperado.

## <a name="the-app-sandbox-in-depth"></a>A área restrita do aplicativo em profundidade

Os mecanismos de controle de acesso fornecidos pela área restrita do aplicativo são poucos e fáceis de entender. No entanto, a maneira como a área restrita do aplicativo será adotada por cada aplicativo é exclusiva e com base nos requisitos do aplicativo.

Devido ao seu melhor esforço para proteger seu aplicativo Xamarin. Mac de ser explorado por código mal-intencionado, precisa haver apenas uma única vulnerabilidade no aplicativo (ou em uma das bibliotecas ou estruturas que ele consome) para obter o controle das interações do aplicativo com o sistema.

A área restrita do aplicativo foi projetada para impedir o tomada (ou limitar o dano que pode causar), permitindo que você especifique as interações pretendidas do aplicativo com o sistema. O sistema só concederá acesso ao recurso que seu aplicativo exige para realizar seu trabalho e nada mais.

Ao projetar para a área restrita do aplicativo, você está projetando para um cenário de pior caso. Se o aplicativo for comprometido por código mal-intencionado, ele será limitado a acessar apenas os arquivos e recursos na área restrita do aplicativo.

### <a name="entitlements-and-system-resource-access"></a>Direitos e acesso a recursos do sistema

Como vimos acima, um aplicativo Xamarin. Mac que não foi colocado na área restrita recebe os direitos totais e o acesso do usuário que está executando o aplicativo. Se comprometido por código mal-intencionado, um aplicativo não protegido pode atuar como um agente para o comportamento hostil, com um grande potencial para causar danos.

Ao habilitar a área restrita do aplicativo, você remove todos, exceto um conjunto mínimo de privilégios, que você reabilita de acordo com a necessidade somente usando os direitos do aplicativo Xamarin. Mac.

Você modifica os recursos de área restrita do aplicativo de seus aplicativos editando seu arquivo Rights **. plist** e verificando ou selecionando os direitos necessários nas caixas suspensas de editores:

[![Editando os direitos](sandboxing-images/sign17.png "Editando os direitos")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>Diretórios de contêineres e acesso ao sistema de arquivos

Quando seu aplicativo Xamarin. Mac adota a área restrita do aplicativo, ele tem acesso aos seguintes locais:

- **O diretório do contêiner de aplicativo** -na primeira execução, o sistema operacional cria um _diretório de contêiner_ especial em que todos os seus recursos vão, que somente ele pode acessar. O aplicativo terá acesso completo de leitura/gravação a esse diretório.
- **Diretórios de contêiner do grupo de aplicativos** – seu aplicativo pode receber acesso a um ou mais _contêineres de grupo_ que são compartilhados entre aplicativos no mesmo grupo.
- **Arquivos especificados pelo usuário** – seu aplicativo obtém automaticamente o acesso a arquivos que são explicitamente abertos ou arrastados e descartados no aplicativo pelo usuário.
- **Itens relacionados** -com os direitos apropriados, seu aplicativo pode ter acesso a um arquivo com o mesmo nome, mas com uma extensão diferente. Por exemplo, um documento que foi salvo como um arquivo de `.txt` e um `.pdf`.
- **Diretórios temporários, diretórios de ferramenta de linha de comando e locais específicos legíveis** para o mundo – seu aplicativo tem graus variados de acesso a arquivos em outros locais bem definidos, conforme especificado pelo sistema.

#### <a name="the-app-container-directory"></a>O diretório do contêiner do aplicativo

Um diretório do contêiner de aplicativos do Xamarin. Mac tem as seguintes características:

- Ele está em um local oculto no diretório base do usuário (geralmente `~Library/Containers`) e pode ser acessado com a função `NSHomeDirectory` (veja abaixo) em seu aplicativo. Como ele está no diretório base, cada usuário receberá seu próprio contêiner para o aplicativo.
- O aplicativo tem acesso irrestrito de leitura/gravação ao diretório do contêiner e a todos os seus subdiretórios e arquivos dentro dele.
- A maioria das APIs de localização de caminho do macOS é relativa ao contêiner do aplicativo. Por exemplo, o contêiner terá sua própria **biblioteca** (acessada via `NSLibraryDirectory`), subdiretórios de suporte e **preferências** de **aplicativo** .
- o macOS estabelece e impõe a conexão entre o e o aplicativo e seu contêiner por meio da assinatura de código. Mesmo que outro aplicativo tente falsificar o aplicativo usando seu **identificador de pacote**, ele não poderá acessar o contêiner devido à assinatura de código.
- O contêiner não é para arquivos gerados pelo usuário. Em vez disso, é para arquivos que seu aplicativo usa, como bancos de dados, caches ou outros tipos específicos de dado.
- Para os tipos de aplicativos _sapatos_ (como o aplicativo de fotos da Apple), o conteúdo do usuário entrará no contêiner.

> [!IMPORTANT]
> Infelizmente, o Xamarin. Mac ainda não tem a cobertura de API de 100% (diferente do Xamarin. iOS), como resultado, a API de `NSHomeDirectory` não foi mapeada na versão atual do Xamarin. Mac.

Como solução alternativa temporária, você pode usar o seguinte código:

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>O diretório do contêiner do grupo de aplicativos

A partir do Mac macOS 10.7.5 (e superior), um aplicativo pode usar o direito de `com.apple.security.application-groups` para acessar um contêiner compartilhado que é comum a todos os aplicativos do grupo. Você pode usar esse contêiner compartilhado para conteúdo não voltado para o usuário, como banco de dados ou outros tipos de arquivos de suporte (como caches).

Os contêineres de grupo são adicionados automaticamente ao contêiner de área restrita de cada aplicativo (se eles forem parte de um grupo) e armazenados em `~/Library/Group Containers/<application-group-id>`. A ID do grupo _deve_ começar com sua ID de equipe de desenvolvimento e um ponto, por exemplo:

```plist
<team-id>.com.company.<group-name>
```

Para obter mais informações, consulte a seção [adicionando um aplicativo a um grupo de aplicativos](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) em [referência de chave de direito](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195).

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>Powerbox e acesso ao sistema de arquivos fora do contêiner do aplicativo

Um aplicativo Xamarin. Mac em área restrita pode acessar locais do sistema de arquivos fora de seu contêiner das seguintes maneiras:

- Na direção específica do usuário (por meio de caixas de diálogo abrir e salvar ou outros métodos, como arrastar e soltar).
- Usando direitos para locais específicos do sistema de arquivos (como `/bin` ou `/usr/lib`).
- Quando o local do sistema de arquivos está em determinados diretórios que são legíveis para o mundo (como compartilhamento).

_Powerbox_ é a tecnologia de segurança do MacOS que interage com o usuário para expandir os direitos de acesso ao arquivo do aplicativo Xamarin. Mac em área restrita. Powerbox não tem API, mas é ativado de forma transparente quando o aplicativo chama um `NSOpenPanel` ou `NSSavePanel`. O acesso ao Powerbox é habilitado por meio dos direitos que você define para seu aplicativo Xamarin. Mac.

Quando um aplicativo em área restrita exibe uma caixa de diálogo abrir ou salvar, a janela é apresentada por Powerbox (e não AppKit) e, portanto, tem acesso a qualquer arquivo ou diretório ao qual o usuário tenha acesso.

Quando um usuário seleciona um arquivo ou diretório na caixa de diálogo abrir ou salvar (ou arrasta-o para o ícone do aplicativo), Powerbox adiciona o caminho associado à área restrita do aplicativo.

Além disso, o sistema automaticamente permite o seguinte a um aplicativo em área restrita:

- Conexão a um método de entrada do sistema.
- Chame serviços selecionados pelo usuário no menu **Serviços** (somente para serviços marcados como _seguros para aplicativos de área restrita_ pelo provedor de serviços).
- Abrir arquivos escolha pelo usuário no menu **abrir recente** .
- Use copiar & colar entre outros aplicativos.
- Leia os arquivos dos seguintes locais legíveis para o mundo:
  - `/bin`
  - `/sbin`
  - `/usr/bin`
  - `/usr/lib`
  - `/usr/sbin`
  - `/usr/share`
  - `/System`
- Ler e gravar arquivos nos diretórios criados por `NSTemporaryDirectory`.

Por padrão, os arquivos abertos ou salvos por um aplicativo Xamarin. Mac em área restrita permanecem acessíveis até que o aplicativo seja encerrado (a menos que o arquivo ainda esteja aberto quando o aplicativo for encerrado). Os arquivos abertos serão automaticamente restaurados para a área restrita do aplicativo por meio do recurso de retomada do macOS na próxima vez em que o aplicativo for iniciado.

Para fornecer persistência a arquivos localizados fora de um contêiner do aplicativo Xamarin. Mac, use indicadores de escopo de segurança (veja abaixo).

#### <a name="related-items"></a>Itens relacionados

A área restrita do aplicativo permite que um aplicativo acesse itens relacionados que têm o mesmo nome de arquivo, mas extensões diferentes. O recurso tem duas partes: a) uma lista de extensões relacionadas no código do arquivo `Info.plst` do aplicativo, b) para informar à área restrita o que o aplicativo fará com esses arquivos.

Há dois cenários em que isso faz sentido:

1. O aplicativo precisa ser capaz de salvar uma versão diferente do arquivo (com uma nova extensão). Por exemplo, exportar um arquivo de `.txt` para um arquivo de `.pdf`. Para lidar com essa situação, você deve usar um `NSFileCoordinator` para acessar o arquivo. Você chamará o método `WillMove(fromURL, toURL)` primeiro, moverá o arquivo para a nova extensão e, em seguida, chamará `ItemMoved(fromURL, toURL)`.
2. O aplicativo precisa abrir um arquivo principal com uma extensão e vários arquivos de suporte com extensões diferentes. Por exemplo, um filme e um arquivo de subtítulo. Use um `NSFilePresenter` para obter acesso ao arquivo secundário. Forneça o arquivo principal para a propriedade `PrimaryPresentedItemURL` e o arquivo secundário para a propriedade `PresentedItemURL`. Quando o arquivo principal for aberto, chame o método `AddFilePresenter` da classe `NSFileCoordinator` para registrar o arquivo secundário.

Em ambos os cenários, o arquivo **info. plist** do aplicativo deve declarar os tipos de documento que o aplicativo pode abrir. Para qualquer tipo de arquivo, adicione o `NSIsRelatedItemType` (com um valor de `YES`) à sua entrada na matriz de `CFBundleDocumentTypes`.

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>Abrir e salvar o comportamento da caixa de diálogo com aplicativos em área restrita

Os limites a seguir são colocados no `NSOpenPanel` e `NSSavePanel` ao chamá-los de um aplicativo Xamarin. Mac em área restrita:

- Não é possível invocar programaticamente o botão **OK** .
- Não é possível alterar programaticamente a seleção de um usuário em um `NSOpenSavePanelDelegate`.

Além disso, as seguintes modificações de herança estão em vigor:

- **Aplicativo não-sandbox**  -  `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>Indicadores de escopo de segurança e acesso a recursos persistentes

Conforme mencionado acima, um aplicativo Xamarin. Mac em área restrita pode acessar um arquivo ou recurso fora de seu contêiner por meio de interação direta do usuário (conforme fornecido pelo PowerBox). No entanto, o acesso a esses recursos não é mantido automaticamente entre as inicializações do aplicativo ou as reinicializações do sistema.

Usando _indicadores com escopo de segurança_, um aplicativo Xamarin. Mac em área restrita pode preservar a intenção do usuário e manter o acesso a determinados recursos após uma reinicialização do aplicativo.

#### <a name="security-scoped-bookmark-types"></a>Tipos de indicadores com escopo de segurança

Ao trabalhar com indicadores de escopo de segurança e acesso de recursos persistentes, há dois casos de uso Sistine:

- **Um indicador no escopo do aplicativo fornece acesso persistente a um arquivo ou pasta especificado pelo usuário.**

    Por exemplo, se o aplicativo Xamarin. Mac em área restrita permitir que o use o para abrir um documento externo para edição (usando um `NSOpenPanel`), o aplicativo poderá criar um indicador no escopo do aplicativo para que ele possa acessar o mesmo arquivo novamente no futuro.
- **Um indicador com escopo de documento fornece um acesso persistente de documento específico a um subarquivo.**

Por exemplo, um aplicativo de edição de vídeo que cria um arquivo de projeto que tem acesso a imagens individuais, clipes de vídeo e arquivos de som que posteriormente serão combinados em um único filme.

Quando o usuário importa um arquivo de recurso para o projeto (por meio de um `NSOpenPanel`), o aplicativo cria um indicador de escopo de documento para o item armazenado no projeto, para que o arquivo esteja sempre acessível ao aplicativo.

Um indicador com escopo de documento pode ser resolvido por qualquer aplicativo que possa abrir os dados do indicador e o próprio documento. Isso dá suporte à portabilidade, permitindo que o usuário envie os arquivos de projeto para outro usuário e que todos os indicadores também funcionem para eles.

> [!IMPORTANT]
> Um indicador no escopo do documento _só_ pode apontar para um único arquivo e não para uma pasta e esse arquivo não pode estar em um local usado pelo sistema (como `/private` ou `/Library`).

#### <a name="using-security-scoped-bookmarks"></a>Usando indicadores com escopo de segurança

Usando qualquer tipo de indicador com escopo de segurança, o exige que você execute as seguintes etapas:

1. **Defina os direitos apropriados no aplicativo Xamarin. Mac que precisa usar indicadores de escopo de segurança** -para indicadores com escopo de aplicativo, defina a chave de direito de `com.apple.security.files.bookmarks.app-scope` como `true`. Para indicadores com escopo de documento, defina a chave de direito de `com.apple.security.files.bookmarks.document-scope` como `true`.
2. **Criar um indicador com escopo de segurança** – você fará isso para qualquer arquivo ou pasta que o usuário tenha fornecido acesso (via `NSOpenPanel` por exemplo), que o aplicativo precisará de acesso persistente ao. Use o método `public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)` da classe `NSUrl` para criar o indicador.
3. **Resolver o indicador de escopo de segurança** -quando o aplicativo precisar acessar o recurso novamente (por exemplo, após a reinicialização), será necessário resolver o indicador para uma URL com escopo de segurança. Use o método `public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)` da classe `NSUrl` para resolver o indicador.
4. **Notifique explicitamente o sistema que você deseja acessar para o arquivo da URL com escopo de segurança** . essa etapa precisa ser feita imediatamente após a obtenção da URL de escopo de segurança acima ou, quando você quiser novamente obter acesso ao recurso depois de ter abandonou seu acesso a ele. Chame o método `StartAccessingSecurityScopedResource ()` da classe `NSUrl` para começar a acessar uma URL com escopo de segurança.
5. **Notifique explicitamente o sistema de que você terminou de acessar o arquivo da URL com escopo de segurança** -assim que possível, você deve informar o sistema quando o aplicativo não precisar mais de acesso ao arquivo (por exemplo, se o usuário o fechar). Chame o método `StopAccessingSecurityScopedResource ()` da classe `NSUrl` para interromper o acesso a uma URL com escopo de segurança.

Depois que você ceder o acesso a um recurso, precisará retornar para a etapa 4 novamente para restabelecer o acesso. Se o aplicativo Xamarin. Mac for reiniciado, você deverá retornar para a etapa 3 e resolver o indicador novamente.

> [!IMPORTANT]
> Falha ao liberar o acesso a recursos de URL com escopo de segurança fará com que um aplicativo Xamarin. Mac vazasse recursos de kernel. Como resultado, o aplicativo não poderá mais adicionar locais do sistema de arquivos ao seu contêiner até que ele seja reiniciado.

### <a name="the-app-sandbox-and-code-signing"></a>A área restrita do aplicativo e a assinatura de código

Depois de habilitar a área restrita do aplicativo e habilitar os requisitos específicos para seu aplicativo Xamarin. Mac (por meio de direitos), você deve assinar o código para que a área restrita entre em vigor. Você deve executar a assinatura de código porque os direitos necessários para a área restrita do aplicativo estão vinculados à assinatura do aplicativo.

o macOS impõe um link entre o contêiner de um aplicativo e sua assinatura de código, dessa forma nenhum outro aplicativo pode acessar esse contêiner, mesmo que ele esteja falsificando a ID do pacote de aplicativos. Esse mecanismo funciona da seguinte maneira:

1. Quando o sistema cria o contêiner do aplicativo, ele define uma ACL ( _lista de controle de acesso_ ) nesse contêiner. A entrada de controle de acesso inicial na lista contém o _requisito designado_ do aplicativo (Dr), que descreve como as versões futuras do aplicativo podem ser reconhecidas (quando ele foi atualizado).
2. Cada vez que um aplicativo com a mesma ID de pacote é iniciado, o sistema verifica se a assinatura de código do aplicativo corresponde aos requisitos designados especificados em uma das entradas na ACL do contêiner. Se o sistema não encontrar uma correspondência, ele impedirá que o aplicativo seja iniciado.

A assinatura de código funciona das seguintes maneiras:

1. Antes de criar o projeto Xamarin. Mac, obtenha um certificado de desenvolvimento, um certificado de distribuição e um certificado de ID de desenvolvedor do portal do desenvolvedor da Apple.
2. Quando a loja de aplicativos do Mac distribui o aplicativo Xamarin. Mac, ela é assinada com uma assinatura de código da Apple.

Ao testar e depurar, você usará uma versão do aplicativo Xamarin. Mac que assinou (que será usada para criar o contêiner do aplicativo). Posteriormente, se você quiser testar ou instalar a versão da Apple App Store, ela será assinada com a assinatura da Apple e não será iniciada (já que ela não tem a mesma assinatura de código que o contêiner do aplicativo original). Nessa situação, você receberá um relatório de falhas semelhante ao seguinte:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

Para corrigir isso, você precisará ajustar a entrada de ACL para apontar para a versão assinada da Apple do aplicativo.

Para obter mais informações sobre como criar e baixar os perfis de provisionamento necessários para a área restrita, consulte a seção [assinatura e provisionamento do aplicativo](#Signing_and_Provisioning_the_App) acima.

#### <a name="adjusting-the-acl-entry"></a>Ajustando a entrada ACL

Para permitir que a versão assinada pela Apple do aplicativo Xamarin. Mac seja executada, faça o seguinte:

1. Abra o aplicativo de terminal (em `/Applications/Utilities`).
2. Abra uma janela do Finder para a versão assinada da Apple do aplicativo Xamarin. Mac.
3. Digite `asctl container acl add -file` na janela do terminal.
4. Arraste o ícone do aplicativo Xamarin. Mac da janela do Finder e solte-o na janela do terminal.
5. O caminho completo para o arquivo será adicionado ao comando no terminal.
6. Pressione **Enter** para executar o comando.

A ACL do contêiner agora contém os requisitos de código designados para ambas as versões do aplicativo Xamarin. Mac e o macOS agora permitirá que qualquer versão seja executada.

#### <a name="display-a-list-of-acl-code-requirements"></a>Exibir uma lista de requisitos de código ACL

Você pode exibir uma lista dos requisitos de código na ACL de um contêiner fazendo o seguinte:

1. Abra o aplicativo de terminal (em `/Applications/Utilities`).
2. Digite `asctl container acl list -bundle <container-name>`.
3. Pressione **Enter** para executar o comando.

O `<container-name>` geralmente é o identificador do pacote para o aplicativo Xamarin. Mac.

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>Criando um aplicativo Xamarin. Mac para a área restrita do aplicativo

Há um fluxo de trabalho comum que deve ser seguido durante a criação de um aplicativo Xamarin. Mac para a área restrita do aplicativo. Dito isso, as especificidades da implementação da área restrita em um aplicativo serão exclusivas para a funcionalidade do aplicativo fornecido.

### <a name="six-steps-for-adopting-the-app-sandbox"></a>Seis etapas para adotar a área restrita do aplicativo

A criação de um aplicativo Xamarin. Mac para a área restrita do aplicativo normalmente consiste nas seguintes etapas:

1. Determine se o aplicativo é adequado para a área restrita.
2. Projete uma estratégia de desenvolvimento e distribuição.
3. Resolva quaisquer incompatibilidades de API.
4. Aplique os direitos de área de proteção do aplicativo necessários ao projeto Xamarin. Mac.
5. Adicione separação de privilégios usando XPC.
6. Implemente uma estratégia de migração.

> [!IMPORTANT]
> Você não deve apenas colocar em área restrita o executável principal no seu pacote de aplicativo, mas também todos os aplicativos auxiliares ou a ferramenta de ajuda nesse pacote. Isso é necessário para qualquer aplicativo distribuído da Mac App Store e, se possível, deve ser feito para qualquer outra forma de distribuição de aplicativo.

Para obter uma lista de todos os binários executáveis em um pacote do aplicativo Xamarin. Mac, digite o seguinte comando no terminal:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

Onde `[Your-App-Bundle]` é o nome e o caminho para o pacote do aplicativo.

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>Determinar se um aplicativo Xamarin. Mac é adequado para a área restrita

A maioria dos aplicativos Xamarin. Mac é totalmente compatível com a área restrita do aplicativo e, portanto, adequada para a área restrita. Se o aplicativo exigir um comportamento que a área restrita do aplicativo não permita, você deve considerar uma abordagem alternativa.

Se seu aplicativo requer um dos seguintes comportamentos, ele é incompatível com a área restrita do aplicativo:

- **Serviços de autorização** -com a área restrita do aplicativo, você não pode trabalhar com as funções descritas em [referência de serviços de autorização C](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826).
- **APIs de acessibilidade** – você não pode colocar aplicativos auxiliares em área restrita, como leitores de tela ou aplicativos que controlam outros aplicativos.
- **Enviar eventos da Apple para aplicativos arbitrários** – se o aplicativo exigir o envio de eventos da Apple para um aplicativo desconhecido e arbitrário, ele não poderá ser colocado na área restrita. Para obter uma lista conhecida de aplicativos chamados, o aplicativo ainda pode ser colocado em área restrita e os direitos precisarão incluir a lista de aplicativos chamada.
- **Enviar dicionários de informações do usuário em notificações distribuídas para outras tarefas** – com a área restrita do aplicativo, você não pode incluir um dicionário de `userInfo` ao postar em um objeto `NSDistributedNotificationCenter` para mensagens de outras tarefas.
- **Carregar extensões do kernel** – o carregamento de extensões de kernel é proibido pela área restrita do aplicativo.
- **Simulando a entrada do usuário nas caixas de diálogo abrir e salvar** – manipular programaticamente caixas de diálogo abertas ou salvas para simular ou alterar a entrada do usuário é proibido pela área restrita do aplicativo.
- **Acessando ou definindo preferências em outros aplicativos** – manipular as configurações de outros aplicativos é proibido pela área restrita do aplicativo.
- **Definir as configurações de rede** – manipular as configurações de rede é proibido pela área restrita do aplicativo.
- **Encerrando outros aplicativos** – a área restrita do aplicativo proíbe o uso de `NSRunningApplication` para encerrar outros aplicativos.

### <a name="resolving-api-incompatibilities"></a>Resolvendo incompatibilidades de API

Ao criar um aplicativo Xamarin. Mac para a área restrita do aplicativo, você pode encontrar incompatibilidades com o uso de algumas APIs do macOS.

Aqui estão alguns problemas comuns e coisas que você pode fazer para solucioná-los:

- **Abrindo, salvando e acompanhando documentos** – se você estiver gerenciando documentos usando qualquer tecnologia diferente de `NSDocument`, deverá alternar para ele por causa do suporte interno para a área restrita do aplicativo. `NSDocument` funciona automaticamente com o PowerBox e oferece suporte para manter documentos na área restrita se o usuário movê-los no Finder.
- **Manter o acesso aos recursos do sistema de arquivos** -se o aplicativo Xamarin. Mac depender do acesso persistente aos recursos fora de seu contêiner, use indicadores de escopo de segurança para manter o acesso.
- **Criar um item de logon para um aplicativo** -com a área restrita do aplicativo, você não pode criar um item de logon usando `LSSharedFileList` nem pode manipular o estado dos serviços de inicialização usando `LSRegisterURL`. Use a função `SMLoginItemSetEnabled` conforme descrito em maçãs [adicionando itens de logon usando a documentação do Service Management Framework](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1) .
- **Acessando dados do usuário** – se você estiver usando funções POSIX, como `getpwuid` para obter o diretório base do usuário dos serviços de diretório, considere o uso de símbolos Cocoa ou Core Foundation, como `NSHomeDirectory`.
- **Acessando as preferências de outros aplicativos** – como a área restrita do aplicativo direciona APIs que localizam caminhos para o contêiner do aplicativo, a modificação de preferências ocorre dentro desse contêiner e o acesso a outras preferências de aplicativos não é permitido.
- **Usando o vídeo inserido do HTML5 em exibições da Web** – se o aplicativo Xamarin. Mac usar WebKit para reproduzir vídeos HTML5 incorporados, você também deverá vincular o aplicativo à estrutura do AV Foundation. Caso contrário, a área restrita do aplicativo impedirá que o CoreMedia reproduza esses vídeos.

### <a name="applying-required-app-sandbox-entitlements"></a>Aplicando os direitos necessários de área restrita do aplicativo

Você precisará editar os direitos de qualquer aplicativo Xamarin. Mac que deseja executar na área restrita do aplicativo e marcar a caixa de seleção **habilitar a área restrita do aplicativo** .

Com base na funcionalidade do aplicativo, talvez seja necessário habilitar outros direitos de acesso aos recursos ou recursos do sistema operacional. A área restrita do aplicativo funciona melhor quando você minimiza os direitos que você solicita ao mínimo necessário para executar seu aplicativo, portanto, basta habilitar os direitos aleatoriamente.

Para determinar quais direitos um aplicativo Xamarin. Mac exige, faça o seguinte:

1. Habilite a área restrita do aplicativo e execute o aplicativo Xamarin. Mac.
2. Execute os recursos do aplicativo.
3. Abra o aplicativo de console (disponível em `/Applications/Utilities`) e procure `sandboxd` violações no log **todas as mensagens** .
4. Para cada violação de `sandboxd`, resolva o problema usando o contêiner do aplicativo em vez de outros locais do sistema de arquivos ou aplique os direitos de área restrita do aplicativo para habilitar o acesso a recursos restritos do sistema operacional.
5. Execute novamente e teste todos os recursos do aplicativo Xamarin. Mac.
6. Repita até que todas as violações de `sandboxd` tenham sido resolvidas.

### <a name="add-privilege-separation-using-xpc"></a>Adicionar separação de privilégios usando XPC

Ao desenvolver um aplicativo Xamarin. Mac para a área restrita do aplicativo, examine os comportamentos do aplicativo nos termos de privilégios e acesso e, em seguida, considere separar operações de alto risco em seus próprios serviços XPCs.

Para obter mais informações, consulte o guia de programação [criando serviços de XPC](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6) e [daemons e serviços](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)da Apple.

### <a name="implement-a-migration-strategy"></a>Implementar uma estratégia de migração

Se você estiver lançando uma nova versão em área restrita de um aplicativo Xamarin. Mac que não estava anteriormente em área restrita, você precisará garantir que os usuários atuais tenham um caminho de atualização suave.

 Para obter detalhes sobre como implementar um manifesto de migração de contêiner, leia [a documentação migrando um aplicativo da Apple para uma área restrita](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1) .

## <a name="summary"></a>Resumo

Este artigo fez uma visão detalhada da área restrita de um aplicativo Xamarin. Mac. Primeiro, criamos um aplicativo somente Xamarin. Mac para mostrar os conceitos básicos da área restrita do aplicativo. Em seguida, mostramos como resolver violações de área restrita. Em seguida, analisamos detalhadamente a área restrita do aplicativo e, por fim, examinamos a criação de um aplicativo Xamarin. Mac para a área restrita do aplicativo.

## <a name="related-links"></a>Links relacionados

- [Publicando na App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [Sobre a área restrita do aplicativo](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [Problemas comuns de área restrita do aplicativo](https://developer.apple.com/library/content/qa/qa1773/_index.html)
