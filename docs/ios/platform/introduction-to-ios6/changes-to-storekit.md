---
title: Alterações no StoreKit no iOS 6
description: 'o iOS 6 apresenta duas alterações na API do kit de loja: a capacidade de exibir produtos iTunes (e App Store/eróticos da iBookstore) de dentro de seu aplicativo e uma nova opção de compra no aplicativo, na qual a Apple hospedará seus arquivos baixáveis. Este documento explica como implementar esses recursos com o Xamarin. iOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 4197dfb38ac6118d20da2b87d0c686558d77b0f6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281568"
---
# <a name="changes-to-storekit-in-ios-6"></a>Alterações no StoreKit no iOS 6

_o iOS 6 introduziu duas alterações na API do kit de loja: a capacidade de exibir produtos iTunes (e App Store/eróticos da iBookstore) de dentro de seu aplicativo e uma nova opção de compra no aplicativo, na qual a Apple hospedará seus arquivos baixáveis. Este documento explica como implementar esses recursos com o Xamarin. iOS._

As principais alterações no kit de armazenamento no iOS6 são esses dois novos recursos:

- **Exibição de conteúdo no aplicativo & compra** – os usuários podem comprar e baixar aplicativos, música, livros e outros conteúdos do iTunes sem sair de seu aplicativo. Você também pode vincular a seus próprios aplicativos para promover a compra ou apenas incentivar análises e classificações.
- **Conteúdo hospedado de compra no aplicativo** – a Apple armazenará e fornecerá o conteúdo associado aos seus produtos de compra no aplicativo, o que elimina a necessidade de um servidor separado para hospedar seus arquivos, oferece suporte automático ao download em segundo plano e permite que você escreva menos código.

Consulte os guias de [compra no aplicativo](~/ios/platform/in-app-purchasing/index.md) para obter uma cobertura detalhada das APIs do StoreKit.

## <a name="requirements"></a>Requisitos

Os recursos do kit de loja discutidos neste documento exigem o iOS 6 e o Xcode 4,5, juntamente com o Xamarin. iOS 6,0.

## <a name="in-app-content-display--purchasing"></a>Exibição de conteúdo no aplicativo & compra

O novo recurso de compra no aplicativo no iOS permite que os usuários exibam informações do produto e comprem ou baixem o produto de dentro de seu aplicativo.
Anteriormente, os aplicativos teriam de disparar o iTunes, a App Store ou o eróticos da iBookstore, o que resultaria no usuário deixar o aplicativo original. Esse novo recurso retorna automaticamente o usuário para seu aplicativo quando eles são concluídos.

[![](changes-to-storekit-images/image1.png "Retornando automaticamente para um aplicativo após a compra")](changes-to-storekit-images/image1.png#lightbox)

Exemplos de como isso pode ser usado incluem:

- **Incentivando os usuários a classificarem seu aplicativo** – você pode abrir a página da loja de aplicativos para que o usuário possa classificar e examinar seu aplicativo sem deixá-lo.
- **Aplicativos de promoção cruzada** – permite que o usuário veja outros aplicativos que você publica, com a capacidade de comprar/baixar imediatamente.
- **Ajudando os usuários a localizar e baixar conteúdo** – ajudar os usuários a comprar conteúdo que seu aplicativo encontra, gerencia ou agregações (por exemplo, um aplicativo relacionado à música pode fornecer uma lista de reprodução de músicas e permitir que cada música seja comprada de dentro do aplicativo).

Depois que `SKStoreProductViewController` o tiver sido exibido, o usuário poderá interagir com as informações do produto como se estivesse no iTunes, na loja de aplicativos ou no eróticos da iBookstore. O usuário pode:

- Exibir capturas de tela (para aplicativos),
- Vídeo ou músicas de exemplo (para música, programas de TV e filmes),
- Ler (e gravar) revisões,
- Compre & download, que ocorre inteiramente dentro do controlador de exibição e do kit de armazenamento.

Algumas opções dentro do `SKStoreProductViewController` ainda forçarão o usuário a deixar seu aplicativo e abrir o aplicativo de loja relevante, como clicar em **produtos relacionados** ou no link de **suporte** de um aplicativo.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

A API para mostrar um produto em qualquer aplicativo é simples: requer apenas que você crie e exiba um `SKStoreProductViewController`. Siga estas etapas para criar e mostrar um produto:

1. Crie um `StoreProductParameters` objeto para passar parâmetros para o controlador de exibição, incluindo `productId` o no construtor.
1. Crie uma `SKProductViewController`instância do. Atribua-o a um campo de nível de classe.
1. Atribua um manipulador ao evento do `Finished` controlador de exibição, que deve ignorar o controlador de exibição. Esse evento é chamado quando o usuário pressiona cancelar; ou, de outra forma, finaliza uma transação dentro do controlador de exibição.
1. Chame o `LoadProduct` método passando o `StoreProductParameters` e um manipulador de conclusão. O manipulador de conclusão deve verificar se a solicitação do produto foi bem-sucedida e, em caso afirmativo `SKProductViewController` , apresentar as modalidades. O tratamento de erros apropriado deve ser adicionado caso o produto não possa ser recuperado.

### <a name="example"></a>Exemplo

O projeto *ProductView* no código de exemplo *StoreKit* para este artigo implementa um `Buy` método que aceita a ID da Apple de qualquer produto e `SKStoreProductViewController`exibe o. O código a seguir exibe as informações do produto para qualquer ID da Apple determinada:

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

O aplicativo se parece com a captura de tela abaixo ao executar – o download ou a `SKStoreProductViewController`compra ocorre inteiramente dentro do:

[![](changes-to-storekit-images/image2.png "O aplicativo tem a seguinte aparência quando executado")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Suporte a sistemas operacionais mais antigos

O aplicativo de exemplo inclui código que mostra como abrir a loja de aplicativos, o iTunes ou o eróticos da iBookstore em versões anteriores do iOS. Use o `OpenUrl` método para abrir uma URL de **iTunes.com** corretamente criada.

Você pode implementar uma verificação de versão para determinar qual código executar, conforme mostrado aqui:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax 
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>Erros

O seguinte erro ocorrerá se a ID da Apple usada não for válida, o que pode ser confuso, pois implica um problema de rede ou de autenticação de algum tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Lendo a documentação do Objective-C

Os desenvolvedores lendo sobre o kit de loja no portal do desenvolvedor da Apple verão um protocolo – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – discutido em relação a esse novo recurso. O protocolo delegado tem apenas um método – productViewControllerDidFinish – que foi exposto como o `Finished` evento `SKStoreProductViewController` no Xamarin. Ios.

## <a name="determining-apple-ids"></a>Determinando IDs da Apple

A ID da Apple exigida `SKStoreProductViewController` pelo é um *número* (não deve ser confundido com IDs de pacote como "com. xamarin. mwc2012"). Há algumas maneiras diferentes de descobrir a ID da Apple para os produtos que você deseja exibir, listados abaixo:

### <a name="itunesconnect"></a>iTunesConnect

Para aplicativos que você publica, é fácil encontrar a ID da **Apple** no iTunes Connect:

[![](changes-to-storekit-images/image3.png "Encontrando a ID da Apple no iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API de pesquisa

A Apple fornece uma API de pesquisa dinâmica para consultar todos os produtos na App Store, no iTunes e no eróticos da iBookstore. Informações sobre como acessar a API de pesquisa podem ser encontradas nos [recursos afiliados da Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), embora a API seja exposta a qualquer pessoa (não apenas afiliadas registradas). O JSON resultante pode ser analisado para descobrir `trackId` que é a ID da Apple a ser usada com `SKStoreProductViewController`o.

Os resultados também incluirão outros metadados, incluindo informações de exibição e URLs do trabalho artístico que podem ser usados para renderizar o produto em seu aplicativo.

Estes são alguns exemplos:

- **aplicativo iBooks** – [ http://itunes.apple.com/search?term=ibooks&amp ; entidade = software&amp; Country = US](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Ponto e Kangaroo iBook** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp ; Entity = livro eletrônico&amp; Country = US](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Feed de parceiro corporativo

A Apple fornece aos parceiros aprovados um despejo de dados completo de todos os seus produtos, na forma de arquivos simples prontos para o banco de dado para download. Se você se qualifica para acesso ao [feed de parceiros corporativos](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html), a ID da Apple para qualquer produto pode ser encontrada nesse conjunto de código.

Muitos usuários do feed de parceiros corporativos são membros do [programa afiliado](http://www.apple.com/itunes/affiliates) que permite que as comissões sejam obtidas em vendas de produtos. `SKStoreProductViewController`o não oferece suporte a IDs de afiliação (no momento da gravação).

### <a name="direct-product-links"></a>Links diretos de produtos

A ID da Apple para um produto pode ser inferida de seu link de URL do iTunes Preview.
Em qualquer link de produto do iTunes (para aplicativos, música ou livros), localize a parte da URL `id` que começa com e use o número a seguir.

Por exemplo, o link direto para iBooks é

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

e a ID da Apple é **364709193**. Da mesma forma para o aplicativo MWC2012, o link direto é

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

e a ID da Apple é **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Conteúdo hospedado de compra no aplicativo

Se suas compras no aplicativo consistirem em conteúdo para download (como livros ou outras mídias, arte e configuração de nível de jogo ou outros arquivos grandes), esses arquivos serão usados para serem hospedados no servidor Web e os aplicativos precisarão incorporar o código para baixá-los com segurança após compras. A partir do iOS 6, a Apple hospedará seus arquivos em seus servidores, eliminando a necessidade de um servidor separado. O recurso está disponível somente para produtos não consumíveis (não consumíveis ou assinaturas). As vantagens de usar o serviço de hospedagem da Apple incluem:

- Economize os custos de largura de banda & de hospedagem.
- Provavelmente mais escalonável do que qualquer host de servidor que esteja sendo usado no momento. 
- Menos código para escrever, já que você não precisa criar nenhum processamento do lado do servidor. 
- O download em segundo plano é implementado para você.

Observação: não há suporte para testar conteúdo de compra no aplicativo hospedado no simulador do iOS, portanto, você deve testar com um dispositivo real.

### <a name="hosted-content-basics"></a>Noções básicas de conteúdo hospedado

Antes do iOS 6, havia duas maneiras de fornecer um produto (descrito mais detalhadamente na documentação de [compra no aplicativo do Xamarin](~/ios/platform/in-app-purchasing/index.md) ):

- **Produtos internos** – recursos que são "desbloqueados" pela compra, mas que são incorporados ao aplicativo (como código ou recursos incorporados). Exemplos de produtos internos incluem filtros de fotos desbloqueados ou Power ups em jogo.
- **Produtos entregues pelo servidor** – após a compra, o aplicativo deve baixar o conteúdo de um servidor que você opera. Esse conteúdo é baixado durante a compra, armazenado no dispositivo e, em seguida, renderizado como parte do fornecimento do produto. Os exemplos incluem livros, problemas da revista ou níveis de jogos que consistem em arquivos de configuração e arte em segundo plano.

No iOS 6, a Apple oferece uma variação de produtos entregues por servidor: eles hospedarão seus arquivos de conteúdo em seus servidores. Isso torna muito mais simples a criação de produtos entregues por servidor, pois não é necessário operar um servidor separado, e o kit de armazenamento fornece a funcionalidade de download em segundo plano que você tinha que escrever anteriormente. Para aproveitar a hospedagem da Apple, habilite a hospedagem de conteúdo para novos produtos de compra no aplicativo e modifique seu código do kit de loja para tirar proveito dele. Os arquivos de conteúdo do produto são então criados usando o Xcode e carregados nos servidores da Apple para revisão e liberação.

[![](changes-to-storekit-images/image4.png "O processo de Build e entrega")](changes-to-storekit-images/image4.png#lightbox)

Usar a App Store para fornecer compras no aplicativo *com conteúdo hospedado* requer a seguinte configuração e configuração:

- **iTunes Connect** – você *deve* ter fornecido suas informações bancárias e fiscais para a Apple para que elas possam remeter os fundos coletados em seu nome. Em seguida, você pode configurar produtos para vender e configurar contas de usuário de área restrita para testar a compra.  _Você também deve configurar o conteúdo hospedado para os produtos não consumíveis que você deseja hospedar com a Apple_.
- **portal de provisionamento do IOS** – criando um identificador de pacote e habilitando o acesso à App Store para seu aplicativo, como você faria para qualquer aplicativo que dê suporte à compra no aplicativo.
- **Kit de loja** – adicionando código ao seu aplicativo para exibir produtos, comprar produtos e restaurar transações.  _No kit de armazenamento do iOS 6 também gerenciará o download do conteúdo do produto, em segundo plano, com atualizações de progresso._
- **Código personalizado** – para controlar as compras feitas pelos clientes e fornecer os produtos ou serviços que eles compraram. Utilize novas classes do kit de armazenamento do `SKDownload` Ios 6 como para recuperar o conteúdo hospedado pela Apple.

As seções a seguir explicam como implementar conteúdo hospedado, desde a criação e o carregamento do pacote até o gerenciamento do processo de compra e de download, usando o código de exemplo deste artigo.

### <a name="sample-code"></a>Código de exemplo

O projeto de exemplo *HostedNonConsumables* (em StoreKitiOS6. zip) usa conteúdo hospedado. O aplicativo oferece dois "capítulos de livros" para venda, o conteúdo para o qual é hospedado nos servidores da Apple. O conteúdo consiste em um arquivo de texto e uma imagem, embora um conteúdo muito mais complexo possa ser usado em um aplicativo real.

O aplicativo tem esta aparência antes, durante e após uma compra:

 [![](changes-to-storekit-images/image5.png "O aplicativo tem esta aparência antes, durante e após uma compra")](changes-to-storekit-images/image5.png#lightbox)

O arquivo de texto e a imagem são baixados e copiados para o diretório de documentos do aplicativo. Para obter mais informações sobre os diferentes diretórios disponíveis para o armazenamento de aplicativos, consulte a [documentação do sistema de arquivos](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>Conexão do iTunes

Ao criar novos produtos que usarão a hospedagem de conteúdo da Apple, certifique-se de selecionar o tipo de produto **não consumível** . Outros tipos de produtos não dão suporte à Hospedagem de conteúdo. Além disso, você não deve habilitar a hospedagem de conteúdo para os produtos *existentes* que vende; Ative apenas a hospedagem de conteúdo para novos produtos.

 [![](changes-to-storekit-images/image6.png "Selecione o tipo de produto não consumível")](changes-to-storekit-images/image6.png#lightbox)

Insira uma **ID de produto**. Essa ID será necessária mais tarde quando você criar o conteúdo para este produto.

 [![](changes-to-storekit-images/image7.png "Insira uma ID de produto")](changes-to-storekit-images/image7.png#lightbox)

A hospedagem de conteúdo é definida na seção detalhes. Antes de a compra no aplicativo ficar ativa, desmarque a caixa de seleção **conteúdo do host com Apple** se você quiser cancelar (mesmo que tenha carregado algum conteúdo de teste). No entanto, a hospedagem de conteúdo não pode ser removida depois que a compra no aplicativo tiver ficado em tempo real.

 [![](changes-to-storekit-images/image8.png "Hospedando conteúdo com a Apple")](changes-to-storekit-images/image8.png#lightbox)

Depois de ativar o conteúdo de hospedagem, o produto entrará em **espera pelo status de carregamento** e mostrará esta mensagem:

 [![](changes-to-storekit-images/image9.png "O produto entrará em espera pelo status de carregamento e mostrará esta mensagem")](changes-to-storekit-images/image9.png#lightbox)

O pacote de conteúdo deve ser criado com o Xcode e carregado usando a ferramenta de arquivamento. As instruções para criar pacotes de conteúdo são fornecidas na próxima seção **criando. Arquivos PKG**.

## <a name="creating-pkg-files"></a>Criar. Arquivos PKG

Os arquivos de conteúdo que você carrega na Apple devem atender às seguintes restrições:

- Não pode exceder 2 GB de tamanho.
- Não pode conter código executável (ou symlinks que aponta para fora do conteúdo).
- Deve ser formatado corretamente (incluindo um arquivo **. plist** ) e ter uma extensão de arquivo **. pkg** . Isso será feito automaticamente se você seguir estas instruções usando o Xcode.

Você pode adicionar vários arquivos diferentes e tipos de arquivos, desde que eles atendam a essas restrições. O conteúdo é compactado antes da entrega ao seu aplicativo e descompactado pelo kit de armazenamento antes de seu código acessá-lo.

Depois de carregar um pacote de conteúdo, ele pode ser substituído por conteúdo mais recente. O novo conteúdo deve ser carregado e enviado para revisão/aprovação por meio do processo normal. Incrementar `ContentVersion` o campo em pacotes de conteúdo atualizados para indicar que ele é mais recente.

### <a name="xcode-in-app-purchase-content-projects"></a>Projetos de conteúdo de compra no aplicativo Xcode

A criação de pacotes de conteúdo para produtos de compra no aplicativo atualmente requer o Xcode. Não há nenhuma codificação OBJECtive-C necessária; O Xcode tem um novo tipo de projeto para esses pacotes que contém apenas seus arquivos e um plist.

Nosso aplicativo de exemplo tem capítulos de livros para venda – cada pacote de conteúdo do capítulo conterá:

- um arquivo de texto e
- uma imagem para representar o capítulo.


Comece selecionando **arquivo > novo projeto** no menu e escolhendo **conteúdo de compra no aplicativo**:

 [![](changes-to-storekit-images/image10.png "Escolher conteúdo de compra no aplicativo")](changes-to-storekit-images/image10.png#lightbox)

Insira o **nome do produto** e o **identificador da empresa** de modo que o **identificador do pacote** corresponda à ID do **produto** que você inseriu no iTunes Connect para este produto.

[![](changes-to-storekit-images/image11.png "Insira o nome e o identificador")](changes-to-storekit-images/image11.png#lightbox)

Agora você terá um projeto **de conteúdo de compra no aplicativo** em branco. Você pode clicar com o botão direito e **Adicionar arquivos...** ou arraste-os para o **navegador do projeto**. Verifique se o **ContentVersion** está correto (ele deve começar em 1,0, mas se você optar por atualizar o conteúdo mais tarde, lembre-se de incrementar).

Esta captura de tela mostra o Xcode com os arquivos de conteúdo incluídos no projeto e as entradas do plist visíveis na janela principal:

[![](changes-to-storekit-images/image12.png "Esta captura de tela mostra o Xcode com os arquivos de conteúdo incluídos no projeto e as entradas do plist visíveis na janela principal")](changes-to-storekit-images/image12.png#lightbox)

Depois de adicionar todos os arquivos de conteúdo, você pode salvar este projeto e editá-lo novamente mais tarde, ou iniciar o processo de carregamento.

## <a name="uploading-pkg-files"></a>Carregar. Arquivos PKG

A maneira mais fácil de carregar pacotes de conteúdo é com a **ferramenta de arquivamento Xcode**. Escolha **produto > arquivo morto** no menu para começar:

![](changes-to-storekit-images/image13.png "Escolha arquivamento")

O pacote de conteúdo será exibido no arquivo, conforme mostrado abaixo. O tipo e o ícone de arquivamento mostram essa linha é um **arquivo de conteúdo de compra no aplicativo**. Clique em **validar...** para verificar se há erros no nosso pacote de conteúdo sem realmente executar o carregamento.

[![](changes-to-storekit-images/image14.png "Validar o pacote")](changes-to-storekit-images/image14.png#lightbox)

Faça logon com suas credenciais do iTunes Connect:

[![](changes-to-storekit-images/image15.png "Faça logon com suas credenciais do iTunes Connect")](changes-to-storekit-images/image15.png#lightbox)

Escolha o aplicativo correto e a compra no aplicativo para associar este conteúdo a:

[![](changes-to-storekit-images/image16.png "Escolha o aplicativo correto e a compra no aplicativo para associar esse conteúdo")](changes-to-storekit-images/image16.png#lightbox)

Você deverá ver uma mensagem como esta captura de tela:

![Um exemplo de mensagem sem problemas](changes-to-storekit-images/image17.png "Um exemplo de mensagem sem problemas")

Agora, passe por um processo semelhante, mas clicando em **distribuir...** na verdade, carregará o conteúdo.

[![Distribuir o aplicativo](changes-to-storekit-images/image18.png "Distribuir o aplicativo")](changes-to-storekit-images/image18.png#lightbox)

Selecione a primeira opção para carregar o conteúdo:

![Carregar o conteúdo](changes-to-storekit-images/image19.png "Carregar o conteúdo")

Entrar novamente:

[![](changes-to-storekit-images/image15.png "Logon em")](changes-to-storekit-images/image15.png#lightbox)

Escolha o aplicativo correto e o registro de compra no aplicativo para carregar o conteúdo:

[![](changes-to-storekit-images/image20.png "Escolha o aplicativo e o registro de compra no aplicativo")](changes-to-storekit-images/image20.png#lightbox)

Aguarde enquanto os arquivos são carregados:

[![](changes-to-storekit-images/image21.png "A caixa de diálogo de carregamento de conteúdo")](changes-to-storekit-images/image21.png#lightbox)

Quando o upload for concluído, uma mensagem será exibida para avisá-lo que o conteúdo foi enviado para a loja de aplicativos.

[![](changes-to-storekit-images/image22.png "Um exemplo de mensagem de upload bem-sucedido")](changes-to-storekit-images/image22.png#lightbox)

Depois que isso for feito, quando você retornar para a página do produto no iTunes Connect, ele mostrará os detalhes do pacote e estará **pronto para enviar** o status. Quando o produto estiver nesse status, você poderá começar a testar no ambiente de área restrita. Você não precisa enviar o produto para teste na área restrita.

[![](changes-to-storekit-images/image23.png "o iTunes Connect irá mostrar os detalhes do pacote e estar pronto para enviar o status")](changes-to-storekit-images/image23.png#lightbox)

Pode levar algum tempo (por exemplo, alguns minutos) entre o carregamento do arquivo morto e o status do iTunes Connect sendo atualizado. Você pode enviar o produto para revisão separadamente ou enviá-lo em conjunto com um binário de aplicativo. Somente após a Apple ter aprovado oficialmente o conteúdo, ele estará disponível na loja de aplicativos de produção para compra em seu aplicativo.

### <a name="pkg-file-format"></a>Formato de arquivo PKG

Usar o Xcode e a ferramenta de arquivamento para criar e carregar um pacote de conteúdo hospedado significa que você nunca vê o conteúdo do pacote em si. Os arquivos e diretórios nos pacotes criados para o aplicativo de exemplo são parecidos com a captura de tela abaixo, com o arquivo **plist** na raiz e os arquivos do produto em um subdiretório de **conteúdo** :

[![](changes-to-storekit-images/image24.png "O arquivo plist na raiz e os arquivos do produto em um subdiretório de conteúdo")](changes-to-storekit-images/image24.png#lightbox)

Observe a estrutura de diretório do pacote (especialmente o local dos arquivos no `Contents` subdiretório), pois você precisará entender essas informações para extrair os arquivos do pacote no dispositivo.

### <a name="updating-package-content"></a>Atualizando conteúdo do pacote

O procedimento para atualizar o conteúdo depois que ele foi aprovado:

- Edite o projeto de conteúdo de compra no aplicativo no Xcode.
- Número da versão do tapa.
- Carregar para o iTunes Connect novamente. Os compradores subsequentes receberão automaticamente a versão mais recente, mas os usuários que já tiverem a versão antiga não receberão nenhuma notificação.
- Seu aplicativo é responsável por notificar os usuários e incentiva-los a recuperar uma versão mais recente do conteúdo. O aplicativo também deve criar uma função que baixe a nova versão, usando o recurso restaurar do Store Kit.
- Para determinar se existe uma versão mais recente, você pode criar um recurso em seu aplicativo para buscar SKProducts (por exemplo, mesmo processo que é usado para recuperar os preços do produto) e comparar a propriedade ContentVersion.

## <a name="purchasing-overview"></a>Visão geral da compra

Antes de ler esta seção, examine a [documentação de compra no aplicativo](~/ios/platform/in-app-purchasing/index.md)existente.

A sequência de eventos que ocorre quando um produto com conteúdo hospedado é comprado e o download é ilustrado neste diagrama:

[![](changes-to-storekit-images/image25.png "A sequência de eventos que ocorre quando um produto com conteúdo hospedado é comprado e baixado")](changes-to-storekit-images/image25.png#lightbox)

1. Novos produtos podem ser criados no iTunes Connect com conteúdo hospedado habilitado. O conteúdo real é construído separadamente no Xcode (como simplesmente arrastar arquivos para uma pasta) e, em seguida, arquivado e carregado no iTunes (nenhuma codificação é necessária). Em seguida, cada produto é enviado para aprovação, após o qual ele fica disponível para compra. No código de exemplo, essas IDs de produto são codificadas, mas a hospedagem de conteúdo com a Apple será mais flexível se você armazenar a lista de produtos disponíveis em um servidor remoto para que ele possa ser atualizado quando você enviar novos produtos e conteúdo ao iTunes Connect.
1. Quando o usuário adquire um produto, uma transação é colocada na fila de pagamento para processamento.
1. O kit de loja encaminha a solicitação de compra para servidores iTunes para processamento.
1. A transação é concluída nos servidores iTunes (por exemplo, o cliente é cobrado) e um recebimento é retornado para o aplicativo, com informações do produto anexadas, incluindo se é baixável (e, nesse caso, o tamanho do arquivo e outros metadados).
1. Seu código deve verificar se o produto pode ser baixado e, em caso afirmativo, fazer uma solicitação de download de conteúdo que também é colocada na fila de pagamento. O kit de loja envia essa solicitação para os servidores iTunes.
1. O servidor retorna o arquivo de conteúdo para o kit de armazenamento, que fornece um retorno de chamada para retornar o progresso do download e as estimativas de tempo restantes para seu código.
1. Depois de concluído, você receberá uma notificação e passará um local de arquivo na pasta de cache.
1. Seu código deve copiar os arquivos e verificá-los, salvar qualquer estado de que você precise se lembrar de que o produto foi comprado. Aproveite esta oportunidade para definir o sinalizador de backup corretamente nos novos arquivos (dica: se eles vierem de um servidor e nunca forem editados pelo usuário, você provavelmente deverá ignorar o backup deles, pois o usuário sempre poderá recuperá-los dos servidores da Apple no futuro).
1. Chame FinishTransaction. Essa etapa é importante, pois remove a transação da fila de pagamentos. Também é importante que você não chame FinishTransaction até depois de ter copiado o conteúdo do diretório de cache. Depois de chamar FinishTransaction, os arquivos armazenados em cache provavelmente serão limpos rapidamente.

## <a name="implementing-hosted-content-purchase"></a>Implementando a compra de conteúdo hospedado

As informações a seguir devem ser lidas em conjunto com a documentação completa de [compras no aplicativo](~/ios/platform/in-app-purchasing/index.md). As informações neste documento se concentram nas diferenças entre o conteúdo hospedado e a implementação anterior.

### <a name="classes"></a>Classes

As seguintes classes foram adicionadas ou alteradas para dar suporte ao conteúdo hospedado no iOS 6:

- **SKDownload** – nova classe que representa um download em andamento. A API permite mais de um por produto, no entanto, inicialmente, apenas um foi implementado.
- **SKProduct** – novas propriedades adicionadas `Downloadable`: `ContentVersion`, `ContentLengths` , matriz.
- **SKPaymentTransaction** – nova propriedade adicionada: `Downloads`, que contém uma coleção de `SKDownload` objetos se este produto tem conteúdo hospedado disponível para download.
- **SKPaymentQueue** – novo método adicionado: `StartDownloads`. Chame esse método com `SKDownload` objetos para buscar seu conteúdo hospedado. O download pode ocorrer em segundo plano.
- **SKPaymentTransactionObserver** – novo método: `UpdateDownloads`. O kit de loja chama esse método com informações de progresso sobre as operações de download atuais.

Detalhes da nova `SKDownload` classe:

- **Progresso** – um valor entre 0-1 que você pode usar para exibir um indicador de porcentagem concluída para o usuário. Não use Progress = = 1 para detectar se o download está concluído, verifique o estado = = concluído.
- **Timecontinueing** – estimativa do tempo de download restante, em segundos. -1 significa que ainda está calculando a estimativa.
- **Estado** – ativo, aguardando, concluído, falha, pausado, cancelado.
- **ContentURL** – local do arquivo em que o conteúdo foi colocado no disco, `Cache` no diretório. Preenchido apenas depois que o download for concluído.
- **Erro** – Verifique essa propriedade se o estado falhar.

As interações entre as classes no código de exemplo são mostradas neste diagrama (o código específico para compras de conteúdo hospedado é mostrado em verde):

[![](changes-to-storekit-images/image26.png "As compras de conteúdo hospedado são mostradas em verde neste diagrama")](changes-to-storekit-images/image26.png#lightbox)

O código de exemplo em que essas classes foram usadas é mostrado no restante desta seção:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Altere a substituição `UpdatedTransactions` existente para verificar se há conteúdo para download e chame `StartDownloads` se necessário:

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

O novo método `UpdatedDownloads` substituído é mostrado abaixo. O kit de `UpdatedTransactions`loja chama esse `StartDownloads` método depois que é disparado. Esse método é chamado *várias vezes* em intervalos indeterminados para fornecer o progresso do download e, em seguida, novamente quando o download for concluído. Observe que o método aceita uma matriz `SKDownload` de objetos, de modo que cada chamada de método pode fornecer o status de vários downloads na fila. Conforme mostrado na implementação abaixo, os status de download são verificados toda vez e a ação apropriada é executada.

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

Essa classe contém um novo método `SaveDownload` que é chamado depois que cada download é concluído com êxito.

O conteúdo hospedado foi baixado com êxito e descompactado no `Cache` diretório. A estrutura do. O arquivo pkg requer que todos os arquivos sejam salvos `Contents` em um subdiretório, portanto, o código a seguir extrai arquivos de `Contents` dentro do subdiretório.

O código itera em todos os arquivos no pacote de conteúdo e os copia `Documents` no diretório, em uma subpasta chamada para o. `ProductIdentifier` Finalmente, ele `CompleteTransaction`chama, que `FinishTransaction` chama para remover a transação da fila de pagamentos.

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles 
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

Quando `FinishTransaction` é chamado, os arquivos baixados não são mais garantidos `Cache` no diretório. Todos os arquivos devem ser copiados `FinishTransaction`antes de chamar.


## <a name="other-considerations"></a>Outras considerações

O código de exemplo acima demonstra uma implementação relativamente simples da compra de conteúdo hospedado. Há alguns pontos adicionais que devem ser considerados:

### <a name="detecting-updated-content"></a>Detectando conteúdo atualizado

Embora seja possível atualizar seus pacotes de conteúdo hospedados, o kit de armazenamento não fornece nenhum mecanismo para enviar por push essas atualizações para os usuários que já baixaram e compraram o produto. Para implementar essa funcionalidade, seu código pode verificar a `SKProduct.ContentVersion` nova propriedade ( `SKProduct` se for `Downloadable`) regularmente e detectar se o valor é incrementado. Como alternativa, você pode criar um sistema de notificação por push.

### <a name="installing-updated-content-versions"></a>Instalando versões de conteúdo atualizadas

O código de exemplo acima ignora a cópia de arquivo se o arquivo já existe. Essa não é uma boa ideia se você quiser dar suporte a versões mais recentes do conteúdo que está sendo baixado.

Uma alternativa pode ser copiar o conteúdo em uma pasta chamada para a versão e controlar qual é a versão atual (por exemplo, no `NSUserDefaults` ou onde você armazena os registros de compra concluídos).

### <a name="restoring-transactions"></a>Restaurando transações

Quando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` é chamado, o kit de armazenamento retorna todas as transações anteriores para o usuário. Se eles tiverem comprado um grande número de itens ou se cada compra tiver grandes pacotes de conteúdo, a restauração poderá resultar em muito tráfego de rede, pois tudo será colocado na fila para download de uma vez.

Considere manter o controle de se um produto foi adquirido separadamente do download real do pacote de conteúdo associado.

### <a name="pausing-restarting-and-canceling-downloads"></a>Pausando, reiniciando e cancelando downloads

Embora o código de exemplo não demonstre esse recurso, é possível pausar e reiniciar downloads de conteúdo hospedado. O `SKPaymentQueue.DefaultQueue` tem métodos para `PauseDownloads`, `ResumeDownloads` e `CancelDownloads`.

Se o código chamar `FinishTransaction` a fila de pagamentos antes do download ser `Finished` cancelado automaticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Configurando o sinalizador SKIP-backup no conteúdo baixado

As diretrizes de backup do iCloud da Apple sugerem que o conteúdo de não-usuário que é facilmente restaurado de um servidor *não* deve ser submetido a backup (porque ele desnecessariamente usaria o armazenamento do icloud). Para obter mais informações sobre como definir o atributo backup, consulte a documentação do [sistema de arquivos](~/ios/app-fundamentals/file-system.md) .

## <a name="summary"></a>Resumo

Este artigo introduziu dois novos recursos do Store Kit no iOS6: comprando o iTunes e outro conteúdo de dentro de seu aplicativo e utilizando o servidor da Apple para hospedar suas próprias compras no aplicativo. Essa introdução deve ser lida em conjunto com a [documentação de compra no aplicativo](~/ios/platform/in-app-purchasing/index.md) existente para obter uma cobertura completa da funcionalidade da implementação do kit de armazenamento.

## <a name="related-links"></a>Links relacionados

- [StoreKit (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)
- [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)
- [Referência da estrutura do StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Referência de classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [Referência da API de pesquisa do iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Vídeo do WWDC: Vendendo produtos com o kit de loja](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
