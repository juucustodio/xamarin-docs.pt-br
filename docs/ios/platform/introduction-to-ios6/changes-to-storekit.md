---
title: Alterações StoreKit
description: 'iOS 6 apresenta duas alterações para a API do Kit de armazenamento: a capacidade de exibir iTunes (e loja de aplicativos/iBookstore) onde Apple hospedará os arquivos para download de opção de compra de produtos de dentro de seu aplicativo e um aplicativo no novo. Este documento explica como implementar esses recursos com xamarin.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 8a7a70c3f84518141cf44d630fb4137051d0c866
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="changes-to-storekit"></a>Alterações StoreKit

_iOS 6 apresenta duas alterações para a API do Kit de armazenamento: a capacidade de exibir iTunes (e loja de aplicativos/iBookstore) onde Apple hospedará os arquivos para download de opção de compra de produtos de dentro de seu aplicativo e um aplicativo no novo. Este documento explica como implementar esses recursos com xamarin._

As principais alterações Kit de repositório em iOS6 são esses dois novos recursos:

-   **Exibição de conteúdo no aplicativo e compras** – usuários podem comprar e baixar aplicativos, música, livros e outros iTunes conteúdo sem deixar seu aplicativo. Você também pode vincular a seus próprios aplicativos para promover a compra ou apenas incentivamos revisões e avaliações. 
-   **No aplicativo de compra hospedado conteúdo** – Apple armazenará e entregar o conteúdo associado com seus produtos de compra no aplicativo, o que elimina a necessidade de um servidor separado hospedar os arquivos automaticamente dá suporte ao download em segundo plano e permite que você Escreva menos código. 


Recomenda-se que este documento ser lida em conjunto com o xamarin existente [compras no aplicativo](~/ios/platform/in-app-purchasing/index.md) documentação.

## <a name="requirements"></a>Requisitos

Os recursos de armazenamento Kit abordados neste documento exigem iOS 6 e 4.5 Xcode, juntamente com o xamarin 6.0.


## <a name="in-app-content-display--purchasing"></a>Exibição de conteúdo no aplicativo e compra

O novo recurso de compra no aplicativo do iOS permite aos usuários exibir as informações de produto e comprar ou baixar o produto de dentro de seu aplicativo.
Anteriormente aplicativos precisam disparar iTunes, loja de aplicativos ou o iBookstore que resultaria em que o usuário sair do aplicativo original. Esse novo recurso retorna automaticamente o usuário para seu aplicativo quando eles são feitos.

 [![](changes-to-storekit-images/image1.png "Retornando automaticamente a um aplicativo após a compra")](changes-to-storekit-images/image1.png#lightbox)

Há várias situações em que isso pode ser útil, incluindo (mas não limitado a):

-   **Incentivar os usuários a taxa de seu aplicativo** – você pode abrir a página da loja de aplicativos para que o usuário pode classificar e revisar seu aplicativo sem deixá-lo. 
-   **Promovendo entre aplicativos** – permitem que o usuário veja outros aplicativos que você publica, a possibilidade de comprar/download imediatamente. 
-   **Ajudando os usuários a localizar e baixar o conteúdo** – ajudam os usuários a conteúdo que seu aplicativo encontra, gerencia ou agrega (por exemplo. um aplicativo de música poderia fornecer uma lista de reprodução de músicas e permitir que cada música ser adquirido de dentro do aplicativo). 


Uma vez o `SKStoreProductViewController` foi exibida o usuário pode interagir com as informações de produto, como se estivesse na iTunes, loja de aplicativos ou o iBookstore. Isso inclui:

-  Exibindo capturas de tela (para aplicativos)
-  Músicas de amostragem ou vídeo (para música, programas de TV e filmes)
-  Revisões de leitura (e gravação)
-  Comprar e baixar, que ocorre inteiramente dentro o Kit de armazenamento e o controlador de exibição. Seu aplicativo não precisa fornecer qualquer código adicional para este trabalho. 


Lembre-se de que algumas opções dentro de `SKStoreProductViewController` ainda será força o usuário para deixar seu aplicativo e abra o aplicativo da loja relevante, como clicar em **produtos relacionados** ou um aplicativo **suporte** link.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

A API para mostrar um produto em qualquer aplicativo é muito simple: requer apenas que você cria e exibe um `SKStoreProductViewController`. Siga estas etapas para criar e exibir um produto:

1.  Criar um `StoreProductParameters` objeto para passar parâmetros para o controlador de exibição, incluindo o `productId` no construtor. 
1.  Criar uma instância de `SKProductViewController` . Atribua um campo de nível de classe. 
1.  Atribuir um manipulador para o controlador de exibição `Finished` evento, que deve descartar o controlador de exibição. Este evento é chamado quando o usuário pressionar Cancelar; ou, caso contrário Finaliza uma transação dentro do controlador de exibição. 
1.  Chamar o `LoadProduct` método passando o `StoreProductParameters` e um processador de conclusão. O processador de conclusão deve verificar que a solicitação de produto teve êxito e nesse caso, apresentar o `SKProductViewController` restrito. Tratamento de erros apropriado deve ser adicionado no caso do produto não pode ser recuperado. 

### <a name="example"></a>Exemplo

O *ProductView* project no *StoreKit* código de exemplo para este artigo implementa um `Buy` método que aceita qualquer produto da ID da Apple e exibe o `SKStoreProductViewController`. O código a seguir exibe as informações de produto para qualquer determinada ID da Apple:

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

O aplicativo tem esta aparência quando em execução – baixar ou comprar ocorre inteiramente dentro do `SKStoreProductViewController`:

 [![](changes-to-storekit-images/image2.png "O aplicativo tem esta aparência quando em execução")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Suporte a sistemas operacionais mais antigos

O aplicativo de exemplo inclui o código que mostra como abrir a loja de aplicativos, iTunes ou o iBookstore em versões anteriores do iOS. Use o `OpenUrl` método abrir criado corretamente **itunes.com** URL.

Você pode implementar uma verificação de versão para determinar qual código em execução, como este:

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

O seguinte erro ocorrerá se você usar a ID da Apple não for válido, que pode ser confuso, pois ele implica um problema de rede ou autenticação de algum tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Ler a documentação Objective-C

Os desenvolvedores de ler sobre o Kit de armazenamento no Portal do desenvolvedor da Apple verá um protocolo – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – discutido em relação a esse novo recurso. O protocolo de representante possui apenas um método – productViewControllerDidFinish – que foi exposto como o `Finished` evento no `SKStoreProductViewController` em xamarin.


## <a name="determining-apple-ids"></a>Determinando IDs Apple

A ID da Apple necessários para o `SKStoreProductViewController` é um *número* (não deve ser confundido com IDs de lote como "com.xamarin.mwc2012"). Há algumas maneiras diferentes, que você pode encontrar a ID da Apple para produtos que você deseja exibir, listados abaixo:

### <a name="itunesconnect"></a>iTunesConnect

Para aplicativos que você publicar, é fácil de encontrar o **ID da Apple** na iTunes conectar:

 [![](changes-to-storekit-images/image3.png "Localizar a ID da Apple no iTunes conectar")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />


### <a name="search-api"></a>API de pesquisa

Apple fornece uma API de pesquisa dinâmico para consultar todos os produtos da loja de aplicativos, iTunes e o iBookstore. Informações sobre como acessar a API de pesquisa podem ser encontradas em [afiliado recursos da Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), embora a API é exposta a qualquer pessoa (não apenas registradas afiliadas). O JSON resultante pode ser analisado para descobrir o `trackId` que é a ID da Apple para usar com `SKStoreProductViewController`.

Os resultados também incluirá outros metadados, incluindo informações de exibição e as URLs de arte final que podem ser usados para renderizar o produto em seu aplicativo.

Estes são alguns exemplos:

-   **aplicativo de iBooks*- [http://itunes.apple.com/search?term=ibooks&amp;entidade = software&amp;país = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us) 
-   **Ponto e o adversário iBook*- [http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entidade = ebook&amp;país = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us) 


### <a name="enterprise-partner-feed"></a>Feed de parceiro do Enterprise

Apple fornece a parceiros aprovados com um despejo de dados completa de todos os seus produtos, na forma de arquivos simples de banco de dados pronto para download. Se qualificar para acessar o [Enterprise parceiro Feed](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html) , em seguida, a ID da Apple para qualquer produto pode ser encontrado no conjunto de dados.

Observe que muitos usuários do Feed de parceiro do Enterprise são membros do [programa afiliado](http://www.apple.com/itunes/affiliates) que permite comissões ser acumulado nas vendas do produto. `SKStoreProductViewController` não oferece suporte a IDs afiliado (no momento da gravação; isso pode ser adicionado pela Apple no futuro).

### <a name="direct-product-links"></a>Produto vínculos diretos

A ID da Apple para um produto pode ser inferido de seu link de URL de visualização do iTunes.
Em qualquer iTunes link do produto (para aplicativos, música ou manuais) localize a parte do início de URL com `id` e use o número que segue.

Por exemplo, é o link direto para iBooks

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

e a ID da Apple é **364709193**. Da mesma forma para o aplicativo MWC2012, o link direto é

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

e a ID da Apple é **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Compra no aplicativo hospedado conteúdo

Se suas compras no aplicativo consistem em conteúdo para download (como manuais ou outra mídia, arte de nível de jogo e configuração ou outros arquivos grandes), em seguida, esses arquivos são usados para ser hospedado em seu servidor web e aplicativos tinham que incorporar o código para baixá-los após o com segurança compra. No iOS 6 Apple introduziu uma opção onde eles hospedará os arquivos em seus servidores, eliminando a necessidade de um servidor separado. O recurso só está disponível para os produtos não consumível (não utilizado ou assinaturas). Vantagens de usar o serviço de hospedagem da Apple:

-  Reduzir os custos de largura de banda e hospedagem.
-  Provavelmente mais escalonável que qualquer host do servidor que você está usando no momento. 
-  Menos código gravar, porque você não precisa criar qualquer processamento do lado do servidor. 
-  Download em segundo plano é implementado para você.


Observação: teste hospedado conteúdo de compra no aplicativo no iOS, não há suporte para o simulador, assim, você deve testar com um dispositivo real.

### <a name="hosted-content-basics"></a>Noções básicas de conteúdo hospedadas

Antes de iOS 6, houve duas maneiras de fornecer um produto (descrito mais detalhadamente em [as compras no aplicativo do Xamarin](~/ios/platform/in-app-purchasing/index.md) documentação):

-   **Produtos internos** – recursos que são 'desbloqueada' adquirindo, mas que são internas para o aplicativo (como código ou recursos incorporados a). Exemplos de produtos internos incluem filtros de foto desbloqueados ou do jogo power-no-break. 
-   **Produtos de servidor e entregue** – após a compra, o aplicativo deve baixar o conteúdo de um servidor que você opera. Este conteúdo é baixado durante a compra, armazenado no dispositivo e renderizado como parte do fornecimento do produto. Exemplos incluem manuais, problemas de revista ou níveis de jogos que consistem em arquivos de arte e configuração do plano de fundo. 


No iOS 6 Apple oferece uma variação de produtos de servidor e entregue: hospedará os arquivos de conteúdo em seus servidores. Isso torna muito mais simples de criar produtos entregues pelo servidor porque não é necessário para operar um servidor separado e Store Kit fornece funcionalidade de download em segundo plano que você tinha anteriormente para gravação por conta própria. Para tirar proveito de hospedagem da Apple, habilitar a hospedagem de conteúdo para os novos produtos de compra no aplicativo e modifique o código do Kit de repositório para tirar proveito dele. Arquivos de conteúdo do produto, em seguida, são criados usando o Xcode e carregados para servidores da Apple para revisão e versão.

 [![](changes-to-storekit-images/image4.png "O processo de compilação e entrega")](changes-to-storekit-images/image4.png#lightbox)

Usando o armazenamento de aplicativo para fornecer a compra no aplicativo *com hospedado conteúdo* requer a instalação e configuração a seguir:

-   **conectar-se de iTunes** – você *deve* forneceu as informações de serviços bancários e imposto para a Apple para que eles podem remeter fundos coletados em seu nome. Você pode configurar produtos para vender e configurar contas de usuário da área restrita para testar a compra.  *Você também deve configurar conteúdo hospedado**para esses produtos não consumíveis que você deseja hospedar com a Apple* *.* 
-   **Portal de provisionamento do iOS** – criar um identificador de pacote e habilitando o acesso de loja de aplicativos para seu aplicativo, como você faria para qualquer aplicativo que oferece suporte a compra no aplicativo. 
-   **Armazenar Kit** – adicionar código ao seu aplicativo para exibir produtos, compra de produtos e restauração de transações.  *No iOS 6 Store Kit também gerenciar o download de seu conteúdo de produto, em segundo plano, com atualizações de andamento.* 
-   **Código personalizado** – para rastrear as compras feitas por clientes e fornecer os produtos ou serviços adquiridos. Utilizar novas classes de armazenamento Kit iOS 6 como `SKDownload` para recuperar o conteúdo hospedado pela Apple. 


As seções a seguir explicam como implementar conteúdo hospedado, criando e carregando o pacote para gerenciar a compra e baixar o processo, usando o código de exemplo para este artigo.


### <a name="sample-code"></a>Código de exemplo

O projeto de exemplo *HostedNonConsumables* (StoreKitiOS6.zip) demonstra como compilar um aplicativo que usa hospedado o conteúdo. O aplicativo oferece dois "capítulos de livro" para venda, o conteúdo para o qual está hospedado em servidores da Apple. O conteúdo consiste em um arquivo de texto e uma imagem, embora o conteúdo mais complexo que pode ser usado em um aplicativo real.

O aplicativo tem esta aparência antes, durante e após uma compra:

 [![](changes-to-storekit-images/image5.png "O aplicativo tem esta aparência antes, durante e após uma compra")](changes-to-storekit-images/image5.png#lightbox)

O arquivo de texto e imagem são baixados e copiados para o diretório de documentos do aplicativo. Consulte o [trabalhando com a documentação do sistema de arquivo](~/ios/app-fundamentals/file-system.md) para obter mais informações sobre os diferentes diretórios disponíveis para o armazenamento de aplicativos.

## <a name="itunes-connect"></a>conectar-se de iTunes

Quando o conteúdo da criação de novos produtos que usarão Apple hospedagem Certifique-se selecionar o **não consumível** tipo de produto. Outros tipos de produto não dão suporte a hospedagem de conteúdo. Além disso, você não deve permitir conteúdo de hospedagem para *existente* produtos que vendem; ativar somente hospedando conteúdo para novos produtos.

 [![](changes-to-storekit-images/image6.png "Selecione o tipo de produto não consumível")](changes-to-storekit-images/image6.png#lightbox)

Insira um **ID de produto**. Isso será necessário mais tarde quando você cria o conteúdo para este produto.

 [![](changes-to-storekit-images/image7.png "Insira uma ID de produto")](changes-to-storekit-images/image7.png#lightbox)

Conteúdo de hospedagem está definido na seção de detalhes. Antes da compra no aplicativo ficarem ativos, desmarque a caixa de seleção "Host conteúdo com Apple" Se você desejar cancelar (mesmo que você carregou o conteúdo de teste). No entanto hospedagem de conteúdo não pode ser removido após a compra no aplicativo passou em tempo real.

 [![](changes-to-storekit-images/image8.png "Hospedando conteúdo com a Apple")](changes-to-storekit-images/image8.png#lightbox)

Depois que você tenha ativado hospedando o conteúdo, o produto entrará **aguardando o carregamento** status e exibir esta mensagem:

 [![](changes-to-storekit-images/image9.png "O produto entra em espera para o status de carregamento e mostrar esta mensagem")](changes-to-storekit-images/image9.png#lightbox)

O conteúdo deve ser criado com o Xcode e carregados usando a ferramenta de arquivamento. Instruções para criar pacotes de conteúdo é fornecido na próxima seção **criando. Arquivos de pacote**.

## <a name="creating-pkg-files"></a>Criando. Arquivos de pacote

Os arquivos de conteúdo que você carrega no Apple devem atender aos seguintes restrições:

-  Não pode exceder 2GB de tamanho.
-  Não pode conter código executável (ou links simbólicos ponto fora do conteúdo). 
-  Deve ser formatada adequadamente (incluindo um arquivo. plist) e têm uma extensão de arquivo. pkg. Isso será feito automaticamente se você seguir essas instruções usando o Xcode. 


Você pode adicionar vários arquivos diferentes e tipos de arquivos, desde que atenda a essas restrições. O conteúdo é compactado antes da entrega para seu aplicativo e descompactado pelo Kit de armazenamento antes que seu código acesse a ele.

Depois de carregar um pacote de conteúdo, ele pode ser substituído com o conteúdo mais recente. Novo conteúdo deve ser carregado e enviado para revisão/aprovação por meio do processo normal. Você deve incrementar o `ContentVersion` campo em pacotes de conteúdo atualizados.

### <a name="xcode-in-app-purchase-content-projects"></a>Projetos de conteúdo de compra Xcode no aplicativo

Criando pacotes de conteúdo para os produtos de compra no aplicativo atualmente requer Xcode. Não há nenhum OBJECTIVE-C a necessidade de CODIFICAÇÃO; Xcode tem um novo tipo de projeto para esses pacotes que contém apenas os arquivos e um plist.

Nosso aplicativo de exemplo tem capítulos do livro para venda – cada pacote de conteúdo do capítulo conterá:

-  um arquivo de texto, e
-  uma imagem para representar o capítulo.


Iniciar selecionando **arquivo > Novo projeto** no menu e escolhendo **conteúdo de compra no aplicativo**:

 [![](changes-to-storekit-images/image10.png "Escolha o conteúdo de compra no aplicativo")](changes-to-storekit-images/image10.png#lightbox)

Insira o **nome do produto** e **identificador da empresa** , de modo que o **identificador de pacote** corresponde a **ID de produto** inserido na iTunes Conecte-se para este produto.

 [![](changes-to-storekit-images/image11.png "Insira o nome e o identificador")](changes-to-storekit-images/image11.png#lightbox)

Agora você terá um espaço em branco **conteúdo de compra no aplicativo** projeto. Clique e **adicionar arquivos...** ou arraste-os para o **navegador de projeto**. Certifique-se de que o **ContentVersion** está correta (ele deve iniciar em 1.0, mas se você escolher mais tarde atualizar seu conteúdo, lembre-se de incrementá-lo).

Esta captura de tela mostra Xcode com os arquivos de conteúdo incluídos no projeto e as entradas de plist visíveis na janela principal:

 [![](changes-to-storekit-images/image12.png "Esta captura de tela mostra Xcode com os arquivos de conteúdo incluídos no projeto e as entradas de plist visíveis na janela principal")](changes-to-storekit-images/image12.png#lightbox)

Depois de adicionar todos os arquivos de conteúdo pode salvar o projeto e editá-lo novamente mais tarde ou começar o processo de carregamento.

## <a name="uploading-pkg-files"></a>Carregando. Arquivos de pacote

É a maneira mais fácil para carregar pacotes de conteúdo com o **Xcode arquivamento ferramenta**. Escolha **produto > arquivamento** no menu Iniciar:

 ![](changes-to-storekit-images/image13.png "Escolha Archiven")

O pacote de conteúdo será exibida no arquivo morto conforme mostrado abaixo. Observe que o tipo de arquivo e o ícone mostram este é um **arquivamento de conteúdo de compra no aplicativo**. Clique em **validar...** Para verificar o nosso pacote de conteúdo para erros sem realmente preforming o carregamento.

 [![](changes-to-storekit-images/image14.png "Validar o pacote")](changes-to-storekit-images/image14.png#lightbox)

Faça logon com sua credenciais de conexão do iTunes:

 [![](changes-to-storekit-images/image15.png "Faça logon com sua credenciais de conexão do iTunes")](changes-to-storekit-images/image15.png#lightbox)

Escolha o aplicativo correto e compras no aplicativo para associar esse conteúdo com:

 [![](changes-to-storekit-images/image16.png "Escolha o aplicativo correto e compras no aplicativo para associar esse conteúdo com")](changes-to-storekit-images/image16.png#lightbox)

Você verá uma mensagem como esta:

 ![](changes-to-storekit-images/image17.png "Um exemplo nenhuma mensagem de problemas")

Agora, passar por um processo semelhante, mas clicar **distribuir...** Na verdade, carregará o conteúdo.

 [![](changes-to-storekit-images/image18.png "Distribuir o aplicativo")](changes-to-storekit-images/image18.png#lightbox)

Selecione a primeira opção, para carregar o conteúdo:

 ![](changes-to-storekit-images/image19.png "Carregar o conteúdo")

Faça logon novamente:

 [![](changes-to-storekit-images/image15.png "Logon no")](changes-to-storekit-images/image15.png#lightbox)

Escolha o aplicativo correto e o registro de compra no aplicativo para carregar o conteúdo a:

 [![](changes-to-storekit-images/image20.png "Escolha o registro de compra no aplicativo e de aplicativos")](changes-to-storekit-images/image20.png#lightbox)

Aguarde enquanto os arquivos são carregados:

 [![](changes-to-storekit-images/image21.png "A caixa de diálogo de carregamento de conteúdo")](changes-to-storekit-images/image21.png#lightbox)

Quando o carregamento for concluído, será exibida uma mensagem para avisá-lo de que o conteúdo foi enviado para a loja de aplicativos.

 [![](changes-to-storekit-images/image22.png "Um exemplo de mensagem de upload bem-sucedido")](changes-to-storekit-images/image22.png#lightbox)

Depois que for feito, ao retornar à página do produto na iTunes conectar, ela mostrará os detalhes do pacote e estar em **pronto para enviar** status. Quando o produto está em status, você pode começar o teste no ambiente de área restrita. NÃO é necessário para o produto para a área restrita de teste 'Enviar'.

 [![](changes-to-storekit-images/image23.png "conectar-se ela mostrará os detalhes do pacote e estar pronto para enviar o status do iTunes")](changes-to-storekit-images/image23.png#lightbox)

Isso pode demorar um pouco (ex. alguns minutos) entre a carregar o arquivo e o status de conexão que está sendo atualizado do iTunes. Você pode enviar o produto para revisão separadamente ou enviá-lo em conjunto com um binário do aplicativo. Depois de Apple oficialmente aprovou o conteúdo ele estará disponível na produção App Store para compra em seu aplicativo.

### <a name="pkg-file-format"></a>Formato de arquivo de pacote

Usando a ferramenta de arquivamento e Xcode para criar e carregar um pacote de conteúdo hospedado significa que você nunca verá o conteúdo do próprio pacote. Os arquivos e diretórios em que os pacotes criados para o aplicativo de exemplo se parecer com isso, com o `plist` arquivo na raiz e os arquivos de produto em um `Contents` subdiretório:

 [![](changes-to-storekit-images/image24.png "O arquivo plist na raiz e os arquivos de produto em um subdiretório de conteúdo")](changes-to-storekit-images/image24.png#lightbox)

Observe a estrutura do diretório do pacote (especialmente o local dos arquivos de `Contents` subdiretório) porque você precisará entender essas informações para extrair os arquivos do pacote no dispositivo.

### <a name="updating-package-content"></a>Atualizando o conteúdo do pacote

O procedimento para atualizar o conteúdo depois que ele tiver sido aprovado:

-  Edite o conteúdo do aplicativo na compra projeto no Xcode.
-  Aumentar o número de versão.
-  Carregar novamente para conectar-se de iTunes. Compradores subsequentes receberá automaticamente a versão mais recente, mas os usuários que já têm a versão antiga não receberá nenhuma notificação. 
-  Seu aplicativo é responsável por notificar os usuários e incentivando para recuperar uma versão mais recente do conteúdo. O aplicativo também deve criar uma função que baixa a nova versão. Isso deve ser feito usando o recurso de restauração do Kit de armazenamento. 
-  Para determinar se existe uma versão mais recente você pode criar um recurso em seu aplicativo para buscar SKProducts (por exemplo. mesmo processo que é usado para recuperar os preços dos produtos) e a propriedade ContentVersion de comparar. 

## <a name="purchasing-overview"></a>Visão geral de compras

Antes de ler esta seção, examine existente [documentação de compra no aplicativo](~/ios/platform/in-app-purchasing/index.md).

A sequência de eventos que ocorre quando um produto com conteúdo hospedado é adquirida e download é ilustrado neste diagrama:

 [![](changes-to-storekit-images/image25.png "A sequência de eventos que ocorre quando um produto com conteúdo hospedado é adquirida e baixar")](changes-to-storekit-images/image25.png#lightbox)

1.  Novos produtos podem ser criados na iTunes conectar-se com o host conteúdo habilitado. O conteúdo real é construído separadamente no Xcode (como arrastar simplesmente como arquivos em uma pasta) e arquivado, em seguida e carregado iTunes (codificação não é necessário). Cada produto, em seguida, é enviado para aprovação, após o qual ele é disponibilizado para compra. No código de exemplo essas IDs de produto são embutidos em código, mas hospedar conteúdo com a Apple é mais flexível, se você armazenar a lista de produtos disponíveis em um servidor remoto para que ele pode ser atualizado quando você enviar novos produtos e conteúdo para conectar-se de iTunes. 
1.  Quando o usuário adquire um produto, uma transação é colocada na fila de pagamento para processamento. 
1.  Kit de repositório encaminha a solicitação de compra para servidores iTunes para processamento. 
1.  Transação é concluída em servidores do iTunes (por exemplo. cliente é cobrado) e uma confirmação é retornada para o aplicativo, com informações de produto anexadas incluindo se ele é baixado (e nesse caso, o tamanho do arquivo e outros metadados). 
1.  Seu código deve verificar se o produto é downloadble e nesse caso fazer uma solicitação de download de conteúdo que também é colocada na fila de pagamento. Kit de repositório envia essa solicitação para os servidores do iTunes. 
1.  Servidor retorna o arquivo de conteúdo para Kit de armazenamento, que fornece um retorno de chamada para retornar o andamento do download e o tempo restante estimativas ao seu código. 
1.  Uma vez concluído, você obter notificado e passado a um local de arquivo na pasta de Cache. 
1.  Seu código deve copiar os arquivos e verificá-los, salvar qualquer estado que você precisa se lembrar que o produto foi adquirido. Aproveitar essa oportunidade para definir o sinalizador de backup corretamente nos novos arquivos (Dica: se eles vêm de um servidor e nunca são editados pelo usuário, você deve provavelmente ignorar fazer o backup, pois o usuário pode sempre recuperá-las de servidores da Apple no futuro). 
1.  Chame FinishTransaction. Isso é importante, pois ele remove a transação da fila de pagamento. Também é importante que você não chama FinishTransaction até depois que você copiou o conteúdo fora do diretório do Cache. Depois de você chamar FinishTransaction, os arquivos em cache são provavelmente rapidamente ser limpos. 


## <a name="implementing-hosted-content-purchase"></a>Implementando a compra de conteúdo hospedada

As informações a seguir devem ser lida em conjunto com o completo [documentação de compras no aplicativo](~/ios/platform/in-app-purchasing/index.md). As informações neste documento aborda as diferenças entre o conteúdo hospedado e a implementação anterior.


### <a name="classes"></a>Classes

As classes a seguir foram adicionadas ou alteradas para conteúdo de suporte hospedado no iOS 6:

-   **SKDownload** – nova classe que representa um download em andamento. A API permite mais de uma por produto, porém inicialmente apenas um foi implementado. 
-   **SKProduct** – novas propriedades adicionadas: `Downloadable` , `ContentVersion` , `ContentLengths` matriz. 
-   **SKPaymentTransaction** – nova propriedade adicionada: `Downloads` , que contém uma coleção de `SKDownload` objetos se este produto tem hospedado conteúdo disponível para download. 
-   **SKPaymentQueue** – novo método adicionado: `StartDownloads` . Chame este método com `SKDownload` objetos para buscar o conteúdo hospedado. Baixando pode ocorrer em segundo plano. 
-   **SKPaymentTransactionObserver** – novo método: `UpdateDownloads` . Kit de repositório chama esse método com informações de progresso sobre operações atuais de download. 


Detalhes do novo `SKDownload` classe:

-   **Andamento** – um valor entre 0-1 que você pode usar para exibir um indicador de porcentagem concluída para o usuário. Não use andamento = = 1 para detectar se o download for concluído, verificar estado = = concluído. 
-   **TimeRemaining** – estimativa do download tempo restante, em segundos. -1 significa que ele ainda está calculando estimativa. 
-   **Estado** – ativo, aguardando, concluído, falha, pausado, cancelada. 
-   **ContentURL** – local onde o conteúdo foi colocado no disco, no arquivo de `Cache` directory. Será populado somente após a conclusão do download. 
-   **Erro** – Verifique se a propriedade se o estado de falha. 


As interações entre as classes no código de exemplo são mostradas no diagrama (o código específico para compras de conteúdo hospedadas é mostrado em verde):

 [![](changes-to-storekit-images/image26.png "Compras de conteúdo hospedadas é mostrado em verde nesse diagrama")](changes-to-storekit-images/image26.png#lightbox)

O código de exemplo em que essas classes foram usadas é mostrado no restante desta seção:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Alterar existente `UpdatedTransactions` substituição para verificar se há conteúdo disponível para download e chame `StartDownloads` se necessário:

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

Novo método substituído `UpdatedDownloads` é mostrado abaixo. Kit de repositório chama esse método após `StartDownloads` é acionado em `UpdatedTransactions`. Este método é chamado *várias vezes* intervalos indeterminado para fornecer a você com o progresso do download e, em seguida, novamente quando o download foi concluída. Observe o método aceita uma matriz de `SKDownload` objetos, para cada chamada de método possa fornecer o status de vários downloads na fila. Conforme mostrado na implementação abaixo que os status do download são verificadas cada hora e a ação apropriada.

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
            Console.WriteLine ("Cancelled"); // TODO: UI?
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

Essa classe contém um novo método `SaveDownload` que é chamado após cada download for concluído com êxito.

O conteúdo hospedado foi baixado com êxito e descompactado para o `Cache` directory. A estrutura da. Arquivo de pacote requer que todos os arquivos sejam salvos em um `Contents` subdiretório, portanto o código a seguir extrai os arquivos de dentro do `Contents` subdiretório.

O código itera em todos os arquivos no pacote de conteúdo e os copia para o `Documents` diretório em uma subpasta chamada para o `ProductIdentifier`. Finalmente, ele chama `CompleteTransaction`, que chama `FinishTransaction` para remover a transação de fila de pagamento.

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

Quando `FinishTransaction` é chamado baixado arquivos não têm garantia do `Cache` directory. Todos os arquivos devem ser copiados antes de chamar `FinishTransaction`.


## <a name="other-considerations"></a>Outras considerações

O código do exemplo anterior demonstra uma implementação bem simple de adquirir conteúdo hospedado. Há alguns aspectos que você deve considerar:

### <a name="detecting-updated-content"></a>Detectar conteúdo atualizado

Embora seja possível atualizar os pacotes de conteúdo hospedados, Kit de repositório não fornece qualquer mecanismo para enviar essas atualizações para os usuários que já tenham baixado e adquiriu o produto. Para implementar essa funcionalidade, seu código pode verificar o novo `SKProduct.ContentVersion` propriedade (se o `SKProduct` é `Downloadable`) regularmente e detectar se o valor é incrementado. Como alternativa, você pode criar um sistema de notificação por push.

### <a name="installing-updated-content-versions"></a>Instalando versões atualizadas de conteúdo

O exemplo de código acima ignora a cópia do arquivo se o arquivo já existe. Isso não é uma boa ideia, se você deseja dar suporte a versões mais recentes do conteúdo que está sendo baixado.

Pode ser uma alternativa para copiar o conteúdo em uma pasta chamada para a versão e manter o controle de qual é a versão atual (por exemplo. em `NSUserDefaults` ou onde quer que você armazena os registros de compra completa).

### <a name="restoring-transactions"></a>Restauração de transações

Quando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` é chamado, o Kit de armazenamento retorna todas as transações anteriores para o usuário. Se eles compraram um grande número de itens, ou se cada compra tiver pacotes de conteúdo muito grande, a restauração pode resultar em uma grande quantidade de tráfego de rede como tudo o que obtém enfileirado para download de uma vez.

Considere a possibilidade de controlar se um produto foi adquirido separadamente do download do pacote de conteúdo associado.

### <a name="pausing-restarting-and-canceling-downloads"></a>Pausar, reiniciar e Cancelar Downloads

Embora o código de exemplo não demonstra esse recurso, é possível pausar e reiniciar os downloads de conteúdo hospedados. O `SKPaymentQueue.DefaultQueue` tem métodos para `PauseDownloads`, `ResumeDownloads` e `CancelDownloads`.

Se o código chama `FinishTransaction` na fila de pagamento antes do download está sendo `Finished` , em seguida, esse download é cancelada automaticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>A definição do sinalizador de Backup ignorar o conteúdo baixado

Diretrizes de Backup no iCloud da Apple sugerem que o conteúdo não-usuário que é facilmente restaurado de um servidor deve *não* ser submetidos a backup (porque ele desnecessariamente usaria o armazenamento no iCloud). Consulte o [trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md) documentação para obter mais detalhes sobre como definir o atributo de backup.

## <a name="summary"></a>Resumo

Este artigo apresentou dois novos recursos do Kit de repositório em iOS6: compra iTunes e outros conteúdos de dentro de seu aplicativo e da utilização de servidor da Apple para hospedar seus próprio compras no aplicativo. Esta introdução deve ser lida em conjunto com a existente [documentação de compra no aplicativo](~/ios/platform/in-app-purchasing/index.md) para cobertura completa de implementar a funcionalidade do Kit de armazenamento.

## <a name="related-links"></a>Links relacionados

- [StoreKit (exemplo)](https://developer.xamarin.com/samples/StoreKit/)
- [Compras no aplicativo](~/ios/platform/in-app-purchasing/index.md)
- [Referência StoreKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Referência de classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [Referência de API de pesquisa do iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC vídeo: Vendendo produtos com o Kit de armazenamento](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
