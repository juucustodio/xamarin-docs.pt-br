---
title: Alterações no StoreKit no iOS 6
description: 'iOS 6 apresenta duas alterações para a API do Store Kit: a capacidade de exibir o iTunes (e o aplicativo Store/Eróticos) opção em que a Apple hospedará os arquivos para download de compra de produtos de dentro de seu aplicativo e um aplicativo no novo. Este documento explica como implementar esses recursos com o xamarin. IOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5d1bb5ab636cd7527a560332a9890e9907fac454
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61295594"
---
# <a name="changes-to-storekit-in-ios-6"></a>Alterações no StoreKit no iOS 6

_iOS 6 introduziu duas alterações para a API do Store Kit: a capacidade de exibir o iTunes (e o aplicativo Store/Eróticos) opção em que a Apple hospedará os arquivos para download de compra de produtos de dentro de seu aplicativo e um aplicativo no novo. Este documento explica como implementar esses recursos com o xamarin. IOS._

As principais alterações Store Kit em iOS6 são esses dois novos recursos:

- **Exibição de conteúdo no aplicativo e compras** – os usuários podem comprar e baixar aplicativos, música, livros e outras iTunes de conteúdo sem sair do seu aplicativo. Você também pode vincular a seus próprios aplicativos para promover a compra ou apenas incentivar as revisões e classificações.
- **No aplicativo de compra hospedado conteúdo** – Apple armazenará e entregar o conteúdo associado com seus produtos de compra no aplicativo, o que elimina a necessidade de um servidor separado hospedar os arquivos, automaticamente dá suporte a download em segundo plano e permite que você Escreva menos código.

Consulte a [compra no aplicativo](~/ios/platform/in-app-purchasing/index.md) guias para cobertura detalhada das APIs StoreKit.

## <a name="requirements"></a>Requisitos

Os recursos do Store Kit discutidos neste documento exigem o iOS 6 e o Xcode 4.5 juntamente com o xamarin. IOS 6.0.

## <a name="in-app-content-display--purchasing"></a>Exibição de conteúdo no aplicativo e compras

O novo recurso de compra no aplicativo do iOS permite aos usuários exibir informações sobre o produto e comprar ou baixar o produto de dentro de seu aplicativo.
Anteriormente aplicativos teria disparar Eróticos, o que resultam no usuário deixar o aplicativo original, a App Store ou iTunes. Esse novo recurso retorna automaticamente o usuário ao seu aplicativo quando eles são feitos.

[![](changes-to-storekit-images/image1.png "Retornando automaticamente para um aplicativo após a compra")](changes-to-storekit-images/image1.png#lightbox)

Exemplos de como isso poderia ser usado:

- **Incentivar os usuários de seu aplicativo** – você pode abrir a página de Store de aplicativo para que o usuário possa classificar e examinar seu aplicativo sem deixá-lo.
- **Ao promover entre aplicativos** – permitir que o usuário veja outros aplicativos que você publicar, com a capacidade de comprar/download imediatamente.
- **Ajudando os usuários a localizar e baixar conteúdo** – ajudam os usuários a comprar conteúdo que seu aplicativo localiza, gerencia ou agrega (por exemplo. um aplicativo de música pode fornecer uma lista de reprodução de músicas e permitir que cada música ser adquirido de dentro do aplicativo).

Uma vez o `SKStoreProductViewController` foi exibido o usuário pode interagir com as informações do produto, como se estivesse no iTunes, a App Store ou o Eróticos. O usuário pode:

- Capturas de tela de exibição (para aplicativos)
- Músicas de exemplo ou um vídeo (para música, programas de TV e filmes),
- Revisões de leitura (e gravação)
- Compra e download, o que acontece inteiramente dentro do Store Kit e controlador de exibição.

Algumas opções dentro de `SKStoreProductViewController` ainda será forçar o usuário para deixar seu aplicativo e abra o aplicativo da loja relevante, como clicar em **produtos relacionados** ou um aplicativo **suporte** link.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

A API para mostrar um produto em qualquer aplicativo é simples: requer apenas que você cria e exibe um `SKStoreProductViewController`. Siga estas etapas para criar e exibir um produto:

1. Criar uma `StoreProductParameters` objeto para passar parâmetros para o controlador de exibição, incluindo o `productId` no construtor.
1. Criar uma instância de `SKProductViewController`. Atribuí-lo a um campo de nível de classe.
1. Atribuir um manipulador para o controlador de exibição `Finished` evento, que deve ignorar o controlador de exibição. Esse evento é chamado quando o usuário pressionar Cancelar; ou, caso contrário, Finaliza uma transação dentro do controlador de exibição.
1. Chame o `LoadProduct` método passando o `StoreProductParameters` e um manipulador de conclusão. O manipulador de conclusão deve verificar se a solicitação de produto foi com êxito e apresentar nesse caso, o `SKProductViewController` modalmente. Tratamento de erros apropriado deve ser adicionado, caso o produto não pode ser recuperado.

### <a name="example"></a>Exemplo

O *ProductView* project na *StoreKit* código de exemplo para este artigo implementa um `Buy` método que aceita qualquer produto da ID da Apple e exibe o `SKStoreProductViewController`. O código a seguir exibe as informações de produto para qualquer determinada ID da Apple:

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

O aplicativo se parece com a captura de tela abaixo, quando em execução – download ou compra ocorre inteiramente dentro do `SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "O aplicativo tem esta aparência quando em execução")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Suporte a sistemas operacionais mais antigos

O aplicativo de exemplo inclui código que mostra como abrir o aplicativo Store, iTunes ou o Eróticos em versões anteriores do iOS. Use o `OpenUrl` método para abrir um criado corretamente **itunes.com** URL.

Você pode implementar uma verificação de versão para determinar qual código deve ser executado, conforme mostrado aqui:

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

O seguinte erro ocorrerá se a ID da Apple que você usa não for válida, que pode ser confuso, pois ele implica um problema de rede ou autenticação de algum tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Lendo a documentação do Objective-C

Os desenvolvedores lendo sobre o Kit de Store no Portal do desenvolvedor da Apple verá um protocolo – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – discutido em relação a esse novo recurso. O protocolo de delegado tem apenas um método – productViewControllerDidFinish – que foi exposto como o `Finished` eventos sobre o `SKStoreProductViewController` no xamarin. IOS.

## <a name="determining-apple-ids"></a>Determinando as IDs da Apple

A ID da Apple necessários para o `SKStoreProductViewController` é um *número* (não deve ser confundido com as IDs do lote, como "com.xamarin.mwc2012"). Há algumas maneiras diferentes, que você pode encontrar a ID da Apple para os produtos que você deseja exibir, listados abaixo:

### <a name="itunesconnect"></a>iTunesConnect

Para aplicativos que você publicar, é fácil encontrar as **ID da Apple** no iTunes Connect:

[![](changes-to-storekit-images/image3.png "Localizando a ID da Apple no iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API de pesquisa

A Apple fornece uma API de pesquisa dinâmica para todos os produtos na Store do aplicativo, iTunes e o Eróticos da consulta. Informações sobre como acessar a API de pesquisa podem ser encontradas no [afiliado recursos da Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), embora a API é exposta a qualquer pessoa (não está registradas apenas afiliadas). O JSON resultante pode ser analisado para descobrir os `trackId` que é a ID da Apple para usar com `SKStoreProductViewController`.

Os resultados incluirão também outros metadados, incluindo informações de exibição e as URLs de arte final que podem ser usadas para renderizar o produto em seu aplicativo.

Estes são alguns exemplos:

- **aplicativo iBooks** – [ http://itunes.apple.com/search?term=ibooks&amp; entidade = software&amp;país = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Ponto e o adversário iBook** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; entidade = ebook&amp;país = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Feed de parceiro da empresa

A Apple fornece a parceiros aprovados com um despejo de dados completos de todos os seus produtos, na forma de arquivos simples de pronto para o banco de dados que pode ser baixados. Se você for qualificado para acessar o [Feed de parceiro empresarial](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html), e em seguida, a ID da Apple para qualquer produto pode ser encontrada no conjunto de dados.

Muitos usuários do Feed de parceiro a Enterprise são membros do [programa para afiliadas](http://www.apple.com/itunes/affiliates) que permite comissões de ser conquistada nas vendas do produto. `SKStoreProductViewController` não oferece suporte a IDs afiliado (no momento da gravação).

### <a name="direct-product-links"></a>Produto Links diretos

A ID da Apple para um produto pode ser inferida de seu link de URL de visualização do iTunes.
Em qualquer iTunes links de produtos (para aplicativos, música ou livros) encontrar a parte do início com URL `id` e usar o número que segue.

Por exemplo, é o link direto para iBooks

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

e é a ID da Apple **364709193**. Da mesma forma para o aplicativo MWC2012, o link direto é

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

e é a ID da Apple **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Compra no aplicativo hospedado de conteúdo

Se suas compras no aplicativo consistem em conteúdo que pode ser baixado (por exemplo, manuais ou outras mídias, arte de nível de jogo e configuração ou outros arquivos grandes), em seguida, esses arquivos são usados para ser hospedado em seu servidor web e aplicativos tinham que incorporar o código para baixá-los após o com segurança Compre. Começando com o iOS 6, a Apple hospedará os arquivos em seus servidores, removendo a necessidade de um servidor separado. O recurso só está disponível para os produtos não consumíveis (não utilizado ou assinaturas). Vantagens de usar o serviço de hospedagem da Apple:

- Economize custos de hospedagem e a largura de banda.
- Provavelmente mais escalonável do que qualquer host de servidor que você está usando atualmente. 
- Menos código para escrever, uma vez que você não precise criar qualquer processamento do lado do servidor. 
- Download em segundo plano é implementado para você.

Observação: teste hospedado conteúdo de compra no aplicativo no iOS que Simulator não for compatível, portanto, você deve testar com um dispositivo real.

### <a name="hosted-content-basics"></a>Noções básicas de conteúdo hospedadas

Antes do iOS 6, havia duas maneiras de fornecer um produto (descrito mais detalhadamente [as compras no aplicativo do Xamarin](~/ios/platform/in-app-purchasing/index.md) documentação):

- **Produtos internos** – recursos que estão 'desbloqueada' com a compra, mas que são criados para o aplicativo (como o código ou recursos inseridos). Exemplos de produtos internos incluem filtros de foto desbloqueados ou geradores de energia no jogo.
- **Produtos de servidor-entregue** – após a compra, o aplicativo deve baixar o conteúdo de um servidor que você opera. Este conteúdo é baixado durante a compra, armazenado no dispositivo e, em seguida, renderizado como parte do fornecimento de produto. Exemplos incluem livros, revistos problemas ou níveis de jogos que consistem em arquivos de arte e configuração do plano de fundo.

No iOS 6 Apple oferece uma variação de produtos de servidor fornecesse: eles hospedará os arquivos de conteúdo em seus servidores. Isso torna muito mais simples de criar produtos de servidor fornecesse porque não é necessário para operar um servidor separado e Store Kit fornece funcionalidade de download em segundo plano que anteriormente você precisava escrever por conta própria. Para tirar proveito de hospedagem da Apple, habilitar a hospedagem de conteúdo para os novos produtos de compra no aplicativo e modificar seu código do Store Kit para tirar proveito dela. Arquivos de conteúdo do produto, em seguida, são criados usando o Xcode e carregados para servidores da Apple para análise e lançamento.

[![](changes-to-storekit-images/image4.png "O processo de compilação e entrega")](changes-to-storekit-images/image4.png#lightbox)

Usando o aplicativo Store para fornecer a compras no aplicativo *com hospedado conteúdo* requer a instalação e configuração a seguir:

- **o iTunes Connect** – você *deve* forneceu suas informações de serviços bancários e imposto para a Apple para que eles podem remeter fundos coletados em seu nome. Em seguida, você pode configurar produtos para vender e configurar contas de usuário de área restrita para testar a compra.  _Você também deve configurar conteúdo hospedado para esses produtos não consumíveis que você deseja hospedar com a Apple_.
- **Portal de provisionamento iOS** – criando um identificador de pacote e habilitando o acesso de Store de aplicativo para seu aplicativo, como você faria para qualquer aplicativo que dá suporte a compras no aplicativo.
- **Store Kit** – adicionar código ao seu aplicativo para exibir produtos, compra de produtos e restaurando transações.  _No iOS 6 Store Kit também gerenciar o download de conteúdo seu produto, em segundo plano, com atualizações de andamento._
- **Código personalizado** : para acompanhar as compras feitas por clientes e fornecer produtos ou serviços adquiridos. Utilize as novas classes do Store Kit iOS 6, como `SKDownload` para recuperar o conteúdo hospedado pela Apple.

As seções a seguir explicam como implementar o conteúdo hospedado, desde a criação e carregamento do pacote de gerenciamento a compra e download de processo, usando o código de exemplo para este artigo.

### <a name="sample-code"></a>Código de exemplo

O projeto de exemplo *HostedNonConsumables* (StoreKitiOS6.zip) usa hospedado conteúdo. O aplicativo oferece duas "capítulos de livros" para venda, o conteúdo para o qual é hospedado em servidores da Apple. O conteúdo consiste em um arquivo de texto e uma imagem, embora muito mais complexo do conteúdo pode ser usado em um aplicativo real.

O aplicativo se parece com isso antes, durante e após uma compra:

 [![](changes-to-storekit-images/image5.png "O aplicativo se parece com isso antes, durante e após uma compra")](changes-to-storekit-images/image5.png#lightbox)

O arquivo de texto e imagem são baixados e copiados no diretório de documentos do aplicativo. Para obter mais informações sobre os diferentes diretórios disponíveis para o armazenamento de aplicativo, consulte o [documentação do sistema de arquivos](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Quando a criação de novos produtos que irá usar o Apple do conteúdo de hospedagem, não se esqueça de selecionar o **não consumível** tipo de produto. Outros tipos de produto não têm suporte para hospedagem de conteúdo. Além disso, você não deve habilitar hospedagem de conteúdo para *existentes* produtos que vendem; ativar somente de hospedagem de conteúdo para os novos produtos.

 [![](changes-to-storekit-images/image6.png "Selecione o tipo de produto não consumível")](changes-to-storekit-images/image6.png#lightbox)

Insira um **ID do produto**. Essa ID será necessária mais tarde quando você cria o conteúdo para este produto.

 [![](changes-to-storekit-images/image7.png "Insira uma ID de produto")](changes-to-storekit-images/image7.png#lightbox)

Hospedagem de conteúdo é definida na seção de detalhes. Antes da compra no aplicativo ficarem ativos, desmarque a **hospedar conteúdo com a Apple** caixa de seleção se você desejar cancelar (mesmo se você tiver carregado o conteúdo de teste). No entanto hospedagem de conteúdo não pode ser removido após a compra no aplicativo passou em tempo real.

 [![](changes-to-storekit-images/image8.png "Hospedagem de conteúdo com a Apple")](changes-to-storekit-images/image8.png#lightbox)

Depois que você tenha ativado a hospedagem de conteúdo, o produto entrará **aguardando carregamento** status e mostrar esta mensagem:

 [![](changes-to-storekit-images/image9.png "O produto será inserir espera para status de Upload e mostrar esta mensagem")](changes-to-storekit-images/image9.png#lightbox)

O pacote de conteúdo deve ser criado com o Xcode e carregado usando a ferramenta de arquivamento. Instruções para criar pacotes de conteúdo são fornecidas na próxima seção **criando. Arquivos PKG**.

## <a name="creating-pkg-files"></a>Criando. Arquivos de pacote

Os arquivos de conteúdo que você carrega para a Apple devem atender aos seguintes restrições:

- Não pode exceder 2 GB de tamanho.
- Não pode conter código executável (ou links simbólicos que apontam para fora o conteúdo).
- Deve ser formatado adequadamente (incluindo uma **. plist** arquivo) e ter um **. pkg** extensão de arquivo. Isso será feito automaticamente se você seguir essas instruções de uso do Xcode.

Você pode adicionar muitos arquivos e diferentes tipos de arquivos, desde que atenderem a essas restrições. O conteúdo é compactado antes da entrega para seu aplicativo e descompactado pela Store Kit antes de seu código o acessa.

Depois de carregar um pacote de conteúdo, ele pode ser substituído com o conteúdo mais recente. Novo conteúdo deve ser carregado e enviado para revisão/aprovação por meio do processo normal. Incremento de `ContentVersion` campo em pacotes de conteúdo atualizados para indicar que ele é mais recente.

### <a name="xcode-in-app-purchase-content-projects"></a>Projetos de conteúdo de compra do Xcode no aplicativo

Criação de pacotes de conteúdo para os produtos de compra no aplicativo no momento, exige o Xcode. Não há nenhum OBJECTIVE-C a necessidade de CODIFICAÇÃO; Xcode tiver um novo tipo de projeto para esses pacotes que contém apenas os arquivos e um plist.

Nosso aplicativo de exemplo tem capítulos do livro para venda – cada pacote de conteúdo do capítulo contém:

-  um arquivo de texto, e
-  uma imagem para representar o capítulo.


Comece selecionando **arquivo > Novo projeto** no menu e escolhendo **o conteúdo de compra no aplicativo**:

 [![](changes-to-storekit-images/image10.png "Escolha o conteúdo de compra no aplicativo")](changes-to-storekit-images/image10.png#lightbox)

Insira o **nome do produto** e **identificador da empresa** , de modo que o **identificador de pacote** corresponde a **ID do produto** inserido no iTunes Conecte-se para este produto.

[![](changes-to-storekit-images/image11.png "Insira o nome e identificador")](changes-to-storekit-images/image11.png#lightbox)

Agora, você terá um espaço em branco **o conteúdo de compra no aplicativo** projeto. Você pode com o botão direito e **adicionar arquivos...** ou arraste-os para o **navegador de projeto**. Certifique-se de que o **ContentVersion** está correto (ele deve começam em 1.0, mas se você escolher mais tarde atualizar seu conteúdo, lembre-se de incrementá-lo).

Esta captura de tela mostra o Xcode com os arquivos de conteúdo incluídos no projeto e as entradas de plist visíveis na janela principal:

[![](changes-to-storekit-images/image12.png "Esta captura de tela mostra o Xcode com os arquivos de conteúdo incluídos no projeto e as entradas de plist visíveis na janela principal")](changes-to-storekit-images/image12.png#lightbox)

Depois de adicionar todos os seus arquivos de conteúdo pode salvar este projeto e editá-lo novamente mais tarde ou iniciar o processo de carregamento.

## <a name="uploading-pkg-files"></a>Carregando. Arquivos de pacote

A maneira mais fácil para carregar pacotes de conteúdo é com o **ferramenta de arquivamento do Xcode**. Escolher **produto > arquivo morto** no menu Iniciar:

![](changes-to-storekit-images/image13.png "Escolha Archiven")

O pacote de conteúdo, em seguida, será exibida no arquivo morto, conforme mostrado abaixo. O tipo de arquivo e o ícone mostram essa linha é um **arquivamento de conteúdo de compra no aplicativo**. Clique em **validar...** Para verificar o nosso pacote de conteúdo para erros sem realmente executar o carregamento.

[![](changes-to-storekit-images/image14.png "Validar o pacote")](changes-to-storekit-images/image14.png#lightbox)

Faça logon com sua as credenciais de conexão do iTunes:

[![](changes-to-storekit-images/image15.png "Faça logon com sua as credenciais de conexão do iTunes")](changes-to-storekit-images/image15.png#lightbox)

Escolha o aplicativo correto e compra no aplicativo para associar esse conteúdo com:

[![](changes-to-storekit-images/image16.png "Escolha o aplicativo correto e compra no aplicativo para associar esse conteúdo com")](changes-to-storekit-images/image16.png#lightbox)

Você verá uma mensagem semelhante esta captura de tela:

![Um exemplo nenhuma mensagem de problemas](changes-to-storekit-images/image17.png "um exemplo nenhuma mensagem de problemas")

Agora, passar por um processo semelhante, mas clicar em **distribuir...** Na verdade, carregará o conteúdo.

[![Distribuir o aplicativo](changes-to-storekit-images/image18.png "distribuir o aplicativo")](changes-to-storekit-images/image18.png#lightbox)

Selecione a primeira opção, para carregar o conteúdo:

![Carregar o conteúdo](changes-to-storekit-images/image19.png "carregar o conteúdo")

Entre novamente:

[![](changes-to-storekit-images/image15.png "Logon no")](changes-to-storekit-images/image15.png#lightbox)

Escolha o aplicativo correto e o registro de compra no aplicativo para carregar o conteúdo para:

[![](changes-to-storekit-images/image20.png "Escolha o registro de aplicativo e no aplicativo de compra")](changes-to-storekit-images/image20.png#lightbox)

Aguarde enquanto os arquivos sejam carregados:

[![](changes-to-storekit-images/image21.png "A caixa de diálogo de upload do conteúdo")](changes-to-storekit-images/image21.png#lightbox)

Quando o upload for concluído, uma mensagem será exibida para avisá-lo de que o conteúdo foi enviado para a App Store.

[![](changes-to-storekit-images/image22.png "Um exemplo de mensagem de upload bem-sucedido")](changes-to-storekit-images/image22.png#lightbox)

Depois que tiver sido feito, quando você retornar para a página de produto no iTunes Connect, ele mostrará os detalhes do pacote e estar no **pronto para enviar** status. Quando o produto estiver nesse status, você pode começar a testar no ambiente de área restrita. Você não precisa do produto para a área restrita de teste de 'Enviar'.

[![](changes-to-storekit-images/image23.png "iTunes Connect, ele mostrará os detalhes do pacote e estar em pronto para o status de envio")](changes-to-storekit-images/image23.png#lightbox)

Pode levar algum tempo (por exemplo. alguns minutos) entre carregar o arquivo morto e o status de conexão que está sendo atualizado do iTunes. Você pode enviar o produto para revisão separadamente ou enviá-lo em conjunto com um binário de aplicativo. Depois de Apple oficialmente aprovou o conteúdo será disponível no aplicativo Store de produção para compra em seu aplicativo.

### <a name="pkg-file-format"></a>Formato de arquivo PKG

Usando a ferramenta de arquivamento e Xcode para criar e carregar um pacote de conteúdo hospedado significa que você nunca verá o conteúdo do próprio pacote. Os arquivos e diretórios em que os pacotes criados para obter a aparência do aplicativo de exemplo, como a captura de tela abaixo, com o **plist** arquivo na raiz e os arquivos de produto em um **conteúdo** subdiretório:

[![](changes-to-storekit-images/image24.png "O arquivo de plist na raiz e os arquivos de produto em um subdiretório de conteúdo")](changes-to-storekit-images/image24.png#lightbox)

Observe a estrutura do diretório do pacote (especialmente a localização dos arquivos no `Contents` subdiretório) porque você precisará entender essas informações para extrair os arquivos do pacote no dispositivo.

### <a name="updating-package-content"></a>Atualizando o conteúdo do pacote

O procedimento para atualizar o conteúdo depois de ter sido aprovado:

- Edite o conteúdo de compra no aplicativo projeto no Xcode.
- Aumentar o número de versão.
- Carregar novamente para o iTunes Connect. Compradores subsequentes receberá automaticamente a versão mais recente, mas os usuários que já têm a versão antiga não receberão qualquer notificação.
- Seu aplicativo é responsável por notificar os usuários e encorajando-os para recuperar uma versão mais recente do conteúdo. O aplicativo também deve criar uma função que baixa a nova versão, usando o recurso de restauração do Store Kit.
- Para determinar se existe uma versão mais recente, você pode criar um recurso em seu aplicativo para buscar SKProducts (por exemplo. mesmo processo que é usado para recuperar os preços dos produtos) e compare a propriedade ContentVersion.

## <a name="purchasing-overview"></a>Visão geral de compras

Antes de ler esta seção, revisar a arquitetura [documentação de compra no aplicativo](~/ios/platform/in-app-purchasing/index.md).

A sequência de eventos que ocorre quando um produto com o conteúdo hospedado é adquirida e download é ilustrado neste diagrama:

[![](changes-to-storekit-images/image25.png "A sequência de eventos que ocorre quando um produto com o conteúdo hospedado é adquirida e baixar")](changes-to-storekit-images/image25.png#lightbox)

1. Novos produtos podem ser criados no iTunes Connect com o conteúdo da hospedado habilitado. O conteúdo real é construído separadamente no Xcode (como simplesmente como arrastar arquivos em uma pasta) e, em seguida, arquivado e carregado no iTunes (nenhuma codificação é necessária). Cada produto, em seguida, é enviado para aprovação, após o qual ele se torna disponível para compra. No código de exemplo essas IDs de produto são embutidos em código, mas a hospedagem de conteúdo com a Apple é mais flexível, se você armazenar a lista de produtos disponíveis em um servidor remoto para que possa ser atualizado quando você envia o conteúdo para o iTunes Connect e novos produtos.
1. Quando o usuário compra um produto, uma transação é colocada na fila para processamento de pagamento.
1. Store Kit encaminha a solicitação de compra para servidores do iTunes para processamento.
1. Transação é concluída (por exemplo, em servidores do iTunes cliente é cobrado) e uma confirmação é retornada ao aplicativo, com informações sobre o produto anexado incluindo se ele está disponível para download (e nesse caso, o tamanho do arquivo e outros metadados).
1. Seu código deve verificar se o produto está disponível para download e, nesse caso, faça uma solicitação de download de conteúdo que também é colocada na fila de pagamento. Store Kit envia essa solicitação para os servidores do iTunes.
1. Servidor retorna o arquivo de conteúdo para Store Kit, que fornece um retorno de chamada para retornar o progresso do download e o tempo restante estimativas ao seu código.
1. Uma vez concluído, obtenha notificado e passado de um local de arquivo na pasta de Cache.
1. Seu código deve copiar os arquivos e verificá-los, salvar qualquer estado que você precisa se lembrar que o produto foi adquirido. Aproveite essa oportunidade para definir o sinalizador de backup corretamente nos novos arquivos (Dica: se eles vêm de um servidor e nunca são editados pelo usuário, você deve ignorar o fazer o backup, uma vez que o usuário pode sempre recuperá-las de servidores da Apple no futuro).
1. Chame FinishTransaction. Esta etapa é importante, pois ele remove a transação da fila de pagamento. Também é importante que você não chamar FinishTransaction até depois de ter copiado o conteúdo fora do diretório de Cache. Depois de chamar FinishTransaction, os arquivos em cache provavelmente sejam limpos rapidamente.

## <a name="implementing-hosted-content-purchase"></a>Implementando a compra de conteúdo hospedada

As informações a seguir devem ser lida em conjunto com o completo [documentação de compras no aplicativo](~/ios/platform/in-app-purchasing/index.md). As informações neste documento se concentra nas diferenças entre o conteúdo hospedado e a implementação anterior.

### <a name="classes"></a>Classes

As classes a seguir foram adicionadas ou alteradas para o conteúdo hospedado de suporte no iOS 6:

- **SKDownload** – nova classe que representa um download em andamento. A API permite mais de uma por produto, no entanto, inicialmente apenas um foi implementado.
- **SKProduct** – as novas propriedades adicionadas: `Downloadable`, `ContentVersion`, `ContentLengths` matriz.
- **SKPaymentTransaction** – nova propriedade adicionada: `Downloads`, que contém uma coleção de `SKDownload` objetos se este produto tem hospedado conteúdo disponível para download.
- **SKPaymentQueue** – novo método adicionado: `StartDownloads`. Chamar esse método com `SKDownload` objetos para buscar o conteúdo hospedado. Baixando pode ocorrer em segundo plano.
- **SKPaymentTransactionObserver** – novo método: `UpdateDownloads`. Store Kit chama esse método com informações de progresso sobre operações atuais de download.

Detalhes do novo `SKDownload` classe:

- **Progresso** – um valor entre 0 a 1 que você pode usar para exibir um indicador de porcentagem de conclusão para o usuário. Não use o andamento = = 1 para detectar se o download for concluído, verificar estado = = concluído.
- **TimeRemaining** – estimativa do download tempo restante, em segundos. -1 significa que ele ainda está calculando estimativa.
- **Estado** – Active Directory, aguardando, concluído, falha, pausada, cancelada.
- **ContentURL** – onde o conteúdo foi colocado no disco, além do local do arquivo a `Cache` directory. Populado somente após a conclusão do download.
- **Erro** – Verifique se a propriedade se o estado de falha.

As interações entre as classes no código de exemplo são mostradas neste diagrama (o código específico a compras de conteúdo hospedadas é mostrado em verde):

[![](changes-to-storekit-images/image26.png "Compras de conteúdo hospedadas é mostrado em verde no diagrama")](changes-to-storekit-images/image26.png#lightbox)

O código de exemplo em que essas classes foram usadas é mostrado no restante desta seção:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Alterar existente `UpdatedTransactions` substituição é para verificar se há conteúdo disponível para download e chamada `StartDownloads` se necessário:

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

Novo método substituído `UpdatedDownloads` é mostrado abaixo. Store Kit chama esse método após `StartDownloads` é disparada no `UpdatedTransactions`. Esse método é chamado *várias vezes* em intervalos indeterminados para fornecer a você com o andamento do download e, em seguida, novamente quando o download foi concluído. Observe que o método aceita uma matriz de `SKDownload` objetos, para que cada chamada de método pode fornecer o status de vários downloads na fila. Conforme mostrado na implementação abaixo que os status de download são verificados cada hora e a ação apropriada.

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

Essa classe contém um novo método `SaveDownload` que é chamado após cada download é concluído com êxito.

O conteúdo hospedado foi baixado com êxito e descompactado no `Cache` directory. A estrutura da. Arquivo PKG requer que todos os arquivos sejam salvos em um `Contents` subdiretório, portanto, o código a seguir extrai os arquivos de dentro do `Contents` subdiretório.

O código itera em todos os arquivos no pacote de conteúdo e os copia para o `Documents` diretório, em uma subpasta nomeada para o `ProductIdentifier`. Por fim, ele chama `CompleteTransaction`, que chama `FinishTransaction` para remover a transação de fila de pagamento.

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

Quando `FinishTransaction` é chamado, o baixado arquivos não são necessariamente no `Cache` directory. Todos os arquivos devem ser copiados antes de chamar `FinishTransaction`.


## <a name="other-considerations"></a>Outras considerações

Exemplo de código acima demonstra uma implementação bastante simple de comprar o conteúdo hospedado. Há alguns pontos adicionais que devem ser considerados:

### <a name="detecting-updated-content"></a>Detectar conteúdo atualizado

Embora seja possível atualizar seus pacotes de conteúdo hospedados, Store Kit não oferece nenhum mecanismo para enviar por push essas atualizações aos usuários que já tenham baixado e adquiriu o produto. Para implementar essa funcionalidade, seu código pode verificar o novo `SKProduct.ContentVersion` propriedade (se o `SKProduct` é `Downloadable`) regularmente e detectar se o valor é incrementado. Como alternativa, você poderia criar um sistema de notificação por push.

### <a name="installing-updated-content-versions"></a>Instalando versões atualizadas do conteúdo

O código de exemplo acima ignora a cópia de arquivo se o arquivo já existe. Isso não é uma boa ideia se você deseja dar suporte a versões mais recentes do conteúdo que está sendo baixado.

Uma alternativa seria copie o conteúdo para uma pasta chamada para a versão e manter o controle de qual é a versão atual (por exemplo. no `NSUserDefaults` ou onde quer que você armazena os registros de compra completa).

### <a name="restoring-transactions"></a>Restauração de transações

Quando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` é chamado, o Kit de Store retorna todas as transações anteriores para o usuário. Se eles compraram um grande número de itens ou se cada compra grandes pacotes de conteúdo, a restauração pode resultar em uma grande quantidade de tráfego de rede, tudo o que obtém o enfileiramento para download ao mesmo tempo.

Considere manter o controle de um produto foi comprado separadamente do download do pacote de conteúdo associado.

### <a name="pausing-restarting-and-canceling-downloads"></a>Pausar, reiniciar e Cancelar Downloads

Embora o código de exemplo não demonstra este recurso, é possível pausar e reiniciar os downloads de conteúdo hospedados. O `SKPaymentQueue.DefaultQueue` tem métodos para `PauseDownloads`, `ResumeDownloads` e `CancelDownloads`.

Se o código chama `FinishTransaction` na fila antes do download que está sendo pagamento `Finished` e em seguida, esse download é cancelada automaticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Definir o sinalizador de Backup ignorar no conteúdo baixado

Diretrizes de Backup do iCloud da Apple sugerem que o conteúdo de não usuário facilmente restaurados de um servidor deve *não* ser submetidos a backup (porque ele usaria desnecessariamente o armazenamento no iCloud). Para obter mais informações sobre como definir o atributo de backup, consulte a [sistema de arquivos](~/ios/app-fundamentals/file-system.md) documentação.

## <a name="summary"></a>Resumo

Este artigo apresentou dois novos recursos do Store Kit no iOS6: compra iTunes e outros conteúdos de dentro de seu aplicativo e da utilização de servidor da Apple para hospedar seus próprio compras no aplicativo. Esta introdução deve ser lida em conjunto com os existentes [documentação de compra no aplicativo](~/ios/platform/in-app-purchasing/index.md) para cobertura completa de como implementar a funcionalidade do Store Kit.

## <a name="related-links"></a>Links relacionados

- [StoreKit (amostra)](https://developer.xamarin.com/samples/StoreKit/)
- [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)
- [Referência de estrutura no StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Referência de classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [Referência da API de pesquisa do iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC vídeo: Produtos vendidos com Store Kit](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
