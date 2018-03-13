---
title: Conectando-se ao Mac
description: "O Xamarin.iOS para Visual Studio permite aos desenvolvedores criar, compilar e depurar aplicativos iOS em um computador com Windows usando o IDE do Visual Studio. Este guia explica os recursos fornecidos pelo Xamarin.iOS para Visual Studio e como a conexão ao host de build do Mac é feita."
ms.topic: article
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: c60927593f062c8ac9694d889ffbf581c09bab82
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-to-the-mac"></a>Conectando-se ao Mac

_O Xamarin.iOS para Visual Studio permite aos desenvolvedores criar, compilar e depurar aplicativos iOS em um computador com Windows usando o IDE do Visual Studio. Este guia explica os recursos fornecidos pelo Xamarin.iOS para Visual Studio e como a conexão ao host de build do Mac é feita._

O Visual Studio conecta-se ao Mac via SSH, o que proporciona vários benefícios, incluindo:

- O Visual Studio pode inicializar e controlar o agente de build diretamente. Não há mais um aplicativo visível ao usuário que exija inicialização e parada manuais.

- O novo Gerenciador de Conexões no Visual Studio descobrirá, autenticará e se lembrará do host de build do Mac.

- Uma vez que toda a comunicação é encapsulada com segurança via SSH, apenas uma conexão passa por um túnel seguro via SSH, apenas uma única conexão de porta a porta 22 é necessária.

- O Visual Studio é notificado das alterações assim que elas ocorrem. Por exemplo, quando um dispositivo iOS é conectado à barra de ferramentas, ele será atualizado imediatamente.

- Várias instâncias do Visual Studio podem se conectar simultaneamente.

- A conexão não interferirá no desenvolvimento. Apenas solicitará uma conexão ao Mac ao realizar uma operação para a qual o Mac seja necessário, como depuração ou uso do Designer do iOS.

A conexão ao Mac é composta por vários processos para as diferentes partes de sua funcionalidade – por exemplo, o agente de designer do iOS e o agente de build – que são controlados por um agente. Esse agente é controlado e atualizado pelo Visual Studio e reiniciará qualquer um dos processos independentes automaticamente caso eles falhem.

O diagrama a seguir mostra uma visão geral simples do fluxo de trabalho de desenvolvimento do Xamarin.iOS:

[![Fluxo de trabalho do desenvolvimento do iOS](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
>  Na verdade, o Visual Studio inicia um processo separado do MSBuild para compilar os projetos. Esse processo cria uma nova conexão com o Mac, significando que, na verdade, há duas conexões SSH do Windows com o Mac no build do Visual Studio. Compilar usando a [linha de comando](#commandline) apenas cria o único processo MSBuild. Para simplificar esse diagrama, todas as conexões são simplesmente representadas por uma seta.

## <a name="requirements"></a>Requisitos

O Xamarin.iOS para Visual Studio realiza um feito incrível: permite aos desenvolvedores criar, compilar e depurar aplicativos iOS em um computador com Windows usando o IDE do Visual Studio. Ele não pode fazer isso sozinho – aplicativos iOS não podem ser criados sem o compilador da Apple e não podem ser implantados sem os certificados e as ferramentas de assinatura de código da Apple. Isso significa que a instalação do Xamarin.iOS para Visual Studio requer uma conexão com um computador em rede Mac OS X (chamado de *host* ou *host de build*) para executar essas tarefas para você. Uma vez configuradas, as ferramentas da Xamarin tornarão o processo mais direto possível.

### <a name="system-requirements"></a>Requisitos de sistema

Os requisitos de sistema podem ser encontrados no guia [Como instalar o Xamarin.iOS no Windows](~/ios/get-started/installation/windows/index.md#system-requirements)


#### <a name="compatibility"></a>Compatibilidade

> [!IMPORTANT]
>  O computador Windows deve estar usando a mesma versão do Xamarin.iOS que o Mac ao qual está conectado. Para garantir que isso seja verdadeiro:                                                    
>                                                                                                                 
> - **Visual Studio 2015 e anterior**: verifique se você está no mesmo [canal de atualizações](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) que o Visual Studio para Mac.
>                                                                                                                 
> - **Visual Studio 2017, Versão de Lançamento**: verifique se você está no canal **Estável** do Visual Studio para Mac.
>                                                                                                                 
> - **Visual Studio 2017, Versão de Visualização**: verifique se você está no canal **Alfa** do Visual Studio para Mac. o Visual Studio não verificará se a SDK do Xamarin.iOS e o Xcode existem e têm versões compatíveis.
>   Eles serão verificados pelo agente de build, resultando em erros de build; e pelo agente do Designer do iOS, resultando em erros do designer.

### <a name="connecting-to-the-mac"></a>Conectando-se ao Mac

#### <a name="mac-setup"></a>Configuração do Mac

Para configurar o host do Mac, você deve habilitar a comunicação entre a extensão do Xamarin para Visual Studio e seu Mac. Para fazer isso, permita **Logon Remoto** em seu Mac seguindo as etapas abaixo:

1. Abra *Destaque* (**⌘-Espaço**), pesquise *Acesso Remoto* e, em seguida, selecione o resultado *Compartilhamento*. Isso abrirá as *Preferências do Sistema* no painel *Compartilhamento*:

   [![Pesquisa do Spotlight para acesso remoto](images/spotlight.png)](images/spotlight.png#lightbox)

2. Marque a opção *Acesso Remoto* na lista *Serviço* à esquerda para permitir que o Xamarin para Visual Studio se conecte ao Mac:

   [![Marque a opção Acesso Remoto na Lista de serviços](images/sharing.png)](images/sharing.png#lightbox)

3. Verifique se o *Logon Remoto* está definido para permitir o acesso de *Todos* os usuários ou se o nome de usuário ou grupo do Mac está incluído na lista de usuários permitidos à direita.

Além disso, se você tiver o firewall do OS X definido para bloquear aplicativos assinados por padrão, poderá ser preciso permitir ao `mono-sgen` receber conexões de entrada. Uma caixa de diálogo de alerta será exibida para avisá-lo, se for o caso.

Desde que haja uma sessão atual aberta em seu Mac, ela agora deverá ser detectável pelo Visual Studio se estiver na mesma rede.

O Visual Studio iniciará e interromperá o agente em seu Mac, portanto, não há nada mais que você, como usuário, precise executar.

### <a name="windows-setup"></a>Instalação do Windows

Certifique-se de [instalar](~/ios/get-started/installation/windows/index.md) as ferramentas do Xamarin em seu computador Windows.

### <a name="connecting-to-the-mac-build-host"></a>Como conectar-se ao host de build do Mac

Há duas maneiras de se conectar ao host de build do Mac:

Na barra de ferramentas do iOS:

[![A barra de ferramentas do iOS](images/image1.png)](images/image1.png#lightbox)

Ou navegando até **Ferramentas > Opções** no Visual Studio, selecionando **Xamarin > Configurações do iOS** e clicando no botão **Localizar Agente Mac do Xamarin**:

[![Localizando o Agente Mac do Xamarin](images/image2.png)](images/image2.png#lightbox)

Navegar de qualquer forma levará à caixa de diálogo **Agente do Mac**, ilustrada abaixo:

[![A caixa de diálogo Agente do Mac](images/image3.png)](images/image3.png#lightbox)

Isso exibirá uma lista de todos os computadores conectados anteriormente e armazenados como computadores conhecidos ou computadores disponíveis para *Logon Remoto*.

Selecione um Mac clicando duas vezes nele para se conectar a ele. Na primeira vez que se conectar a um Mac, será solicitado que você insira suas credenciais de usuário do Mac para permitir a conexão remota:

[![Insira as credenciais de usuário do Mac](images/image4.png)](images/image4.png#lightbox)

O agente usará essas credenciais para criar uma nova conexão SSH com Mac. Se tiver êxito, uma chave SSH será criada e será [registrada](#commandline) no arquivo `authorized_keys` no Mac. Em conexões subsequentes, o agente usará o arquivo de nome de usuário e o arquivo de chave para se conectar ao host de build conhecido conectado mais recentemente.

> [!NOTE]
>  **Observação**: você deve usar o _nome de usuário_ e não o _nome completo_, ao inserir suas credenciais.  Você pode descobrir isso usando o comando `whoami` no Terminal.  Por exemplo, na captura de tela abaixo, o nome da conta será **amyb** e não **Amy Burns**:
>
> ![Encontrando o nome de usuário no aplicativo Terminal](images/image5.png)

Quando uma conexão for estabelecida com êxito, ela será exibida na caixa de diálogo Seleção de Host com um ícone **conectado** ao lado, conforme ilustrado abaixo:

[![A caixa de diálogo Seleção de Host com um ícone conectado ao lado](images/image6.png)](images/image6.png#lightbox)

Pode haver apenas um Mac conectado a qualquer momento.

Cada computador na lista, esteja conectado ou não, exibirá um menu de contexto ao clicar com o botão direito do mouse, permitindo **Conectar**, **Desconectar** ou **Esquecer este Mac**, conforme necessário:

[![Os menus de contexto Conectar, Desconectar ou Esquecer este Mac](images/image7.png)](images/image7.png#lightbox)

Se você optar por **Esquecer este Mac**, precisará inserir novamente suas credenciais para conectá-lo novamente.

<a name="manual-add"/>

### <a name="manually-adding-a-mac"></a>Como adicionar um Mac manualmente

Em determinadas circunstâncias, você poderá adicionar um Mac manualmente se não localizar o nome mDNS listado na caixa de diálogo Seleção de Host. Para fazer isso, siga as etapas abaixo:

1. Localize o endereço IP de seu Mac acessando **Preferências do Sistema > Compartilhamento > Logon Remoto** no Mac:

   [![O endereço IP do Mac nas Preferências do Sistema](images/image8.png)](images/image8.png#lightbox)

   Ou, se você preferir usar a linha de comando, poderá localizar seu endereço IP digitando `ipconfig getifaddr en0` no Terminal (observe que, dependendo do tipo de conexão, a variável pode ser `en1`, `en2`, etc.):

   [![O endereço IP no aplicativo Terminal](images/image9.png)](images/image9.png#lightbox)

2. Retorne ao Visual Studio e, na caixa de diálogo Seleção de Host, selecione **Adicionar Mac...**:

   [![A caixa de diálogo Seleção de Host](images/image10.png)](images/image10.png#lightbox)

3. Insira o endereço IP do Mac na caixa de diálogo Adicionar Mac e clique em **Adicionar**:

   [![Insira o endereço IP do Mac na caixa de diálogo Adicionar Mac](images/image11.png)](images/image11.png#lightbox)

4. Por fim, insira o nome de usuário (não o nome completo) de sua conta de administrador do Mac e a senha correspondente:

   [![Insira o nome de usuário e a senha](images/image12.png)](images/image12.png#lightbox)

Após clicar em **Logon**, o Visual Studio fará logon no computador Mac usando o SSH e adicionará esse Mac como um computador conhecido.

<a name="commandline"/>

### <a name="command-line-support"></a>Suporte de linha de comando

O agente também dá suporte à criação de uma configuração do Xamarin.iOS usando a linha de comando.  Para usá-lo, você precisará passar os seguintes parâmetros obrigatórios para MSBuild:

- `ServerAddress` – O endereço IP do servidor Mac.

- `ServerUser` – O nome de usuário (não o nome completo) a ser usado para fazer logon no servidor Mac.

- `ServerPassword` – A senha usada para fazer logon no host do Mac (opcional).

O `ServerPassword` parâmetro não é obrigatório.

Em vez disso, na primeira vez que uma senha for passada, seja usando o Visual Studio ou a Linha de Comando para essa configuração específica do Windows, Mac e usuário, um par de chaves será gerado e armazenado no computador Windows para uso futuro. Ele ficará localizado em **%localappdata%\Xamarin\MonoTouch\id_rsa**.  Se você não passar o parâmetro `ServerPassword`, o keyfile `id_rsa` será usado para autenticação.

Um exemplo de comando para conectar-se ao Mac 10.211.55.2 usando a conta **xamUser** com senha **mypassword** é mostrado abaixo:

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

### <a name="summary"></a>Resumo

Este artigo abordou a conexão entre o Visual Studio e as ferramentas de build e designer do iOS no Mac, permitindo compilar aplicativos Xamarin.iOS usando Visual Studio.

### <a name="related-links"></a>Links relacionados

- [Instalação](~/ios/get-started/installation/windows/index.md)
- [Solução de problemas de conexão](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Conectar um Mac ao seu ambiente do Visual Studio com XMA (vídeo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
