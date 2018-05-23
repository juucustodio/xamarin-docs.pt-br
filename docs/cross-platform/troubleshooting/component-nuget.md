---
title: Atualizar referências de componentes para NuGet
description: Substitua as referências de componente com pacotes do NuGet para obsoleta seus aplicativos.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: e9c056d37577a280b66bb3aa7ded19e966bca43b
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2018
---
# <a name="updating-component-references-to-nuget"></a>Atualizar referências de componentes para NuGet

> [!IMPORTANT]
> O armazenamento de componentes foi descontinuado a partir de 15 de maio de 2018 (esse feriado foi originalmente [anunciada](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) em novembro de 2017).
>
> Xamarin componentes não são mais suportados no Visual Studio e deve ser substituídos por pacotes do NuGet. Siga as instruções abaixo para remover manualmente as referências de componentes de seus projetos.

Consulte as instruções para adicionar pacotes do NuGet em [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) ou [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Uma lista de popular Xamarin [plug-ins e bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md) está disponível para ajudar a encontrar alternativas para componentes que não estão disponíveis como pacotes do NuGet.

## <a name="manually-removing-component-references"></a>Remover manualmente as referências de componentes

A versão 15.6 do Visual Studio e a versão 7.4 do Visual Studio para Mac não dão suporte ao protocolo componentes em seu projeto. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se você carregar um projeto no Visual Studio, a seguinte caixa de diálogo será exibida, explicando que você deve remover todos os componentes do seu projeto manualmente:

![Caixa de diálogo explicando que um componente foi encontrado no seu projeto e deve ser removido de alerta](component-nuget-images/component-alert-vs.png)

Para remover um componente do seu projeto:

1. Abra o **. csproj** arquivo. Para fazer isso, clique no nome do projeto e selecione **descarregar projeto**. 

2. Clique novamente no projeto descarregado e selecione **Editar {your-projeto-name} csproj**.

3. Localizar todas as referências no arquivo `XamarinComponentReference`. Ela deve ser semelhante ao exemplo a seguir:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Remova as referências a `XamarinComponentReference` e salve o arquivo. No exemplo acima, é seguro remover todo o `ItemGroup`.

5. Depois que o arquivo foi salvo, clique no nome do projeto e selecione **recarregar projeto**.

6. Repita as etapas acima para cada projeto na solução.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Se você carregar um projeto no Visual Studio para Mac, a seguinte caixa de diálogo será exibida, explicando que você deve remover todos os componentes do seu projeto manualmente:

![Caixa de diálogo explicando que um componente foi encontrado no seu projeto e deve ser removido de alerta](component-nuget-images/component-alert.png)

Para remover um componente do seu projeto:

1. Abra o arquivo. csproj. Para fazer isso, clique no nome do projeto e selecione **Ferramentas > Editar arquivo**.

2. Localizar todas as referências no arquivo `XamarinComponentReference`. Ela deve ser semelhante ao exemplo a seguir:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Remova as referências a `XamarinComponentReference` e salve o arquivo. No exemplo acima, é seguro remover todo o `ItemGroup`

4. Repita as etapas acima para cada projeto na solução.

-----

> [!WARNING]
> As instruções a seguir só funcionam com versões anteriores do Visual Studio.
> O **componentes** nó não está mais disponível nas versões atuais de 2017 do Visual Studio ou Visual Studio para Mac.

As seções a seguir explicam como atualizar as soluções existentes de Xamarin para alterar referências de componentes para pacotes do NuGet.

- [Componentes que contêm pacotes do NuGet](#contain)
- [Componentes com substituições do NuGet](#replace)

A maioria dos componentes se enquadram em uma das categorias acima.
Se você estiver usando um componente que não parece ter um pacote do NuGet equivalente, leia o [componentes sem um caminho de migração do NuGet](#require-update) seção abaixo.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componentes que contêm pacotes do NuGet

Muitos componentes já contêm pacotes NuGet e o caminho de migração é simplesmente exclua a referência do componente.

Você pode determinar se o componente já inclui um pacote NuGet clicando duas vezes no componente da solução:

![Nó de componentes expandido](component-nuget-images/solution-sml.png)

O **pacotes** guia listará todos os pacotes NuGet incluídos no componente:

![Guia de pacotes contém NuGet](component-nuget-images/packages-tab-sml.png)

Observe que o **Assemblies** guia estará vazia:

![Guia de assemblies está vazio](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Atualizando a solução

Para atualizar sua solução, exclua o **componente** entrada da solução:

![Excluir componente](component-nuget-images/delete-component-sml.png)

O pacote NuGet permanecerá listado no **pacotes** nó e seu aplicativo irá compilar e executar como de costume. No futuro, esse pacote será atualizado por meio de **Nuget** recurso de atualização:

![Pacote de atualização do NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componentes com substituições do NuGet

Se a página de informações do componente **Assemblies** guia contém as entradas, conforme mostrado abaixo, você precisará localizar o pacote NuGet equivalente manualmente.

![Contém os assemblies](component-nuget-images/assemblies-tab-sml.png)

Observe que o **pacotes** guia provavelmente estará vazia:

![](component-nuget-images/packages-tab-empty-sml.png)

_Pode conter dependências do NuGet, mas eles podem ser ignorados._

Para confirmar uma substituição existe pacote do NuGet, pesquise [NuGet.org](https://www.nuget.org/packages), usando o nome do componente, ou pelo autor.

Por exemplo, você pode encontrar o popular **sqlite-net-pcl** pacote procurando por:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – o nome do produto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – perfil do autor.

### <a name="updating-the-solution"></a>Atualizando a solução

Depois de confirmar que o componente está disponível no NuGet, siga estas etapas:

#### <a name="delete-the-component"></a>Exclua o componente

Clique com o botão direito no componente na solução e escolha **remover**:

![Remover componente](component-nuget-images/remove-component-sml.png)

Isso excluirá o componente e todas as referências. Isso interromperá a compilação, até que você adicione o pacote NuGet equivalente para substituí-lo.

#### <a name="add-the-nuget-package"></a>Adicionar o pacote NuGet

1. Clique com botão direito no **pacotes** nó e escolha **adicionar pacotes de...** .
2. Procure a substituição do NuGet por nome ou o autor:

  ![](component-nuget-images/nuget-search-sml.png)

3. Pressione **Adicionar pacote**.

O pacote NuGet será adicionado ao seu projeto, juntamente com quaisquer dependências.
Isso deve corrigir a compilação. Se a compilação continuar falhando, investigue cada erro para ver se não houver diferenças de API entre o componente e o pacote NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componentes sem um caminho de migração do NuGet

Não se preocupe se você não encontrar imediatamente uma substituição para os componentes usados em seu aplicativo. Os componentes existentes continuarão a funcionar em 15,5 do Visual Studio e o **componentes** nó aparecerá em sua solução como de costume.

Versões futuras do Visual Studio, no entanto, serão _não_ restaurar ou atualizar os componentes.
Isso significa que se você abrir a solução em um novo computador, o componente não ser baixado e instalado; e o autor não poderá fornecer as atualizações. Você deve planejar a:

* Extraia os assemblies do componente e referenciá-las diretamente em seu projeto.
* Entre em contato com o autor do componente e perguntar sobre os planos para migrar para o NuGet.
* Investigue os pacotes do NuGet alternativos ou procure o código-fonte se o componente é o código-fonte aberto.

Muitos fornecedores de componente ainda estão trabalhando na migração para o NuGet, e outros usuários (incluindo produtos comercialmente disponíveis) podem investigar opções de entrega alternativo.


## <a name="related-links"></a>Links relacionados
- [Lista de bibliotecas e Xamarin Plugins populares](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Instalar e usar um pacote do NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Incluindo um pacote do NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
